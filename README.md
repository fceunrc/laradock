# **Resumen de servicios en**

# docker-compose.yml

### **Utilidades y base**

- **workspace** → contenedor de desarrollo (PHP, Node, Composer, herramientas varias).  

- **php-fpm** → intérprete PHP-FPM.  

- **php-worker** → workers para ejecutar tareas en segundo plano.  

- **laravel-horizon** → supervisor de colas en Laravel.  

- **soketi** → servidor WebSocket (alternativa a Pusher).  

- **docker-in-docker** → cliente Docker dentro de un contenedor.  

### **Servidores web / proxies**

- **nginx** → servidor web principal.  

- **openresty** → Nginx extendido con Lua.  

- **apache2** → servidor web Apache.  

- **caddy** → servidor web moderno con SSL automático.  

- **hhvm** → intérprete alternativo para PHP (HipHop VM).  

- **proxy / proxy2 (varnish)** → cache HTTP.  

- **haproxy** → balanceador de carga.  

- **traefik** → reverse proxy moderno con dashboard.  

### **Email & mensajería**

- **mailcatcher, mailhog, maildev, mailpit** → capturan correos en desarrollo.  

- **mailu-**\* → suite completa de servidor de correo:  
  - _front (nginx), admin, imap (dovecot), smtp (postfix), antispam (rspamd), antivirus (clamav), webdav, webmail, fetchmail  
        _
- **rabbitmq** → cola de mensajes.  

- **sqs** → emulación de Amazon SQS.  

- **beanstalkd** + **beanstalkd-console** → cola de trabajo ligera.  

- **gearman** → sistema de colas distribuido.  

- **laravel-echo-server** → broadcasting en Laravel.  

### **Bases de datos**

- **mysql**
- **percona**
- **mssql**
- **mariadb**
- **postgres**
- **postgres-postgis**
- **pgbackups** → backup automático de Postgres.  

- **neo4j** → base de grafos.  

- **mongo**
- **rethinkd**
- **clickhouse**
- **cassandra**
- **redis** / **redis-cluster**
- **ssdb** → key-value DB similar a Redis.  

- **aerospike**
- **nats** → sistema de mensajería ligera.  

- **zookeeper** → coordinación para Kafka y otros.  

- **couchdb**
- **tarantool** / **tarantool-admin**
- **manticore** → motor de búsqueda (fork de Sphinx).  

### **Motores de búsqueda / analítica**

- **meilisearch**
- **elasticsearch**
- **logstash**
- **kibana**
- **dejavu** (UI para ES).  

- **solr**
- **sonarqube** → análisis estático de código.  

- **weaver (AthenaPDF)** → conversión de documentos a PDF.  

- **metabase** → BI / dashboards.  

- **grafana** → visualización de métricas.  

- **graylog** → centralización de logs.  

### **DevOps / CI-CD**

- **blackfire** → profiler de rendimiento PHP.  

- **jenkins** → CI/CD.  

- **gitlab** → servidor Git.  

- **gitlab-runner**
- **portainer** → dashboard de Docker.  

- **docker-registry** → registro privado de imágenes.  

- **docker-web-ui** → interfaz web para el registro.  

- **aws (eb-cli)** → cliente AWS EB.  

- **certbot** → generación de certificados SSL.  

### **IDEs web**

- **ide-theia**
- **ide-webide**
- **ide-codiad**
- **ide-icecoder**

### **Herramientas admin**

- **phpmyadmin**
- **adminer**
- **pgadmin**
- **redis-webui**
- **mongo-webui**

### **Documentación & APIs**

- **swagger-editor**
- **swagger-ui**

### **Testing & automatización**

- **selenium**
- **thumbor** → procesador de imágenes.  

### **Seguridad / autenticación**

- **keycloak** → identity & access management.  

### **Otros**

- **confluence** → wiki empresarial (Atlassian).  

- **onedev** → alternativa ligera a Gitlab/Github.  

- **tomcat** → servidor Java.  

- **react** → frontend React (hot reload).  

- **mosquitto** → broker MQTT (IoT).  

- **ide-x** varios.  

# **Conclusión**

Este docker-compose.yml es un **super-stack multipropósito**:

- Bases de datos SQL, NoSQL y motores de búsqueda.  

- Servidores web, balanceadores y proxies.  

- Infra de mensajería (RabbitMQ, Kafka, NATS, Redis, etc.).  

- Servicios de correo (Mailu completo + mailcatcher/hog/dev).  

- Herramientas DevOps (CI/CD, monitoreo, métricas).  

