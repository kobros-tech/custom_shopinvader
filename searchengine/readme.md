# Setup Elasticsearch search engine to index products, categories, and brands

1. Install the ``connector_search_engine`` module
2. Open ``Search Engine ==> Configuration ==> Index Configurations`` view and create a new ``se.index`` record and assign fields the suitable values:
    + ``Name:`` ``product_product_config``
    + ```json
        {
            "settings": {
                "analysis": {
                    "char_filter": {
                        "replace": {
                            "type": "mapping", 
                            "mappings": ["&=> and "]
                        }
                    }, 
                    "filter": {
                        "word_delimiter": {
                            "type": "word_delimiter", 
                            "split_on_numerics": false, 
                            "split_on_case_change": true, 
                            "generate_word_parts": true, 
                            "generate_number_parts": true, 
                            "catenate_all": true, 
                            "preserve_original": true, 
                            "catenate_numbers": true
                        }
                    }, 
                    "analyzer": {
                        "default": {
                            "type": "custom", 
                            "char_filter": ["html_strip", "replace"], 
                            "tokenizer": "whitespace", 
                            "filter": ["lowercase", "word_delimiter"]
                        }
                    }, 
                    "index": {
                        "sort.field": ["main", "id"], 
                        "sort.order": ["desc", "asc"]
                    }
                }
            }, 
            "mappings": {
                "properties": {
                    "hierarchicalCategories": {
                        "type": "nested", 
                        "properties": {
                            "ancestors": {
                                "type": "keyword"
                            }, 
                            "order": {
                                "type": "integer"
                            }, 
                            "value": {
                                "type": "keyword"
                            }, 
                            "level": {
                                "type": "integer"
                            }
                        }
                    }, "url_key": {
                        "type": "keyword"
                    }, 
                    "variant_attributes": {
                        "properties": {
                            "color": {
                                "type": "text", 
                                "fielddata": true
                            }
                        }
                    }, 
                    "main": {
                        "type": "boolean"
                    }, 
                    "id": {
                        "type": "integer"
                    }, 
                    "redirect_url_key": {
                        "type": "keyword"
                    }
                }
            }
        }
        ```
3. create another ``se.index`` record and assign fields the suitable values:
    + ``Name:`` ``product_category_config``
    + ```json
        {
            "settings": {
                "index": {
                    "sort.field": ["id"], 
                    "sort.order": ["asc"]
                }
            }, 
            "mappings": {
                "properties": {
                    "url_key": {
                        "type": "keyword"
                    }, 
                    "redirect_url_key": {
                        "type": "keyword"
                    }, 
                    "id": {
                        "type": "integer"
                    }
                }
            }
        }
        ```
3. Open `` search Engine`` view and create a new ``se.backend`` record and assign fields the suitable values:
    + ``Name:`` ``Elastic External FS``
    + ``Tech Name:`` ``elastic_external_fs``
    + ``Index Prefix Name:`` ``elastic_external_fs``
    + ``Type:`` ``ElasticSearch``
    + ``Image Data Url Strategy:`` ``FS Storage URL``
    + ``Media Data Url Strategy:`` ``FS Storage URL``
    + ``Stock Level Config:`` ``Only Quantity``
    + ``Host:`` ``http://localhost:9200``
    + ``Auth Type:`` ``HTTP``
    + ``User:`` ``elastic``
    + ``Password:`` ``your_elasticsearch_password``
    + Index:
        + line:
            + ``Lang:`` ``English (US)``
            + ``Model:`` ``Product Variant``
            + ``Serializer Type:`` ``Shopinvader Product``
            + ``Config:`` ``product_product_config``
        + line:
            + ``Lang:`` ``English (US)``
            + ``Model:`` ``Product Category``
            + ``Serializer Type:`` ``Shopinvader Category``
            + ``Config:`` ``product_category_config``
    + Thumbnail Sizes:
        + line:
            + ``Search Engine Backend:`` ``Elastic External FS``
            + ``Model:`` ``Product Variant``
            + ``Images Field:`` ``Variant Images (product.product)``
            + ``Size:`` ``Shop Small (60x60), Shop Medium (300x300), Shop Large (600x600)``
        + line:
            + ``Search Engine Backend:`` ``Elastic External FS``
            + ``Model:`` ``Product Category``
            + ``Images Field:`` ``Images (product.category)``
            + ``Size:`` ``Shop Small (60x60), Shop Medium (300x300), Shop Large (600x600)``
4. Make sure to install [elasticsearch docker image](./elasticsearch.md).
5. Test connection
