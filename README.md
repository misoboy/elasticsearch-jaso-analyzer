# elasticsearch-jaso-analyzer

> Korean Jaso (자소) analyzer plugin for **Elasticsearch 7.x** — enables Korean autocomplete by decomposing Hangul into consonants and vowels (자음/모음).

[![Elasticsearch](https://img.shields.io/badge/Elasticsearch-7.14.1-005571?logo=elasticsearch&logoColor=white)](https://www.elastic.co)
[![Java](https://img.shields.io/badge/Java-11+-orange?logo=java&logoColor=white)](https://java.com)
[![Gradle](https://img.shields.io/badge/Gradle-build-02303A?logo=gradle&logoColor=white)](https://gradle.org)

## Overview

한국어 자소 분석기 플러그인입니다. 한글을 자음/모음 단위로 분해하여 초성 검색, 자소 기반 자동완성 등을 구현할 수 있습니다.

**예시:** `"삼성"` → `ㅅ ㅏ ㅁ ㅅ ㅓ ㅇ` 단위로 분해 → 초성 `"ㅅㅅ"` 으로도 검색 가능

## Installation

```bash
# Build plugin zip
gradle build buildPluginZip

# Install to Elasticsearch
bin/elasticsearch-plugin install \
  https://github.com/misoboy/elasticsearch-jaso-analyzer/releases/download/7.14.1/jaso-analyzer-plugin-7.14.1-plugin.zip

# Remove plugin
bin/elasticsearch-plugin remove jaso-analyzer
```

## Index Setup

```bash
# Create index with jaso analyzer
curl -XPUT -H 'Content-Type: application/json' localhost:9200/jaso/ -d '
{
  "settings": {
    "analysis": {
      "analyzer": {
        "jaso_search_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["jaso_filter"]
        }
      },
      "filter": {
        "jaso_filter": { "type": "jaso_filter" }
      }
    }
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "jaso_search_analyzer"
      }
    }
  }
}'

# Delete index (if needed)
curl -XDELETE 'http://localhost:9200/jaso'
```

## Autocomplete Search Example

```bash
# Index a document
curl -XPOST 'http://localhost:9200/jaso/_doc' \
  -H 'Content-Type: application/json' \
  -d '{"name": "삼성전자"}'

# Search with partial input (e.g., "ㅅ" matches "삼성전자")
curl -XGET 'http://localhost:9200/jaso/_search' \
  -H 'Content-Type: application/json' \
  -d '{"query": {"match": {"name": "ㅅ"}}}'
```

## Compatibility

| Plugin Version | Elasticsearch |
|---------------|---------------|
| 7.14.1 | 7.14.1 |

## License

Apache 2.0