- IDEs web y administración de DBs.  

- Aplicaciones empresariales (Confluence, Gitlab, Keycloak).  

# **Servicios y configuración (conciso)**

## **Utilidades / base**

- **workspace**: build ./workspace; decenas de args y herramientas dev; mapea código (${APP_CODE_PATH_HOST} a ${APP_CODE_PATH_CONTAINER}), puertos de dev (3000/3001/8080/8000/4200/5173), depende de docker-in-docker; redes frontend y backend; SSH en ${WORKSPACE_SSH_PORT}.  

- **php-fpm**: build ./php-fpm; gran lista de flags de extensiones PHP vía args; expone 9000; usa php${PHP_VERSION}.ini; red backend; depende de workspace.  

- **php-worker**: build ./php-worker; workers/supervisor; monta ./php-worker/supervisord.d; red backend; depende de workspace.  

- **laravel-horizon**: build ./laravel-horizon; supervisor de colas; monta ./laravel-horizon/supervisord.d; red backend; depende de workspace.  

- **soketi**: build ./soketi; puertos ${SOKETI_PORT}:6001, ${SOKETI_METRICS_SERVER_PORT}:9601; redes frontend y backend.  

- **docker-in-docker**: imagen docker:20.10-dind; privileged; share /certs/client; expone 2375; red backend.  

## **Web / proxies / balanceadores**

- **nginx**: build ./nginx; monta código, logs y sites-available; puertos ${NGINX_HOST_HTTP_PORT}:80, ${NGINX_HOST_HTTPS_PORT}:443, y backend en 81; depende de php-fpm; redes frontend/backend.  

- **openresty**: build ./openresty; similar a nginx; puertos HTTP/HTTPS y 81; depende de php-fpm; redes frontend/backend.  

- **apache2**: build ./apache2; mapea código, logs y sitios; puertos ${APACHE_HOST_HTTP_PORT}:80, ${APACHE_HOST_HTTPS_PORT}:443; depende de php-fpm; redes frontend/backend.  

- **caddy**: build ./caddy; mapea config y logs; puertos ${CADDY_HOST_HTTP_PORT}:80, ${CADDY_HOST_HTTPS_PORT}:443; depende de php-fpm; redes frontend/backend.  

- **hhvm**: build ./hhvm; expone 9000; redes frontend/backend.  

- **proxy** / **proxy2** (Varnish): build ./varnish; expone ${VARNISH_PORT}; variables de backend (VARNISH_BACKEND_PORT ~ 81 hacia nginx/openresty); red frontend.  

- **haproxy**: build ./haproxy; puerto ${HAPROXY_HOST_HTTP_PORT}:8085; monta el socket Docker; link a proxy y proxy2.  

- **traefik**: build ./traefik; puertos ${TRAEFIK_HOST_HTTP_PORT}, ${TRAEFIK_HOST_HTTPS_PORT}, ${TRAEFIK_DASHBOARD_PORT}; labels para dashboard y basic-auth; redes frontend/backend.  

## **Correo y mensajería**

- **mailcatcher**, **mailhog**, **maildev**, **mailpit**: capturan email de desarrollo; exponen puertos SMTP/HTTP indicados por variables (MAILDEV_HTTP_PORT, etc.); redes frontend/backend.  

- **Mailu** (**mailu-front/admin/imap/smtp/antispam/antivirus/webdav/webmail/fetchmail**): imágenes ${DOCKER_ORG}/…:${MAILU_VERSION} (definir DOCKER_ORG y MAILU_VERSION en el .env de Docker; sugerido ghcr.io/mailu y versión actual). mailu-front publica puertos 80/443 y puertos IMAP/POP/SMTP estándar; volúmenes bajo ${DATA_PATH_HOST}/mailu/\*; depende de redis en algunos componentes.  

- **rabbitmq**: build ./rabbitmq; puertos ${RABBITMQ_NODE_HOST_PORT}:5672, management ${RABBITMQ_MANAGEMENT_HTTP_HOST_PORT}:15672; volumen de datos y config; depende de php-fpm; red backend.  

- **sqs** (localstack-similar): build ./sqs; puertos ${SQS_NODE_HOST_PORT}:9324, ${SQS_MANAGEMENT_HTTP_HOST_PORT}:9325; red frontend/backend.  

- **beanstalkd** y **beanstalkd-console**: puertos ${BEANSTALKD_HOST_PORT}:11300 y console ${BEANSTALKD_CONSOLE_HOST_PORT}:2080; red backend.  

