# Proyecto Semestral: Equipo 3

## Microservicios y Repositorios
| Servicio | Encargado | Repositorio |
| :--- | :--- | :--- |
| Pagos-Service | Cristobal Ruiz | https://github.com/GhostCriss/pagos-service |
| Envios-Service | Cristobal Ruiz | https://github.com/GhostCriss/envio-service |
| Inventario-Service | Cristobal Ruiz | https://github.com/GhostCriss/inventario-service |
| Pedidos-Service | Cristobal Ruiz | https://github.com/GhostCriss/gestion-pedidos-service |
| Favoritos-Service | Josefa Lagos | https://github.com/josefalagos/favorito-service |
| Promociones-service | Josefa Lagos | https://github.com/josefalagos/promociones-service |
|  | Josefa Lagos |  |
| Carrito-service | Diego Sanzana | https://github.com/DIEGHOT/carrito-service |
|  | Diego Sanzana |  |
|  | Diego Sanzana |  |

## Despliegue Técnico


De acuerdo con los lineamientos establecidos para la **Modalidad Opción A (Descentralizada)**, el sistema se ha implementado bajo una arquitectura distribuida que garantiza la independencia operativa de cada componente.

### Infraestructura Cloud
* **Plataforma:** Se utilizan instancias independientes de **AWS EC2** ejecutando el sistema operativo **Ubuntu 24.04 LTS**.
* **Aislamiento:** Cada microservicio reside en una instancia dedicada, lo que permite una gestión de recursos y escalabilidad granular por cada miembro del equipo.

### Orquestación y Contenedores
La gestión del ciclo de vida de los servicios se realiza mediante **Docker Compose**, asegurando la paridad entre los entornos de desarrollo y producción.
El despliegue se ejecuta mediante el siguiente procedimiento técnico:
```bash
docker compose up -d --build

###  Matriz de Contratos Formales de la API
[cite_start]Los contratos HTTP definen las firmas de los endpoints remotos que actúan como proveedores de datos dentro de la red del ecosistema[cite: 1008, 1009]:

| Servicio Origen (Cliente) | Servicio Destino (Proveedor) | Encargado | Verbo | Ruta del Endpoint | Objeto Transportado (DTO) | Propósito del Flujo |
| :--- | :--- | :--- | :---: | :--- | :--- | :--- |
| **Pedidos** (:8086) | **Autenticación** (:8081) | Diego | `GET` | `/api/usuarios/{id}` | `UsuarioDTO` | [cite_start]Validar estado activo de cuenta del comprador[cite: 999, 1010]. |
| **Pedidos** (:8086) | **Inventario** (:8087) | Cristóbal | `POST` | `/api/inventario/descontar` | `StockRequestDTO` $\rightarrow$ `StockResponseDTO` | [cite_start]Validación de lote y descuento físico de existencias[cite: 999, 1010]. |
| **Pedidos** (:8086) | **Promociones** (:8085) | Josefa | `POST` | `/api/promociones/validar` | `CuponDTO` | [cite_start]Liquidar cupones de descuento activos sobre el total[cite: 999, 1010]. |
| **Pedidos** (:8086) | **Módulo de Pagos** (:8089) | Cristóbal | `POST` | `/api/pagos/procesar` | `PagoCreateDTO` $\rightarrow$ `PagoResponseDTO` | [cite_start]Transaccionar cargo bancario y visar estado financiero[cite: 1003, 1010]. |
| **Pedidos** (:8086) | **Envíos y Logística** (:8088) | Cristóbal | `POST` | `/api/envios/generar` | `EnvioRequestDTO` $\rightarrow$ `EnvioDTO` | [cite_start]Generar tracking de despacho post-pago confirmado[cite: 1004, 1010]. |
| **Post-venta** (:8090) | **Gestión de Pedidos** (:8086) | Cristóbal | `GET` | `/api/pedidos/{id}` | `PedidoHistorialDTO` | [cite_start]Asegurar inmutabilidad contable del cobro original[cite: 1007, 1010]. |
| **Post-venta** (:8090) | **Inventario** (:8087) | Cristóbal | `POST` | `/api/inventariodevolver/` | `RestaurarStockDTO` | [cite_start]Reincorporación física de productos devueltos a bodega[cite: 1007, 1010]. |
| **Carrito** (:8083) | **Catálogo** (:8082) | Diego | `GET` | `/api/productos/{id}/stock` | `ProductoStockDTO` | [cite_start]Comprobación en tiempo real previa a la pre-selección[cite: 984, 1010]. |

---

###  Escenario de Despliegue y Resiliencia Cloud
[cite_start]De acuerdo al diseño de infraestructura **Escenario B (Multi-Host VPC)**, los servicios interactúan cruzando la red interna de AWS mediante el mapeo dinámico de variables de entorno hacia IPs privadas fijas (Elastic IPs) de instancias dedicadas[cite: 334, 335, 383, 966].

#### Directivas de Tolerancia a Fallos e Integridad (application.properties)
[cite_start]Cada cliente declarativo Feign tiene inyectadas propiedades estrictas de tiempo límite (Timeouts) para mitigar el bloqueo indefinido de hilos de ejecución (*Thread Starvation*) y caídas en cascada ante interrupciones de red cloud[cite: 458, 621, 623, 1012, 1020]:

```properties
# Tiempos Límites para Conexiones Distribuidas (Milisegundos)
spring.cloud.openfeign.client.config.default.connectTimeout=3000
spring.cloud.openfeign.client.config.default.readTimeout=5000
[cite_start]
