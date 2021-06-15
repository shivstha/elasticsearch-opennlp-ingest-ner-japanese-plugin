# Elasticsearch OpenNLP Ingest Processor for Japanese Language

This elasticsearch ingest plugin uses rondhuit model developed by [RONDHUIT Co, Ltd.](https://www.rondhuit.com/) for Named Entity Recognition of Japanese Language. This plugin is also attached with [Kuromoji Analyzer](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-kuromoji-analyzer.html) the built-in plugin of elasticsearch for tokenizing Japanese words.

This rondhuit model supports entity recoginition like **ACCESS, LANGUAGE, PERSON, ORGANIZATION, LOCATION, DATETIME, EVENT, TITLE** and stores the output in the JSON before it is being stored.


## Installation


**IMPORTANT**: This plugin is tested only in Elasticversion 7.13.0 . Make sure to install Elastiversion 7.13.0 if it doesnot install in your elasticsearch envrionment. 
| ES    | Command |
| ----- | ------- |
| 7.13.0 | `bin/elasticsearch-plugin install https://github.com/shivstha/elasticsearch-opennlp-ingest-ner-japanese-plugin/releases/download/7.13.0/ingest-opennlp-7.13.0.1-SNAPSHOT.zip` |


To download the models, run the following under Linux and osx (this is in
the `bin` directory of your Elasticsearch installation)

```
bin/ingest-opennlp/download-models
```

If you are using windows, please use the following command

```
bin\ingest-opennlp\download-models.bat
```


## Usage

This is how you configure a pipeline with support for opennlp

You can add the following lines to the `config/elasticsearch.yml` (as those models are shipped by default, they are easy to enable). The models are looked up in the `config/ingest-opennlp/` directory.

```
ingest.opennlp.model.file.japanese: rondhuit-ja-ner-model.bin
```

Now fire up Elasticsearch and configure a pipeline

```
PUT _ingest/pipeline/opennlp-pipeline
{
  "description": "A pipeline to do named entity extraction",
  "processors": [
    {
      "opennlp" : {
        "field" : "description"
      }
    }
  ]
}


PUT /my-index/_doc/1?pipeline=opennlp-pipeline
{
  "description" : "市名  は  この 地  に 先住し た インディアン  部族  で ある  スクア  ミシュ  族  の シアトル  酋長  の 名  に 因ん で  いる  。  スクア  ミシュ  族  は  １９  世紀  に アメリカ 連邦  政府  に よっ て  保留  地  へ 強制  移住  さ  せ られ 、  彼 ら  の 土地  に シアトル  が 建設  さ  れ た 。  強制  移住  を 受け入れ させ られ た 部族  が この 地  を 離れる 際  の 、  シアトル 酋長  の 演説  は  非常  に 有 [J-c] | 名  で ある  。  そして 「  グレート  ・  ノーザン 鉄道  を 父  と し  、  日本 郵船  を 母  と する  。  」  の 言葉  で 有名  な よう  に 、  この 両社  に よっ て  東洋  貿易  の 中継  地点  と し  て  発展  を 遂げ た 。"
}


# response will contain an entities field with organization, datetime and location
GET /my-index/_doc/1
```

## Output in Kibana
[!Output of ingest plugin on japanese text](https://github.com/shivstha/elasticsearch-opennlp-ingest-ner-japanese-plugin/blob/ed16b43bae78289b18788c0d2645663fa6a03810/model/japanese-ner-results.png)