- **gearman**: build ./gearman; puerto ${GEARMAN_PORT}:4730; red backend.  

- **laravel-echo-server**: build ./laravel-echo-server; puerto ${LARAVEL_ECHO_SERVER_PORT}:6001; link a redis; redes frontend/backend.  

- **nats**: build ./nats; puertos ${NATS_CLIENT_PORT}:4222, ${NATS_MONITORING_PORT}:6222, ${NATS_ROUTE_PORT}:8222; red backend.  

- **mosquitto**: build ./mosquitto; puerto ${MOSQUITTO_PORT}:9001; redes frontend/backend.  

## **Bases de datos y almacenamiento**

- **mysql** / **percona** / **mariadb**: datos en ${DATA_PATH_HOST}/mysql|percona|mariadb; puertos ${…\_PORT}:3306; credenciales por variables ${…\_DATABASE}, ${…\_USER}, ${…\_PASSWORD}, ${…\_ROOT_PASSWORD}; red backend.  

- **mssql**: build ./mssql; volumen mssql; puerto ${MSSQL_PORT}:1433; SA_PASSWORD desde ${MSSQL_PASSWORD}; red backend.  

- **postgres**: build ./postgres; datos en ${DATA_PATH_HOST}/postgres; puerto ${POSTGRES_PORT}:5432; variables para integraciones (Gitlab/Keycloak/JupyterHub); red backend.  

- **pgbackups**: imagen prodrigestivill/postgres-backup-local; monta ../backup; link a postgres; usa ${POSTGRES_\*}.  

- **postgres-postgis**: build ./postgres-postgis; mismo puerto 5432 (ojo conflicto si levantas ambos); red backend.  

- **neo4j**: imagen neo4j:latest; puertos 7401:7474 (HTTP), 7402:7687 (Bolt); NEO4J_AUTH=none; volúmenes de datos/logs; red backend.  

- **mongo**: build ./mongo; puerto ${MONGODB_PORT}:27017; MONGO_INITDB_ROOT_USERNAME/PASSWORD; datos en ${DATA_PATH_HOST}/mongo\*; red backend.  

- **rethinkdb**: build ./rethinkdb; puerto ${RETHINKDB_PORT}:8080; datos en ${DATA_PATH_HOST}/rethinkdb; red backend.  

- **clickhouse**: build ./clickhouse; puertos ${CLICKHOUSE_HTTP_PORT}:8123, ${CLICKHOUSE_CLIENT_PORT}:9000, ${CLICKHOUSE_NATIVE_PORT}:9009; datos y config montados; ulimits elevados; red backend.  

- **redis** / **redis-cluster**: build ./redis o ./redis-cluster; puerto ${REDIS_PORT}:6379 (cluster usa rango ${REDIS_CLUSTER_PORT_RANGE}:7000-7005); password vía ${REDIS_PASSWORD}; red backend.  

- **ssdb**: build ./ssdb; puerto ${SSDB_PORT}:8888; red backend.  

- **aerospike**: build ./aerospike; puertos ${AEROSPIKE_\*} 3000–3003; red backend.  

- **zookeeper**: build ./zookeeper; puerto ${ZOOKEEPER_PORT}:2181; red backend.  

- **couchdb**: build ./couchdb; puerto ${COUCHDB_PORT}:5984; red backend.  

- **tarantool** / **tarantool-admin**: puertos ${TARANTOOL_PORT}:3301 y ${TARANTOOL_ADMIN_PORT}:80; red backend.  

- **manticore**: build ./manticore; puertos ${MANTICORE_API_PORT}:9312, ${MANTICORE_SPHINXQL_PORT}:9306, ${MANTICORE_HTTP_PORT}:9308; red backend.  

- **minio**: build ./minio; puertos ${MINIO_PORT}:9000, ${MINIO_CONSOLE_PORT}:9001; MINIO_ROOT_USER/PASSWORD; redes frontend/backend.  

## **Búsqueda / analítica / observabilidad**

- **meilisearch**: imagen getmeili/meilisearch:latest; puerto ${MEILISEARCH_HOST_PORT}:7700; master key ${MEILISEARCH_KEY}; redes frontend/backend.  

- **elasticsearch**: build ./elasticsearch; puertos ${ELASTICSEARCH_HOST_HTTP_PORT}:9200, ${ELASTICSEARCH_HOST_TRANSPORT_PORT}:9300; desactiva xpack; ES_JAVA_OPTS 512M; redes frontend/backend.  

