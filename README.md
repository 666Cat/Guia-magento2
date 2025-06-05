# 🐳 Magento 2 Docker Guide

Este repositorio contiene una guía completa en formato Markdown para instalar Magento 2 usando la configuración de Docker oficial de [Mark Shust](https://github.com/markshust/docker-magento).

✅ Guía paso a paso  
✅ Configuración corregida de `compose.yaml`  
✅ Archivos `.env` recomendados  
✅ Solución a errores comunes de OpenSearch y MariaDB  
✅ Comandos útiles

---

## 📄 Contenido
🛠️ Requisitos previos
Antes de comenzar, asegúrate de tener instalado:

Docker Desktop (con soporte de Docker Compose)

Git

(opcional) Make

Linux (o WSL2 en Windows 10/11)

RAM mínima: 8 GB (recomendado 12 GB+)

Espacio en disco: al menos 10 GB libres

🗂️ Estructura del proyecto


🧭 Flujo recomendado

1️⃣ Clonar el repositorio oficial de Mark Shust
2️⃣ Descargar la versión deseada de Magento
3️⃣ Configurar los archivos .env
4️⃣ Corregir y personalizar docker-compose.yaml
5️⃣ Levantar los contenedores
6️⃣ Verificar OpenSearch y DB
7️⃣ Ejecutar bin/setup para instalar Magento
8️⃣ Crear usuario admin
9️⃣ Acceder al frontend y backend
10️⃣ Realizar pruebas o desarrollo

⚙️ Comandos útiles
Levantar contenedores:
docker compose up -d

Parar contenedores:
docker compose down

Parar contenedores + borrar volúmenes:
docker compose down -v

Ver estado de contenedores:
docker ps

Ver logs de un contenedor específico:
docker logs magento-db-1

Verificar OpenSearch:

docker exec -it magento-app-1 curl http://opensearch:9200

Crear usuario admin:

bin/magento admin:user:create

## 🚀 Registro de fallas y soluciones

Falla: opensearch contenedor unhealthy
Mensaje:

node settings must not contain any index level settings
Causa:
Estaba en compose.yaml la línea:

yaml

- index.blocks.read_only_allow_delete=true
Solución:
Eliminar esa línea del environment: de opensearch.

🚩 Falla: curl no conecta a OpenSearch
Mensaje:

curl: (7) Failed to connect to opensearch port 9200
Solución:
Asegurarse que el contenedor opensearch esté en estado healthy.

Corregir el healthcheck en compose.yaml:

yaml

healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:9200/_cluster/health"]
  interval: 30s
  timeout: 10s
  retries: 5
Verificar con:

bash

docker exec -it magento-app-1 curl http://opensearch:9200
🚩 Falla: magento-db-1 unhealthy
Logs:

dependency failed to start: container magento-db-1 is unhealthy
Causa:
MYSQL_ROOT_PASSWORD=magento en env/db.env

Solución:
Cambiar a:

env

MYSQL_ROOT_PASSWORD=root
Luego reiniciar:

bash

docker compose down -v
docker compose up -d
🚩 Falla: rabbitmq unhealthy
Causa común:
Otro proceso usando el puerto 5672.

Solución:
Verificar:

bash

sudo lsof -i :5672
Si está ocupado, parar el proceso o cambiar el puerto en compose.yaml.

🚩 Falla: bin/setup error yaml: line ...
Mensaje:

yaml: line 12: did not find expected key
yaml: line 91: could not find expected ':'
Causa:
Error de indentación en compose.yaml.

Solución:
Revisar que las líneas del compose.yaml estén correctamente indentadas con 2 espacios.

Revisar especialmente las líneas dentro de environment: y healthcheck:.





### 1️⃣ Parar el servicio Docker en Linu:

Parar todos los contenedores:

bash

docker stop $(docker ps -q)
Parar el servicio de Docker Engine:

bash

sudo systemctl stop docker
Reiniciar Docker:

bash

sudo systemctl restart docker
Ver estado de Docker:

bash

sudo systemctl status docker



