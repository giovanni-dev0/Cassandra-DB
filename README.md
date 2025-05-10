
# 📘 Guía para Montar el Clúster Cassandra (Requisito 1 – INF325)

## 📌 Objetivo

Montar un clúster Cassandra de 3 nodos en tu computador usando Docker y Docker Compose, Requisito 1 del laboratorio INF325.

---

## 🖥️ Requisitos del sistema

- Windows 11 con **WSL 2** y Ubuntu 22.04 instalado
- Docker Desktop instalado y funcionando
- Docker Compose v2 habilitado
- Conexión a Internet

### ✅ Verifica que Docker esté funcionando

Desde Ubuntu (WSL), corre:

```bash
docker --version
docker compose version
docker ps
```

Si ves versiones válidas y la lista de contenedores (aunque vacía), estás listo.

---

## 📁 Paso 1: Crear la carpeta del proyecto

```bash
mkdir -p ~/lab-cassandra-cluster
cd ~/lab-cassandra-cluster
```

---

## 📄 Paso 2: Crear el archivo `docker-compose.yml`

Ejecuta:

```bash
nano docker-compose.yml
```

Y pega este contenido:

```yaml
version: '3.8'

services:
  cassandra1:
    image: cassandra:4.1
    container_name: cassandra1
    hostname: cassandra1
    networks:
      - cassandra-net
    ports:
      - 9042:9042
    environment:
      - CASSANDRA_CLUSTER_NAME=MiCluster
      - CASSANDRA_SEEDS=cassandra1,cassandra2,cassandra3
      - CASSANDRA_BROADCAST_ADDRESS=cassandra1
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch

  cassandra2:
    image: cassandra:4.1
    container_name: cassandra2
    hostname: cassandra2
    networks:
      - cassandra-net
    environment:
      - CASSANDRA_CLUSTER_NAME=MiCluster
      - CASSANDRA_SEEDS=cassandra1,cassandra2,cassandra3
      - CASSANDRA_BROADCAST_ADDRESS=cassandra2
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch

  cassandra3:
    image: cassandra:4.1
    container_name: cassandra3
    hostname: cassandra3
    networks:
      - cassandra-net
    environment:
      - CASSANDRA_CLUSTER_NAME=MiCluster
      - CASSANDRA_SEEDS=cassandra1,cassandra2,cassandra3
      - CASSANDRA_BROADCAST_ADDRESS=cassandra3
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch

networks:
  cassandra-net:
    driver: bridge
```

Guarda y cierra el editor (`Ctrl + O`, luego `Enter`, y `Ctrl + X`).

---

## 🚀 Paso 3: Levantar el clúster

```bash
docker compose up -d
```

Verifica que los contenedores estén corriendo:

```bash
docker ps
```

---

## 🔍 Paso 4: Verificar el estado del clúster

Conéctate a uno de los nodos:

```bash
docker exec -it cassandra1 bash
```

Dentro del contenedor, ejecuta:

```bash
nodetool status
```

✔️ Debes ver los 3 nodos con estado `UN` (Up / Normal).

Luego sal del contenedor:

```bash
exit
```

---

## 🛑 Paso 5: Apagar el clúster

Cuando termines:

```bash
docker compose down
```

---

## 📸 Evidencia para el informe

Para completar el informe en el Requisito 1 (`2.1 Desarrollo Requisito 1`):

- Captura de `docker ps`
- Captura de `nodetool status`
- Imagen del archivo `docker-compose.yml`
- Breve explicación del funcionamiento del clúster Cassandra y configuración

---
