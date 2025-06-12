# Análisis de la Plataforma E-Commerce Olist

## Objetivo del Proyecto

El objetivo era analizar datos de múltiples tablas de una base de datos MySQL para entender patrones de compra, geografía de clientes y rendimiento de productos, demostrando la capacidad de integrar SQL y Python para análisis de negocio.

## Arquitectura del Proyecto

```
Múltiples Archivos CSV (9 tablas)
    ↓
Script de Python (pandas + SQLAlchemy)
    ↓
Base de Datos MySQL (olist_db)
    ↓
Consultas SQL con JOINs
    ↓
DataFrame de Pandas
    ↓
Análisis y Visualización en Power BI
```

## Proceso Técnico Detallado

### 1. Creación de la Base de Datos
Se utilizó Python y SQLAlchemy para crear una base de datos MySQL (`olist_db`) y poblarla dinámicamente a partir de 9 archivos CSV. El proceso automatizado lee cada archivo CSV y crea las tablas correspondientes en MySQL.

```python
# Configuración de conexión a MySQL
user = 'root'
password = 'yaris'
host = '127.0.0.1'
database = 'olist_db'

connection_string = f"mysql+mysqlconnector://{user}:{password}@{host}/{database}"
engine = create_engine(connection_string)

# Carga automática de archivos CSV a MySQL
for filename in os.listdir(dataset_path):
    if filename.endswith('.csv'):
        file_path = os.path.join(dataset_path, filename)
        df = pd.read_csv(file_path)
        table_name = filename.replace('olist_', '').replace('_dataset.csv', '')
        df.to_sql(table_name, con=engine, if_exists='replace', index=False)
```

### 2. Consulta y Unión de Datos (SQL)
El núcleo del proyecto fue la extracción de datos mediante consultas SQL. La consulta principal unió las tablas `orders`, `customers` y `order_items` para crear una vista consolidada de los pedidos.

```sql
SELECT
    o.order_id,
    o.order_status,
    o.order_purchase_timestamp,
    oi.product_id,
    oi.price,
    oi.freight_value,
    c.customer_unique_id,
    c.customer_city,
    c.customer_state
FROM
    orders AS o
LEFT JOIN
    customers AS c ON o.customer_id = c.customer_id
LEFT JOIN
    order_items AS oi ON o.order_id = oi.order_id
```

### 3. Análisis y Exportación (Python/Pandas)
Una vez los datos fueron cargados en un DataFrame de Pandas, se realizó un análisis exploratorio inicial y se exportó el dataset limpio a un archivo CSV (`olist_analisis.csv`) para su uso en Power BI.

```python
# Análisis exploratorio
conteo_por_estado = df_pedidos_clientes['customer_state'].value_counts()
print("Top 10 estados por número de pedidos")
print(conteo_por_estado.head(10))

# Visualización con matplotlib y seaborn
plt.figure(figsize=(15, 8))
sns.barplot(x=conteo_por_estado.index, y=conteo_por_estado.values, 
           hue=conteo_por_estado.index, palette='viridis', legend=False)
plt.title('Número de Pedidos por Estado en Olist', fontsize=16)

# Exportación para Power BI
df_para_powerbi.to_csv('olist_analisis.csv', index=False)
```

## Estructura de Datos

El proyecto trabaja con las siguientes tablas principales:
- **orders**: Información de pedidos y fechas
- **customers**: Datos geográficos de clientes
- **order_items**: Detalles de productos y precios
- **products**: Información de productos
- **sellers**: Datos de vendedores
- **order_payments**: Métodos de pago
- **order_reviews**: Calificaciones y reseñas

## Tecnologías Utilizadas

- **Python**: Procesamiento de datos y automatización
- **Pandas**: Manipulación y análisis de DataFrames
- **SQLAlchemy**: ORM para conexión con MySQL
- **MySQL**: Base de datos relacional
- **Matplotlib/Seaborn**: Visualizaciones en Python
- **Power BI**: Dashboard interactivo final

## Estructura del Proyecto

```
olist_project/
├── analisis_olist_sql.ipynb    # Notebook principal con análisis
├── olist_analisis.csv          # Dataset limpio exportado
├── README.md                   # Este archivo
└── olist_dataset/              # Archivos CSV originales
    ├── olist_customers_dataset.csv
    ├── olist_orders_dataset.csv
    ├── olist_order_items_dataset.csv
    ├── olist_products_dataset.csv
    ├── olist_sellers_dataset.csv
    ├── olist_order_payments_dataset.csv
    ├── olist_order_reviews_dataset.csv
    ├── olist_geolocation_dataset.csv    └── product_category_name_translation.csv
```

## Dashboard Interactivo

*[Aquí se incluirá una captura de pantalla del dashboard de Power BI una vez completado]*

El dashboard final permite explorar interactivamente:
- Distribución geográfica de pedidos por estado
- Tendencias temporales de ventas
- Análisis de productos más vendidos
- Métricas de rendimiento por región

## Cómo Ejecutar el Proyecto

1. **Instalar dependencias**:
   ```bash
   pip install pandas sqlalchemy mysql-connector-python matplotlib seaborn
   ```

2. **Configurar MySQL**:
   - Instalar MySQL Server
   - Crear usuario y configurar credenciales

3. **Ejecutar el notebook**:
   - Abrir `analisis_olist_sql.ipynb`
   - Actualizar credenciales de MySQL en las celdas correspondientes
   - Ejecutar todas las celdas secuencialmente

4. **Importar en Power BI**:
   - Abrir Power BI Desktop
   - Importar el archivo `olist_analisis.csv`   - Crear visualizaciones interactivas

## Insights Principales

- **São Paulo (SP)** lidera significativamente en número de pedidos
- Los estados del sur y sureste de Brasil concentran la mayor actividad
- El análisis temporal revela patrones estacionales en las compras
- La integración SQL-Python permite análisis eficientes de grandes volúmenes de datos

---

**Desarrollado por**: [Tu Nombre]  
**Fecha**: Junio 2025  
**Tecnologías**: Python, SQL, MySQL, Power BI
