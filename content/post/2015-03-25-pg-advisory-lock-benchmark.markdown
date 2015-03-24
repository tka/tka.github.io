+++
date = "2015-03-25T02:53:09+08:00"
draft = false
title = "Postgresql Advisory Lock 測試"

+++

前兩天聊天的時候聊到 Postgresql Advisory Lock 

功能的介紹可以參考

* http://www.postgresql.org/docs/9.4/static/explicit-locking.html#ADVISORY-LOCKS
* http://hashrocket.com/blog/posts/advisory-locks-in-postgres

這邊直接看簡單的測試結果

pgbench 初始化參數是 `pgbench -Upostgres -i -s 10`

先看單純 select for update 的測試script
    
    BEGIN;
        update pgbench_accounts set abalance = 1 where aid = (select aid from pgbench_accounts where abalance = 0 limit 1 for update );
        \sleep 1 ms
    END;

這邊測試裡面加了 sleep 1 ms 的原因是為了讓測試結果比較像是真實的使用情境, 在 update 完資料到 commit 之間可能還會做些事情, 這邊就假設多用了 1ms 吧

結果是

    tarting vacuum...end.
    transaction type: Custom query
    scaling factor: 1
    query mode: simple
    number of clients: 200
    number of threads: 1
    number of transactions per client: 100
    number of transactions actually processed: 20000/20000
    latency average: 0.000 ms
    tps = 823.817249 (including connections establishing)
    tps = 829.889465 (excluding connections establishing)
    statement latencies in milliseconds:
            0.105646        BEGIN;
            122.298852          update pgbench_accounts set abalance = 1 where aid = (select aid from pgbench_accounts where abalance = 0 limit 1 for update );
            1.083191            \sleep 1 ms
            0.072635        END;

再看用了 advisory lock 的測試script

    BEGIN;
       update pgbench_accounts set abalance = 1 where aid = (select  aid  from pgbench_accounts where abalance = 0 and  pg_try_advisory_xact_lock(aid) limit 1 for update);
       \sleep 1 ms
    END;

結果是

    starting vacuum...end.
    transaction type: Custom query
    scaling factor: 1
    query mode: simple
    number of clients: 200
    number of threads: 1
    number of transactions per client: 100
    number of transactions actually processed: 20000/20000
    latency average: 0.000 ms
    tps = 7411.739157 (including connections establishing)
    tps = 7914.144198 (excluding connections establishing)
    statement latencies in milliseconds:
            0.940393        BEGIN;
            19.822141           update pgbench_accounts set abalance = 1 where aid = (select  aid  from pgbench_accounts where abalance = 0 and  pg_try_advisory_xact_lock(aid) limit 1 for update);
            1.283840            \sleep 1 ms
            1.779052        END;


比較一下兩邊 tps 的結果 823 vs 7411 

也就是說用了 advisory lock 後 tps 大概快了 8 倍
