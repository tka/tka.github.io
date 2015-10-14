+++
date = "2015-10-14T23:22:22+08:00"
draft = true
title = "PostgreSQL 中利用 trigger 來維護 nested set 結構"

+++

Ref.

1. http://stackoverflow.com/questions/4048151/what-are-the-options-for-storing-hierarchical-data-in-a-relational-database
1. https://sites.google.com/site/postgresqlnestedsetstriggers/

調整出來比較順手的版本, 只需要設定 node 的 left 就會調整好資料結構

```
CREATE OR REPLACE FUNCTION items_nested_set_handler() RETURNS TRIGGER AS $$
DECLARE
  set_size INTEGER;
  set_offset INTEGER;
  mid_lft INTEGER;
  mid_rgt INTEGER;
BEGIN

  --------------------------INSERT---------------
  IF (TG_OP = 'INSERT') THEN
    UPDATE items SET
      lft = CASE WHEN lft >=  NEW.lft THEN lft+2 ELSE lft END,
      rgt = CASE WHEN rgt >= NEW.lft THEN rgt+2 ELSE rgt END
    WHERE foldr_id = NEW.foldr_id;

    UPDATE items SET lft = NEW.lft, rgt = (NEW.lft+1) WHERE id=NEW.id;


  --------------------------DELETE---------------
  ELSIF (TG_OP = 'DELETE') THEN
    DELETE FROM items WHERE lft BETWEEN OLD.lft AND OLD.rgt AND foldr_id = OLD.foldr_id;

    UPDATE items SET
      lft = CASE WHEN lft > OLD.lft THEN lft - (OLD.rgt - OLD.lft + 1) ELSE lft END,
      rgt = CASE WHEN rgt > OLD.lft THEN rgt - (OLD.rgt - OLD.lft + 1) ELSE rgt END
    WHERE foldr_id = OLD.foldr_id;

  -------------------------UPDATE----------------
  ELSIF (TG_OP = 'UPDATE') THEN
    IF (OLD.lft != NEW.lft) THEN
      set_size := OLD.rgt - OLD.lft + 1;
      mid_lft := CASE WHEN NEW.lft > OLD.lft THEN OLD.lft ELSE OLD.lft + set_size END;
      mid_rgt := CASE WHEN NEW.lft > OLD.lft THEN OLD.rgt ELSE OLD.rgt + set_size END;
      set_offset := NEW.lft - mid_lft ;

      UPDATE items SET lft = OLD.lft WHERE id = OLD.id; -- recovery item lft

      UPDATE items SET
        lft = CASE WHEN lft >= NEW.lft THEN lft + set_size ELSE lft END,
        rgt = CASE WHEN rgt >= NEW.lft THEN rgt + set_size ELSE rgt END
      WHERE foldr_id = OLD.foldr_id;

      UPDATE items SET
        lft = lft + set_offset, rgt = rgt + set_offset
      WHERE lft BETWEEN mid_lft AND mid_rgt AND foldr_id = OLD.foldr_id;

      UPDATE items SET
        lft = CASE WHEN lft > mid_lft THEN lft - set_size ELSE lft END,
        rgt = CASE WHEN rgt > mid_lft THEN rgt - set_size ELSE rgt END
      WHERE foldr_id = OLD.foldr_id;

    END IF;
  END IF;

  RETURN NULL;
END;
$$ language plpgsql;
-- +goose StatementEnd

DROP TRIGGER IF EXISTS items_nested_set_trigger ON items;

CREATE TRIGGER items_nested_set_trigger
AFTER INSERT OR UPDATE OR DELETE ON items
FOR EACH ROW WHEN (pg_trigger_depth() = 0) EXECUTE PROCEDURE items_nested_set_handler();
```

