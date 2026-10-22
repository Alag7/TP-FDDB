# TP-FDDB

## Trabajo Práctico Final: Sistema de Gestión de Pedidos, Inventario y Clientes

**Materia:** Fundamentos de Desarrollo Backend, UTN FRSF, Ingeniería en Sistemas de Información
**Entrega:** martes 24 de noviembre · **Coloquio (tentativo):** lunes 30 de noviembre

### Descripción

Backend de **TiendaOnline** separado en tres microservicios independientes, cada uno con su propia base de datos, desarrollado con Java + Spring Boot.

| Servicio | Responsabilidad |
|---|---|
| `servicio-clientes` | Datos de clientes |
| `servicio-inventario` | Stock y reservas |
| `servicio-pedidos` | Pedidos y su ciclo de vida |

### Funcionalidad por servicio

**servicio-clientes**
- Registrar un cliente.
- Consultar un cliente por id. Los campos `nombre` y `email` (PII) se enmascaran con asteriscos (ej. `J*** P***`, `****@****.com`) según la feature flag `mask-pii`, evaluada en runtime. Con el flag inactivo se devuelven sin enmascarar.
- Listar los pedidos de un cliente (consulta síncrona a `servicio-pedidos`).

**servicio-inventario**
- Consultar disponibilidad de stock de un producto.
- Reservar unidades para un pedido (se rechaza si no hay stock suficiente).
- Liberar una reserva existente.

**servicio-pedidos**
- Crear un pedido: verifica que el cliente exista (`servicio-clientes`), reserva el stock de cada ítem (`servicio-inventario`) y calcula el monto total. Si el cliente no existe o no hay stock, el pedido no se crea y se devuelve el error correspondiente.
- Consultar un pedido por id.
- Listar los pedidos de un cliente (consumido por `servicio-clientes`).
- Cancelar un pedido en estado `CREADO` o `CONFIRMADO`, liberando la reserva de stock asociada.

### Proceso por lotes (Spring Batch)

Job nocturno que recorre los pedidos en estado `CREADO` con más de 24 hs sin confirmar, los pasa a `EXPIRADO`, libera la reserva en `servicio-inventario` y genera un resumen de la corrida. Incluye al menos un Step de tipo chunk (reader / processor / writer).

### Modelo de datos (campos mínimos)

| Servicio | Entidad | Campos |
|---|---|---|
| clientes | Cliente | id, nombre (PII), email (PII), fechaAlta |
| inventario | Producto | id, nombre, cantidadDisponible |
| inventario | Reserva | id, producto, pedido, cantidad, estado (`ACTIVA`, `LIBERADA`) |
| pedidos | Pedido | id, clienteId, estado (`CREADO`, `CONFIRMADO`, `CANCELADO`, `EXPIRADO`), montoTotal, fechaCreacion, fechaActualizacion |
| pedidos | ItemPedido | id, pedido, producto, cantidad, precioUnitario |

Todos los ids son enteros autoincrementales.

### Requisitos técnicos

- Java + Spring Boot, mínimo 3 microservicios, una base de datos por servicio.
- API REST documentada con OpenAPI/Swagger, con validaciones y manejo de errores consistente.
- Migraciones con **Flyway** (`ddl-auto=update` no está permitido).
- **Spring Batch** para la expiración de pedidos.
- **Feature flags (Statsig)** para `mask-pii`, evaluado en runtime.
- **Tests unitarios** con Mockito.
- **Tests de integración** con MockServer (simulación de llamadas entre servicios) y `@SpringBootTest`.
- Desarrollo guiado por tests (TDD).
- **Observabilidad:** como mínimo observar y buscar logs, y seguir traces. Extras son bonus.

### Cómo ejecutar

<!-- Completar: prerrequisitos, comando para levantar servicios/DBs, puertos -->

### URLs útiles

<!-- Completar: Swagger de cada servicio, herramienta de logs/traces -->

### Integrantes

<!-- Completar -->

### Decisiones de diseño

<!-- Completar o enlazar al documento de entrega -->