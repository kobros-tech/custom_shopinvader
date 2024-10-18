# Elasticsearch docker compose configuration file:
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