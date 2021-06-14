# Elasticsearch OpenNLP Ingest Processor for Japanese Language

This elasticsearch ingest plugin uses rondhuit model developed by [RONDHUIT Co, Ltd.](https://www.rondhuit.com/) for Named Entity Recognition of Japanese Language. This plugin is also attached with [Kuromoji Analyzer](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-kuromoji-analyzer.html) the built-in plugin of elasticsearch for tokenizing Japenese words.

This rondhuit model supports entity recoginition like ACCESS,LANGUAGE,PERSON,ORGANIZATION,LOCATION,DATETIME,EVENT,TITLE and stores the output in the JSON before it is being stored.


## Installation


**IMPORTANT**: This plugin is tested only in Elasticversion 7.13.0 . Make sure to install Elastiversion 7.13.0 if it doesnot install in your elasticsearch envrionment. 

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
ingest.opennlp.model.file.persons: en-ner-persons.bin
ingest.opennlp.model.file.dates: en-ner-dates.bin
ingest.opennlp.model.file.locations: en-ner-locations.bin
```

Now fire up Elasticsearch and configure a pipeline

```
PUT _ingest/pipeline/opennlp-pipeline
{
  "description": "A pipeline to do named entity extraction",
  "processors": [
    {
      "opennlp" : {
        "field" : "my_field"
      }
    }
  ]
}

PUT /my-index/_doc/1?pipeline=opennlp-pipeline
{
  "my_field" : "Kobe Bryant was one of the best basketball players of all times. Not even Michael Jordan has ever scored 81 points in one game. Munich is really an awesome city, but New York is as well. Yesterday has been the hottest day of the year."
}

# response will contain an entities field with locations, dates and persons
GET /my-index/_doc/1
```

You can also specify only certain named entities in the processor, i.e. if you only want to extract persons


```
PUT _ingest/pipeline/opennlp-pipeline
{
  "description": "A pipeline to do named entity extraction",
  "processors": [
    {
      "opennlp" : {
        "field" : "my_field"
        "fields" : [ "persons" ]
      }
    }
  ]
}
```

You can also emit text in the format used by the [annotated text plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/current/mapper-annotated-text.html).

```
PUT _ingest/pipeline/opennlp-pipeline
{
  "description": "A pipeline to do named entity extraction",
  "processors": [
    {
      "opennlp" : {
        "field" : "my_field",
        "annotated_text_field" : "my_annotated_text_field"
      }
    }
  ]
}
```

**Note: The creation of annotated text field syntax is only supported when running on Elasticsearch 7.0.1 onwards**


## Configuration

You can configure own models per field, the setting for this is prefixed `ingest.opennlp.model.file.`. So you can configure any model with any field name, by specifying a name and a path to file, like the three examples below:

| Parameter | Use |
| --- | --- |
| ingest.opennlp.model.file.names    | Configure the file for named entity recognition for the field name        |
| ingest.opennlp.model.file.dates    | Configure the file for date entity recognition for the field date         |
| ingest.opennlp.model.file.persons  | Configure the file for person entity recognition for the field person     |
| ingest.opennlp.model.file.WHATEVER | Configure the file for WHATEVER entity recognition for the field WHATEVER |

## Development setup & running tests

In order to install this plugin, you need to create a zip distribution first by running

```bash
./gradlew clean check
```

This will produce a zip file in `build/distributions`. As part of the build, the models are packaged into the zip file, but need to be downloaded before. There is a special task in the `build.gradle` which is downloading the models, in case they dont exist.

After building the zip file, you can install it like this

```bash
bin/plugin install file:///path/to/elasticsearch-ingest-opennlp/build/distribution/ingest-opennlp-X.Y.Z-SNAPSHOT.zip
```

Ensure that you have the models downloaded, before testing.

## Bugs & TODO

* A couple of groovy build mechanisms from core are disabled. See the `build.gradle` for further explanations
* Only the most basic NLP functions are exposed, please fork and add your own code to this!

"# refactored-elasticsearch-opennlp-plugin" 
