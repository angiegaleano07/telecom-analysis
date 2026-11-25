# telecom-analysis
# 📡 Análisis de Clientes ConnectaTel

Análisis exploratorio y segmentación de clientes de ConnectaTel, una empresa de telecomunicaciones en Latinoamérica, con datos registrados hasta el año 2024.

El proyecto recorre el ciclo completo de un análisis de datos: carga, auditoría de calidad, limpieza, estadística descriptiva, visualización, detección de outliers, segmentación y conclusiones para el negocio.

---

## 🎯 Objetivo

Evaluar el comportamiento de los clientes para:

- Construir un **perfil estadístico** de los usuarios y su consumo.
- Detectar **comportamientos atípicos** (outliers) de uso.
- Crear **segmentos de clientes** por edad y por nivel de uso.
- Identificar **patrones de consumo** que sirvan para diseñar estrategias de retención y mejorar la oferta de planes.

---

## 🗂️ Datasets utilizados

| Archivo | Contenido |
|---|---|
| `plans.csv` | Planes vigentes: precio, minutos incluidos, GB incluidos y costo por extra (2 planes: Básico y Premium). |
| `users_latam.csv` | Clientes: edad, ciudad, fecha de registro, plan y fecha de baja (churn). |
| `usage.csv` | Detalle del uso real de los servicios (llamadas y mensajes): tipo, fecha, duración y longitud. |

> ⚠️ Los datos no están incluidos en este repositorio. El notebook los lee desde la ruta `/datasets/`. Consulta la sección [Cómo ejecutar el notebook](#-cómo-ejecutar-el-notebook) para adaptar las rutas.

---

## 🧭 Etapas del análisis

1. **Cargar y explorar:** carga de los tres datasets, revisión de dimensiones (`.shape`), tipos de datos (`.info()`) y vista preliminar (`.head()`).
2. **Calidad de datos:** revisión de valores nulos, detección de valores inválidos (sentinels) y validación de fechas.
3. **Limpieza básica:**
   - `age`: el sentinel `-999` se reemplaza por la mediana.
   - `city`: el sentinel `"?"` se convierte en nulo.
   - `reg_date`: las fechas futuras (año 2026) se marcan como nulas.
   - `duration` y `length`: se dejan como nulos tras verificar que su ausencia depende del tipo de evento (`call` o `text`).
4. **Estadísticas de uso por usuario:** se agrega `usage` por `user_id` (`cant_mensajes`, `cant_llamadas`, `cant_minutos_llamada`) y se une con `users` en `user_profile`.
5. **Distribuciones y outliers:** histogramas por plan, boxplots y método IQR. Los valores extremos se conservan porque representan consumo real de alto uso.
6. **Segmentación de clientes:**
   - `grupo_uso`: Bajo uso, Uso medio y Alto uso, según llamadas y mensajes.
   - `grupo_edad`: Joven, Adulto y Adulto mayor.
7. **Insight ejecutivo:** conclusiones y recomendaciones para los stakeholders.

---

## 🔎 Hallazgos principales

**Problemas de calidad de datos**

| Columna | Problema | Magnitud |
|---|---|---|
| `users.age` | Sentinel `-999` | Reemplazado por la mediana |
| `users.city` | Nulos y sentinel `"?"` | 469 nulos (~11%) y 96 registros con `"?"` |
| `users.reg_date` | Fechas futuras (2026) | 40 registros |
| `usage.date` | Nulos | 50 registros (0.125%) |
| `usage.duration` / `usage.length` | Nulos estructurales según `type` | ~55% y ~44% |

**Perfil de clientes**

- Distribución de planes: **Básico ≈ 65% / Premium ≈ 35%**, estable en todos los rangos de edad.
- Consumo típico bajo a moderado: **mediana de 4 llamadas, 5 mensajes y ~19.8 minutos**.
- Las variables de uso tienen **sesgo a la derecha**, con pocos usuarios de consumo muy alto (hasta 15 llamadas, 17 mensajes y ~155.7 minutos).

**Recomendaciones**

- Campaña de *upgrade* para clientes de **Alto uso en plan Básico**.
- Plan de entrada más económico para el segmento de uso bajo.
- Plan intermedio o de bloques adicionales de minutos.
- Alertas para usuarios con consumo extremo.
- Validaciones de entrada para `age`, `city` y `reg_date`.
- Siguiente paso: cruzar los segmentos con `churn`.

---

## 🛠️ Tecnologías

- Python 3
- `pandas`, `numpy`
- `matplotlib`, `seaborn`
- Jupyter Notebook / Google Colab

---

## ▶️ Cómo ejecutar el notebook

### Google Colab 

1. Abre el notebook en Colab: **File → Open notebook → GitHub** y pega la URL de este repositorio.
2. Sube los tres archivos CSV al entorno de Colab, o móntalos desde Google Drive.
3. Ajusta las rutas de carga en la sección 1.1 si es necesario:

   ```python
   plans = pd.read_csv('/datasets/plans.csv')
   users = pd.read_csv('/datasets/users_latam.csv')
   usage = pd.read_csv('/datasets/usage.csv')
   ```

4. Ejecuta todas las celdas con **Runtime → Run all**.

---

## 🔁 Guía breve de reproducción

1. Cargar los tres datasets y verificar `.shape` e `.info()`.
2. Aplicar la limpieza del paso 3 **antes** de agregar el uso por usuario.
3. Construir `user_profile` uniendo `users` con `usage_agg` (`how="left"`).
4. Generar histogramas y boxplots a partir de `user_profile`.
5. Crear `grupo_uso` y `grupo_edad` con `np.select`.
6. Revisar las conclusiones del paso 7.

---

## 📁 Estructura del repositorio

```
.
├── README.md
└── notebooks/
    └── S7_Version-Estudiante-Project-ConnectaTel.ipynb
```

---

## ⚠️ Limitaciones

- Los datos de `users` y `usage` tienen nulos que se conservaron deliberadamente. Cualquier análisis por ciudad debe tomarlo en cuenta.
- El análisis es descriptivo. **No se analizó el churn**, por lo que no se pueden afirmar relaciones causales entre segmento y abandono.
- Los umbrales de segmentación (`grupo_uso` y `grupo_edad`) son reglas de negocio simples y pueden ajustarse.

---

## 👤 Autor

**[Angie Nuñez]**
GitHub: [@Angiegaleano07](https://github.com/tu-usuario)
