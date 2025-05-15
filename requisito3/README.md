# Requisito 3 – Consultas del negocio en Apache Cassandra

## 🎯 Objetivo

Ejecutar consultas específicas en Apache Cassandra sobre postulaciones universitarias, utilizando un modelo físico orientado a consultas. Estas consultas responden a necesidades reales de análisis del proceso de admisión.

---

## 🧩 Preparación

Desde el terminal:

```bash
docker exec -it cassandra1 cqlsh
```

Luego dentro de `cqlsh`:

```sql
USE postulaciones_ks;
```

---

## 🧪 Consultas ejecutadas

### 🔸 Consulta 3.a – Postulantes de Medicina por período

**Descripción:** Listar todos los postulantes **matriculados** en la carrera de **Medicina**, ordenados por **PERIODO**.

**Tabla usada:** `postulaciones_por_carrera_estado`  
**Claves usadas:**  
- Partition key: `(carrera, estado)`
- Clustering key: `(periodo DESC, cedula ASC)`

```sql
SELECT
    cedula,
    periodo,
    sexo,
    preferencia,
    carrera,
    estado AS matriculado,
    puntaje,
    facultad,
    region
FROM postulaciones_por_carrera_estado
WHERE carrera = 'MEDICINA' AND estado = 'SI';
```

📌 Resultado esperado: Listado ordenado por período descendente.
![Captura de pantalla 2025-05-15 181550](https://github.com/user-attachments/assets/ce5f38a0-92f5-4d99-85e8-7d9088cbda21)

---

### 🔸 Consulta 3.b – Postulantes de Ing. Civil Informática en Maule

**Descripción:** Listar todos los postulantes **matriculados** en la carrera **Ingeniería Civil Informática** que provienen de la **Región del Maule**, ordenados por **PERIODO**.

**Tabla usada:** `postulaciones_por_region_carrera`  
**Claves usadas:**  
- Partition key: `(region, carrera, estado)`
- Clustering key: `(periodo DESC, cedula ASC)`

```sql
SELECT
    cedula,
    periodo,
    sexo,
    preferencia,
    carrera,
    estado AS matriculado,
    puntaje,
    facultad,
    region
FROM postulaciones_por_region_carrera
WHERE region = 'MAULE' AND carrera = 'INGENIERÍA CIVIL INFORMÁTICA' AND estado = 'SI';
```

📌 Resultado esperado: Postulantes ordenados por periodo.
![Captura de pantalla 2025-05-15 181657](https://github.com/user-attachments/assets/6ac39828-36eb-4fcb-8fb2-51bf8fc15005)

---

### 🔸 Consulta 3.c – Postulantes de Ciencias de la Salud por puntaje PSU

**Descripción:** Listar todos los postulantes **matriculados** en la **Facultad de Ciencias de la Salud**, ordenados por **PUNTAJE PSU**.

**Tabla usada:** `postulaciones_por_facultad`  
**Claves usadas:**  
- Partition key: `(facultad, estado)`
- Clustering key: `(puntaje DESC, cedula ASC)`

```sql
SELECT
    cedula,
    periodo,
    sexo,
    preferencia,
    carrera,
    estado AS matriculado,
    puntaje,
    facultad,
    region
FROM postulaciones_por_facultad
WHERE facultad = 'CIENCIAS DE LA SALUD' AND estado = 'SI';
```

📌 Resultado esperado: Orden descendente por puntaje PSU.
![Captura de pantalla 2025-05-15 181810](https://github.com/user-attachments/assets/4dd7a79f-29a1-45e1-999d-a3565a783689)

---

## ✅ Conclusión

Las tres consultas se ejecutaron exitosamente sobre las tablas previamente modeladas. Estas consultas forman la base para construir visualizaciones en Power BI (Requisito 4), ya que reflejan patrones de matrícula por carrera, región y facultad.