- **logstash**: build ./logstash; monta config/pipeline; puerto 5001:5001; LS_JAVA_OPTS 1G; depende de elasticsearch.  

- **kibana**: build ./kibana; puerto ${KIBANA_HTTP_PORT}:5601; depende de elasticsearch; redes frontend/backend.  

- **dejavu**: build ./dejavu; puerto ${DEJAVU_HTTP_PORT}:1358; depende de elasticsearch.  

- **sonarqube**: build ./sonarqube; puerto ${SONARQUBE_PORT}:9000; depende de postgres; sonar.jdbc.\* desde variables; redes backend/frontend.  

- **weaver (Athena PDF)**: imagen arachnysdocker/athenapdf-service; puerto 8080:8080; depende de workspace.  

- **metabase**: imagen metabase/metabase:latest; puerto ${METABASE_PORT}:3000; volumen ${DATA_PATH_HOST}/metabase-data; red backend.  

- **grafana**: build ./grafana; puerto ${GRAFANA_PORT}:3000; volumen ${DATA_PATH_HOST}/grafana; red backend.  

- **graylog**: build ./graylog; puertos 9000, 514/tcp+udp, 12201/tcp+udp; depende de mongo y elasticsearch; volumen ${DATA_PATH_HOST}/graylog; red backend.  

## **Admin de BD / UIs**

- **phpmyadmin**: build ./phpmyadmin; puerto ${PMA_PORT}:80; depende de ${PMA_DB_ENGINE} (MySQL/MariaDB/Percona); redes frontend/backend.  

- **adminer**: build ./adminer; puerto ${ADM_PORT}:8080; redes frontend/backend.  

- **pgadmin**: imagen dpage/pgadmin4:latest; puerto ${PGADMIN_PORT}:80; volumen ${DATA_PATH_HOST}/pgadmin; depende de postgres; redes frontend/backend.  

- **redis-webui**: build ./redis-webui; puerto ${REDIS_WEBUI_PORT}:80; depende de redis; red backend.  

- **mongo-webui**: build ./mongo-webui; puerto ${MONGO_WEBUI_PORT}:3000; depende de mongo; red backend.  

## **DevOps / CI-CD**

- **blackfire**: imagen blackfire/blackfire; requiere BLACKFIRE_SERVER_ID/TOKEN; depende de php-fpm; red backend.  

- **jenkins**: build ./jenkins; puertos ${JENKINS_HOST_HTTP_PORT}:8080 y ${JENKINS_HOST_SLAVE_AGENT_PORT}:50000; monta ${JENKINS_HOME} y socket Docker; redes frontend/backend.  

- **gitlab**: build ./gitlab; puertos ${GITLAB_HOST_HTTP_PORT}:80, ${GITLAB_HOST_HTTPS_PORT}:443, ${GITLAB_HOST_SSH_PORT}:22; depende de redis y postgres; mapea config/data/logs; red backend.  

- **gitlab-runner**: imagen gitlab/gitlab-runner:latest; monta /var/run/docker.sock; RUNNER_EXECUTOR=shell.  

- **portainer**: build ./portainer; puerto 9010:9000; monta socket Docker; red backend.  

- **docker-registry** / **docker-web-ui**: registry privado ${DOCKER_REGISTRY_PORT}:5000 y UI ${DOCKER_WEBUI_PORT}:80; redes backend (UI también frontend).  

- **aws (eb-cli)**: build ./aws-eb-cli; TTY true; depende de workspace.  

- **certbot**: build ./certbot; monta ./data/certbot/certs/ y ./certbot/letsencrypt/; red frontend.  

## **IDEs Web**

- **ide-theia**: build ./ide-theia; puerto ${IDE_THEIA_PORT}:3000; red backend; monta ${APP_CODE_PATH_HOST}.  

- **ide-webide**: build ./ide-webide; puerto ${IDE_WEBIDE_PORT}:8080; red backend.  

- **ide-codiad**: build ./ide-codiad; puerto ${IDE_CODIAD_PORT}:80; red backend.  

- **ide-icecoder**: build ./ide-icecoder; puerto ${IDE_ICECODER_PORT}:8080; red backend.  

## **Documentación & APIs**

- **swagger-editor**: build ./swagger-editor; puerto ${SWAGGER_EDITOR_PORT}:8080; red backend.  

- **swagger-ui**: build ./swagger-ui; puerto ${SWAGGER_UI_PORT}:8080; API_URL por variable; red backend.  

## **Testing / automatización / procesamiento**

