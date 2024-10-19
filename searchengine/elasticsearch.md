# Elasticsearch docker compose configuration file:


## Create this docker compose configuration file:
    
```yml
services:
    elastic:
        image: docker.elastic.co/elasticsearch/elasticsearch:7.13.4
        environment:
        - discovery.type=single-node
        - http.cors.enabled=true
        - http.cors.allow-origin=*
        - http.cors.enabled=true  # Enable CORS
        - http.cors.allow-credentials=true  # Allow credentials
        - http.cors.allow-headers=Authorization, Content-Type  # Allow necessary headers
        - xpack.security.enabled=true            # Enabling security
        - ELASTIC_PASSWORD=your_elastic_password  # Setting password for the elastic user
        - bootstrap.memory_lock=true             # Lock memory for performance
        - "ES_JAVA_OPTS=-Xms512m -Xmx512m"       # Set Java heap size (adjust as per your need)
    ulimits:
    memlock:
        soft: -1
        hard: -1
    volumes:
    - esdata:/usr/share/elasticsearch/data
    labels:
    - "traefik.enable=true"
    - "traefik.port=9200"
    - "traefik.http.routers.elastic.rule=Host(elastic.localhost)"
    ports:
    - "9200:9200"
    - "9300:9300"
    cap_add:
    - IPC_LOCK  # Add this to enable memory locking
    restart: always

version: '3'
volumes:
esdata:
```


## After indexing items [from odoo to elasticsearch](./readme.md), you can check which idices are present through this url:
    
``http://localhost:9200/_cat/indices?v``


## If the index health is labeled ``yellow``, you can change it into green by running this command:

```bash
curl -u elastic:your_elastic_password -X PUT \
"http://localhost:9200/elastic_external_fs_product_variant_en_us-1/_settings" \
-H 'Content-Type: application/json' -d '{"index": {"number_of_replicas": 0}}'
```


## To get all indexed products within a certain index call this url:

``http://localhost:9200/elastic_external_fs_product_variant_en_us-1/_search?pretty``


## To get an indexed product by it's ID within a certain index call this url:

``http://localhost:9200/elastic_external_fs_product_variant_en_us-1/_doc/61?pretty``

## To search products within a certain index make this JSON call:

```JavaScript
const username = 'elastic';  // Replace with your username
const password = 'your_elastic_password';  // Replace with your password
const url = "http://localhost:9200/elastic_external_fs_2_product_variant_en_us-1/_search";

async function searchProduct(productName) {
  const headers = {
    "Authorization": "Basic " + btoa(`${username}:${password}`),
    "Content-Type": "application/json"
  };

  const body = JSON.stringify({
    query: {
      match: {
        name: productName  // Replace with the field you want to search
      }
    }
  });

  try {
    const response = await fetch(url, {
      method: "POST",  // POST method since we are sending a query body
      headers: headers,
      body: body
    });

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    console.log(data);  // Handle the data, e.g., display search results
    console.log(data.hits.hits);
  } catch (error) {
    console.error("Fetch error:", error);
  }
}

// Call the function and search for a product
searchProduct("Meuble TV");
```