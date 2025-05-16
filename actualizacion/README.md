
# 🧑‍💻 Actualización del clúster Cassandra para cumplir Requisitos 4 y 5

## 🎯 Objetivo

Modificar el archivo `docker-compose.yml` para exponer todos los nodos del clúster Cassandra en puertos distintos del host. Esto permitirá:

- Conectarse desde Power BI a cada nodo individual.
- Simular tolerancia a fallos (alta disponibilidad).
- Cumplir el Requisito 4 (conexión desde Power BI) y el Requisito 5 (disponibilidad).

---

## 📁 Paso 1: Editar el archivo `docker-compose.yml`

1. Abrir la terminal (Ubuntu o WSL) en la carpeta donde tienen su proyecto Cassandra.
2. Abrir el archivo con:

   ```bash
   nano docker-compose.yml
   ```

3. Modificar el contenido del archivo para que cada nodo exponga un puerto distinto:

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
    ports:
      - 9043:9042
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
    ports:
      - 9044:9042
    environment:
      - CASSANDRA_CLUSTER_NAME=MiCluster
      - CASSANDRA_SEEDS=cassandra1,cassandra2,cassandra3
      - CASSANDRA_BROADCAST_ADDRESS=cassandra3
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch

networks:
  cassandra-net:
    driver: bridge
```

---

## 💾 Paso 2: Reiniciar los contenedores

```bash
docker compose down
docker compose up -d
```

Verifica los puertos con:

```bash
docker ps
```

Debes ver los puertos 9042, 9043 y 9044 expuestos.

---

## 🌐 Paso 3: Obtener la IP desde Windows

1. Abre PowerShell o CMD en Windows.
2. Ejecuta:

   ```powershell
   ipconfig
   ```

3. Busca la interfaz `vEthernet (WSL)` y anota la IP (ej: 172.27.48.1).

---

## 📌 Paso 4: Crear DSN en Windows

Abre “Administrador de orígenes de datos ODBC (64 bits)” y crea 3 DSN:

- Cassandra_Node1 → IP: 172.27.x.x, Puerto: 9042
- Cassandra_Node2 → IP: 172.27.x.x, Puerto: 9043
- Cassandra_Node3 → IP: 172.27.x.x, Puerto: 9044

---

## ✅ Paso 5: Conectarse desde Power BI

1. Abrir Power BI Desktop.
2. Obtener datos → ODBC → seleccionar DSN.
3. Usar consulta SQL como:

```sql
SELECT * FROM postulaciones_ks.postulaciones_por_carrera_estado WHERE carrera = 'MEDICINA' AND estado = 'SI';
```

---

## 🧪 Paso 6: Probar alta disponibilidad

```bash
docker stop cassandra1
```

Luego recargar Power BI usando DSN de cassandra2 o cassandra3.

---

## ✅ Conclusión

Con esta modificación tus compañeros pueden cumplir los Requisitos 4 y 5 sin rehacer todo, solo editando el archivo `docker-compose.yml`.