- **selenium**: build ./selenium; puerto ${SELENIUM_PORT}:4444; monta /dev/shm; red frontend.  

- **thumbor**: build ./thumbor; puerto ${THUMBOR_PORT}:8000; enormes opciones vía variables; redes frontend/backend.  

## **Seguridad / identidad**

- **keycloak**: imagen bitnami/keycloak:${KEYCLOAK_VERSION}; puerto ${KEYCLOAK_HTTP_PORT}; DB Postgres vía variables; depende de postgres; redes frontend/backend.  

## **Otros**

- **confluence**: imagen atlassian/confluence-server:${CONFLUENCE_VERSION}; puerto ${CONFLUENCE_HOST_HTTP_PORT}:8090; volumen ${DATA_PATH_HOST}/Confluence; depende de postgres; redes frontend/backend.  

- **onedev**: imagen 1dev/server; puertos ${ONEDEV_HTTP_PORT}:6610, ${ONEDEV_SSH_PORT}:6611; monta ${DATA_PATH_HOST}/onedev y socket Docker; redes frontend/backend.  

- **tomcat**: imagen tomcat:${TOMCAT_VERSION}; puerto ${TOMCAT_HOST_HTTP_PORT}:8080; monta webapps y logs; redes frontend/backend.  

- **react**: build ./react; puerto 3000:3000; monta ./react y excluye node_modules real (usa del contenedor); redes frontend/backend.  

- **kafka**: imagen wurstmeister/kafka; puerto 9092:9092; KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181; volumen datos y socket; red backend.  

- **kafka-manager**: imagen sheepkiller/kafka-manager; puerto 9020:9000; ZK_HOSTS=zookeeper:2181; red backend.  

- **ipython-controller/engine**: build ./ipython; puertos 33327-33338; red backend.  

- **jupyterhub / jupyterhub-user**: build ./jupyterhub; puerto ${JUPYTERHUB_PORT}:80; depende de postgres y jupyterhub-user; monta config y datos; red backend.  

- **netdata**: imagen netdata/netdata:latest; puerto ${NETDATA_PORT}:19999; cap SYS_PTRACE; monta /proc, /sys y socket; red backend.  

## **Diferencias con .env (Laravel)**

Tu .env de Laravel define cosas como: APP_\*, DB_CONNECTION=sqlite, SESSION_DRIVER=database, REDIS_HOST=127.0.0.1, MAIL_MAILER=log, etc. Esto **no** alimenta automáticamente las variables ${…} del compose. Útil para la app, pero **no configura contenedores** (puertos, rutas, credenciales, imágenes).

# LARAVEL

Laravel necesita, como base:

- **workspace** → para desarrollar (composer, npm, artisan).  

- **php-fpm** → motor PHP.  

- **nginx** (o apache/caddy) → servidor web.  

- **mysql** (o mariadb/postgres) → base de datos.  

- **phpmyadmin** o **adminer** (opcional, para administrar DB).  

- **redis** (opcional, si tu app lo usa para cache/queue).  

- **laravel-horizon** (opcional, si gestionas colas).  

## **Comando recomendado**

Para un stack Laravel clásico con MySQL + Redis:

**docker compose up -d workspace php-fpm nginx mysql phpmyadmin redis**

## **Variantes**

- Si prefieres **Postgres** en lugar de MySQL:  
    <br/>

**docker compose up -d workspace php-fpm nginx postgres pgadmin redis**

Si usas queues con Horizon: añade laravel-horizon.

## **Importante**

Antes de correr el comando:

1. Configura en tu .env de Laravel los valores correctos (DB_HOST=mysql, REDIS_HOST=redis, etc.).  

2. Verifica que en el .env de Docker Compose hayas definido puertos y contraseñas (ej. MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD).  

3. Revisa con:

**docker compose config**

para confirmar que todas las variables ${…} están resueltas.

# Code-Server

Como la imagen theiaide/theia ya no está disponible en Docker Hub, la opción estable es **VS Code Server** de Coder.

**ide-theia/Dockerfile**

code-server escucha por defecto en el **puerto 8080** dentro del contenedor. En el compose lo exponemos como 3000 en el host (o lo que definas en IDE_THEIA_PORT).

Variables útiles en tu .env (de Docker Compose)

IDE_THEIA_PORT=3000

IDE_PASSWORD=pon-una-clave-fuerte

APP_CODE_PATH_HOST=/ruta/a/tu/proyecto

## **Comandos**

docker compose build ide-theia

docker compose up -d ide-theia
