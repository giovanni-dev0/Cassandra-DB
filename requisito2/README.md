# Requisito 2 – Modelado físico y carga de datos en Apache Cassandra

## 🎯 Objetivo

Diseñar el modelo físico de la base de datos en Apache Cassandra y poblarla con datos reales de postulaciones universitarias contenidas en `postulaciones.xlsx`.

---

## 🧱 Estructura del modelo y creación en Cassandra

### 🔹 Ingreso a CQLSH y creación del keyspace

Desde el contenedor `cassandra1`, se accedió al shell interactivo de Cassandra:

```bash
docker exec -it cassandra1 cqlsh
```

Dentro de `cqlsh`:

```sql
CREATE KEYSPACE IF NOT EXISTS postulaciones_ks
WITH replication = {
  'class': 'SimpleStrategy',
  'replication_factor': 3
};

USE postulaciones_ks;
```

---

### 🔹 Creación de las tablas

#### 1. `postulaciones_por_carrera_estado`

```sql
CREATE TABLE IF NOT EXISTS postulaciones_por_carrera_estado (
  carrera TEXT,
  estado TEXT,
  periodo INT,
  cedula TEXT,
  sexo TEXT,
  facultad TEXT,
  puntaje INT,
  region TEXT,
  ptje_nem INT,
  psu_promlm INT,
  grupo_depen TEXT,
  preferencia INT,
  pace TEXT,
  gratuidad TEXT,
  latitud DOUBLE,
  longitud DOUBLE,
  PRIMARY KEY ((carrera, estado), periodo, cedula)
) WITH CLUSTERING ORDER BY (periodo DESC, cedula ASC);
```

#### 2. `postulaciones_por_region_carrera`

```sql
CREATE TABLE IF NOT EXISTS postulaciones_por_region_carrera (
  region TEXT,
  carrera TEXT,
  estado TEXT,
  periodo INT,
  cedula TEXT,
  sexo TEXT,
  facultad TEXT,
  puntaje INT,
  ptje_nem INT,
  psu_promlm INT,
  grupo_depen TEXT,
  preferencia INT,
  pace TEXT,
  gratuidad TEXT,
  latitud DOUBLE,
  longitud DOUBLE,
  PRIMARY KEY ((region, carrera, estado), periodo, cedula)
) WITH CLUSTERING ORDER BY (periodo DESC, cedula ASC);
```

#### 3. `postulaciones_por_facultad`

```sql
CREATE TABLE IF NOT EXISTS postulaciones_por_facultad (
  facultad TEXT,
  estado TEXT,
  puntaje INT,
  cedula TEXT,
  periodo INT,
  carrera TEXT,
  sexo TEXT,
  region TEXT,
  ptje_nem INT,
  psu_promlm INT,
  grupo_depen TEXT,
  preferencia INT,
  pace TEXT,
  gratuidad TEXT,
  latitud DOUBLE,
  longitud DOUBLE,
  PRIMARY KEY ((facultad, estado), puntaje, cedula)
) WITH CLUSTERING ORDER BY (puntaje DESC, cedula ASC);
```

Verificación:

```sql
DESCRIBE TABLES;
```
![image](https://github.com/user-attachments/assets/3427a210-5229-4b78-a706-0d167671a6f5)

---

## ⚙️ Proceso de carga de datos

### 1. Se copiaron los archivos necesarios al entorno WSL:

```bash
cp /mnt/c/Users/USUARIO/Downloads/postulaciones.xlsx ~/contenedores/lab-cassandra-cluster/
cp /mnt/c/Users/USUARIO/Downloads/cargar_postulaciones.py ~/contenedores/lab-cassandra-cluster/
```

### 2. Se instalaron las dependencias requeridas:

```bash
sudo apt update
sudo apt install python3-pip -y
pip3 install pandas openpyxl cassandra-driver
```

### 3. Se ejecutó el script de carga:

```bash
python3 cargar_postulaciones.py
```

📥 Resultado:
> Inserción completada sin errores :)
![image](https://github.com/user-attachments/assets/a76c315b-68c9-442d-86cd-362e8cd7dbd5)

---

## 🔍 Verificación de la carga

Las siguientes consultas se ejecutaron desde `cqlsh` para confirmar la inserción de datos:

```sql
USE postulaciones_ks;

SELECT COUNT(*) FROM postulaciones_por_carrera_estado;       -- 16651 registros
SELECT COUNT(*) FROM postulaciones_por_region_carrera;       -- 16651 registros
SELECT COUNT(*) FROM postulaciones_por_facultad;             -- 15243 registros
```
![image](https://github.com/user-attachments/assets/301ec0b7-1b4a-4e9f-900e-75279047dfcb)


---

## ✅ Conclusión

El modelo físico fue implementado correctamente, las tablas cumplen con las reglas de distribución y eficiencia de lectura en Cassandra, y los datos fueron insertados sin errores. Este paso habilita el uso eficiente de las consultas del negocio (Requisito 3).
