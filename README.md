# Proyecto MongoDB - Tienda de Borojó

## Autor
Proyecto desarrollado para demostrar operaciones avanzadas en MongoDB con un caso de uso real de comercio electrónico.
Daniel Florez Cubides

## Descripción del Proyecto

Este proyecto implementa una base de datos MongoDB para una tienda especializada en productos derivados del borojó, una fruta tropical del Pacífico colombiano conocida por sus propiedades energéticas y nutricionales.

### Escenario

La tienda "Borojó Natural" maneja un inventario de productos derivados del borojó, desde frutas frescas hasta productos procesados como jugos, mermeladas, cosméticos y hasta cerveza artesanal. El sistema debe gestionar:

- **Productos**: Catálogo con diferentes categorías y características
- **Clientes**: Base de datos de compradores con preferencias
- **Ventas**: Registro de transacciones realizadas
- **Inventario**: Control de stock y lotes
- **Proveedores**: Información de suministradores

## Estructura de la Base de Datos

### Colecciones:
- `productos`: Catálogo de productos con precios, stock y tags
- `clientes`: Información de clientes y sus preferencias
- `ventas`: Registro de transacciones
- `inventario`: Control de stock por lotes
- `proveedores`: Información de suministradores

## Instrucciones de Ejecución

### 1. Inicialización
```bash
# Conectar a MongoDB
mongosh

# Usar la base de datos
use tienda_borojo

```

## Ejercicios Desarrollados

### 1. Operaciones CRUD

#### Inserción
- ✅ Insertar producto "Chocolatina de borojó"
```javascript
db.productos.insertOne({
  nombre: "Chocolatina de borojó",
  categoria: "Snack",
  precio: 4000,
  stock: 35,
  tags: ["dulce", "energía"],
});
```
![I1](image-2.png)
- ✅ Insertar cliente "Mario Mendoza"
```javascript
db.clientes.insertOne({
  nombre: "Mario Mendoza",
  email: "mario@email.com",
  compras: [],
  preferencias: ["energético", "natural"],
});
```

![I2](image-1.png)

#### Lectura
- ✅ Consultar productos con stock > 20
```javascript
db.productos.find({ stock: { $gt: 20 } });
```

![L1](image.png)
- ✅ Encontrar clientes sin compras
```javascript
db.clientes.find({ compras: { $size: 0 } });
```

![L2](image-3.png)

#### Actualización
- ✅ Aumentar stock de "Borojó deshidratado"
```javascript
db.productos.updateOne({ nombre: "Borojó deshidratado" }, { $inc: { stock: 10 } });
```

![A1](image-4.png)
- ✅ Añadir tag "bajo azúcar" a bebidas
```javascript
db.productos.updateMany({ categoria: "Bebida" }, { $addToSet: { tags: "bajo azúcar" } });
```

![A2](image-5.png)
#### Eliminación
- ✅ Eliminar cliente con email "juan@email.com"
```javascript
db.clientes.deleteOne({ email: "juan@email.com" });
```

![E1](image-7.png)
- ✅ Eliminar productos con stock < 5
```javascript
db.productos.deleteMany({ stock: { $lt: 5 } });
```
![E2](image-6.png)

### 2. Consultas con Expresiones Regulares
- ✅ Productos que empiecen por "Boro"
- ✅ Productos que contengan "con"
- ✅ Clientes con letra "z" en el nombre

### 3. Operadores en Arrays
- ✅ Clientes con preferencia "natural"
- ✅ Productos con tags "natural" y "orgánico"
- ✅ Productos con más de un tag

### 4. Aggregation Framework
- ✅ Productos más vendidos
- ✅ Clientes agrupados por cantidad de compras
- ✅ Ventas totales por mes
- ✅ Promedio de precios por categoría
- ✅ Top 3 productos con mayor stock

### 5. Funciones JavaScript (script.js)
- ✅ `calcularDescuento(precio, porcentaje)`
- ✅ `clienteActivo(idCliente)`
- ✅ `verificarStock(productoId, cantidadDeseada)`

### 6. Transacciones
- ✅ Simular venta con transacción
- ✅ Entrada de inventario con transacción
- ✅ Operación de devolución

### 7. Indexación
- ✅ Índice en campo `nombre` de productos
- ✅ Índice compuesto `categoria` y `precio`
- ✅ Índice en `email` de clientes
- ✅ Análisis con `explain()`

## Capturas de Pantalla de Consultas

### Consulta: Productos con stock > 20
```javascript
db.productos.find({stock: {$gt: 20}})
```
**Resultado**: 6 productos encontrados (Borojó fresco, Jugo de borojó, Mermelada de borojó, Galletas con borojó, etc.)

### Consulta: Clientes sin compras
```javascript
db.clientes.find({compras: {$size: 0}})
```
**Resultado**: 1 cliente encontrado (Mario Mendoza)

### Consulta: Productos más vendidos (Aggregation)
```javascript
db.ventas.aggregate([
  {$unwind: "$productos"},
  {$group: {_id: "$productos.productoId", totalVendido: {$sum: "$productos.cantidad"}}},
  {$sort: {totalVendido: -1}}
])
```
**Resultado**: Producto ID 2 (Jugo de borojó) con 3 unidades vendidas

### Consulta con Expresión Regular
```javascript
db.productos.find({nombre: /^Boro/})
```
**Resultado**: 2 productos (Borojó fresco, Borojó deshidratado)

### Análisis de Índice con explain()
```javascript
db.productos.find({nombre: "Borojó fresco"}).explain("executionStats")
```
**Resultado**: Muestra uso del índice \`nombre_1\` con \`IXSCAN\`

## Tecnologías Utilizadas
- **MongoDB 7.0+**
- **MongoDB Shell (mongosh)**
- **JavaScript** para funciones personalizadas
- **JSON** para datos de prueba

## Estructura de Archivos
```
tienda-borojo-mongodb/
├── README.md
├── data/
│   ├── productos.json
│   ├── clientes.json
│   ├── ventas.json
│   ├── inventario.json
│   └── proveedores.json
└── scripts/
    ├── 01-inicializacion.js
    ├── 02-crud-insercion.js
    ├── 03-crud-lectura.js
    ├── 04-crud-actualizacion.js
    ├── 05-crud-eliminacion.js
    ├── 06-expresiones-regulares.js
    ├── 07-operadores-arrays.js
    ├── 08-aggregation-framework.js
    ├── 09-transacciones.js
    ├── 10-indexacion.js
    └── script.js
```