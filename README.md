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
```javascript
db.productos.find({ nombre: /^Boro/ });
```

![ER1](image-8.png)
- ✅ Productos que contengan "con"
```javascript
db.productos.find({ nombre: /con/i });
```

![ER2](image-9.png)
- ✅ Clientes con letra "z" en el nombre
```javascript
db.clientes.find({ nombre: /z/i });
```

![ER3](image-10.png)
### 3. Operadores en Arrays
- ✅ Clientes con preferencia "natural"
```javascript
db.clientes.find({ preferencias: {$in: ["natural"]} });
```

![OA1](image-11.png)
- ✅ Productos con tags "natural" y "orgánico"
```javascript
db.productos.find({tags: { $all: ["natural", "orgánico"] }});
```

![OA2](image-12.png)
- ✅ Productos con más de un tag
```javascript
db.productos.find({$expr: { $gt: [{ $size: "$tags" }, 1] }});
```

![OA3](image-13.png)
### 4. Aggregation Framework
- ✅ Productos más vendidos
```javascript
db.ventas.aggregate([
  { $unwind: "$productos" },
  {
    $group: {
      _id: "$productos.productoId",
      totalVendido: { $sum: "$productos.cantidad" },
      ventasCount: { $sum: 1 }
    }
  },
  {
    $lookup: {
      from: "productos",
      localField: "_id",
      foreignField: "_id",
      as: "infoProducto"
    }
  },
  { $unwind: "$infoProducto" },
  {
    $project: {
      nombre: "$infoProducto.nombre",
      categoria: "$infoProducto.categoria",
      totalVendido: 1,
      ventasCount: 1,
      ingresoTotal: { $multiply: ["$totalVendido", "$infoProducto.precio"] }
    }
  },
  { $sort: { totalVendido: -1 } }
]);
```

![AF1](image-14.png)
- ✅ Clientes agrupados por cantidad de compras
```javascript
db.clientes.aggregate([
  {
    $project: {
      nombre: 1,
      email: 1,
      cantidadCompras: { $size: "$compras" },
      preferencias: 1
    }
  },
  {
    $group: {
      _id: "$cantidadCompras",
      clientes: {
        $push: {
          nombre: "$nombre",
          email: "$email",
          preferencias: "$preferencias"
        }
      },
      totalClientes: { $sum: 1 }
    }
  },
  { $sort: { _id: -1 } }
]);
```

![AF2](image-15.png)
- ✅ Ventas totales por mes
```javascript
db.ventas.aggregate([
  {
    $group: {
      _id: {
        año: { $year: "$fecha" },
        mes: { $month: "$fecha" }
      },
      totalVentas: { $sum: "$total" },
      cantidadVentas: { $sum: 1 },
      promedioVenta: { $avg: "$total" }
    }
  },
  { $sort: { "_id.año": 1, "_id.mes": 1 } }
]);
```

![AF3](image-16.png)
- ✅ Promedio de precios por categoría
```javascript
db.productos.aggregate([
  {
    $group: {
      _id: "$categoria",
      precioPromedio: { $avg: "$precio" },
      precioMinimo: { $min: "$precio" },
      precioMaximo: { $max: "$precio" },
      cantidadProductos: { $sum: 1 },
      stockTotal: { $sum: "$stock" }
    }
  },
  { $sort: { precioPromedio: -1 } }
]);
```

![AF4](image-17.png)
- ✅ Top 3 productos con mayor stock
```javascript
db.productos.aggregate([
  { $sort: { stock: -1 } },
  { $limit: 3 },
  {
    $project: {
      nombre: 1,
      categoria: 1,
      stock: 1,
      precio: 1,
      valorInventario: { $multiply: ["$stock", "$precio"] },
      tags: 1
    }
  }
]);
```

![AF5](image-18.png)
### 5. Funciones definidas
- ✅ `calcularDescuento(precio, porcentaje)`
```javascript
function calcularDescuento(precio, porcentaje) {
  if (typeof precio !== "number" || typeof porcentaje !== "number") {
    throw new Error("Los parámetros deben ser números");
  }
  
  if (precio < 0 || porcentaje < 0 || porcentaje > 100) {
    throw new Error("Precio debe ser positivo y porcentaje entre 0-100");
  }
  
  const descuento = (precio * porcentaje) / 100;
  const precioFinal = precio - descuento;
  
  return {
    precioOriginal: precio,
    porcentajeDescuento: porcentaje,
    montoDescuento: descuento,
    precioFinal: precioFinal,
    ahorroTotal: descuento
  };
}
```

![FD1](image-20.png)
- ✅ `clienteActivo(idCliente)`
```javascript
function clienteActivo(idCliente) {
  if (typeof idCliente !== "number") {
    throw new Error("El ID del cliente debe ser un número");
  }
  
  const cliente = db.clientes.findOne({ _id: idCliente });
  
  if (!cliente) {
    return {
      existe: false,
      activo: false,
      mensaje: `Cliente con ID ${idCliente} no encontrado`
    };
  }
  
  const cantidadCompras = cliente.compras.length;
  const esActivo = cantidadCompras > 3;
  
  return {
    existe: true,
    clienteId: idCliente,
    nombre: cliente.nombre,
    email: cliente.email,
    cantidadCompras: cantidadCompras,
    activo: esActivo,
    mensaje: esActivo 
      ? `Cliente ${cliente.nombre} es ACTIVO (${cantidadCompras} compras)`
      : `Cliente ${cliente.nombre} no es activo (${cantidadCompras} compras, necesita más de 3)`
  };
}
```

![FD2](image-19.png)
- ✅ `verificarStock(productoId, cantidadDeseada)`
```javascript
function verificarStock(productoId, cantidadDeseada) {
  if (typeof productoId !== "number" || typeof cantidadDeseada !== "number") {
    throw new Error("Los parámetros deben ser números");
  }
  
  if (cantidadDeseada <= 0) {
    throw new Error("La cantidad deseada debe ser mayor a 0");
  }
  
  const producto = db.productos.findOne({ _id: productoId });
  
  if (!producto) {
    return {
      existe: false,
      disponible: false,
      mensaje: `Producto con ID ${productoId} no encontrado`
    };
  }
  
  const stockDisponible = producto.stock;
  const hayStock = stockDisponible >= cantidadDeseada;
  
  return {
    existe: true,
    productoId: productoId,
    nombre: producto.nombre,
    stockActual: stockDisponible,
    cantidadSolicitada: cantidadDeseada,
    disponible: hayStock,
    faltante: hayStock ? 0 : cantidadDeseada - stockDisponible,
    mensaje: hayStock 
      ? `Stock suficiente: ${stockDisponible} disponibles, solicita ${cantidadDeseada}`
      : `Stock insuficiente: ${stockDisponible} disponibles, solicita ${cantidadDeseada}, faltan ${cantidadDeseada - stockDisponible}`
  };
}
```

![FD3](image-21.png)
### 6. Transacciones
- ✅ Simular venta con transacción
- ✅ Entrada de inventario con transacción
- ✅ Operación de devolución

### 7. Indexación
- ✅ Índice en campo `nombre` de productos
- ✅ Índice compuesto `categoria` y `precio`
- ✅ Índice en `email` de clientes
- ✅ Análisis con `explain()`


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