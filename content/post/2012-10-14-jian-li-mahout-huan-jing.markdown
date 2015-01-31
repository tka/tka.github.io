---
layout: post
title: "Mahout 搭配 JRuby 建立環境與簡單測試"
date: "2012-10-14T11:22:00+08:00"
comments: true
categories: 
---

# 建立環境

1. 下載&安裝 Mahout
    * https://cwiki.apache.org/confluence/display/MAHOUT/BuildingMahout 
1. 下載&安裝 Hadoop
    * http://www.apache.org/dyn/closer.cgi/hadoop/common/
    * 單機安裝步驟 http://hadoop.apache.org/common/docs/stable/single_node_setup.html
1. 設定 ClASSPATH
```
 export CLASSPATH=.:~/Project/Mahout/mahout-distribution-0.7/mahout-core-0.7.jar:~/Project/Mahout/mahout-distribution-0.7/mahout-math-0.7.jar:~/Project/Mahout/mahout-distribution-0.7/lib/'*'
```

# 簡單測試

可以從 <https://github.com/tdunning/MiA> 下載 Mahout in Action 的範例程式, 這邊選擇第2章的 [RecommenderIntro.java](https://github.com/tdunning/MiA/blob/mahout-0.7/src/main/java/mia/recommender/ch02/RecommenderIntro.java) 來將他改寫為 JRuby 的版本, 改寫的結果如下


``` ruby
require 'java'

module Mahout
  include_package "org.apache.mahout.cf.taste.impl.model.file";
  include_package "org.apache.mahout.cf.taste.impl.neighborhood";
  include_package "org.apache.mahout.cf.taste.impl.recommender";
  include_package "org.apache.mahout.cf.taste.impl.similarity";
  include_package "org.apache.mahout.cf.taste.model";
  include_package "org.apache.mahout.cf.taste.neighborhood";
  include_package "org.apache.mahout.cf.taste.recommender";
  include_package "org.apache.mahout.cf.taste.similarity";

end

model = Mahout::FileDataModel.new( java.io.File.new("intro.csv"));

similarity = Mahout::PearsonCorrelationSimilarity.new(model);
neighborhood = Mahout::NearestNUserNeighborhood.new(2, similarity, model);

recommender = Mahout::GenericUserBasedRecommender.new( model, neighborhood, similarity);

recommendation =recommender.recommend(1, 1);

puts recommendation

```
