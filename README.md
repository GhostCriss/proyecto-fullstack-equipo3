# Proyecto Semestral: Equipo 3

## Microservicios y Repositorios
| Servicio | Encargado | Repositorio |
| :--- | :--- | :--- |
| API-gateway|   |https://github.com/GhostCriss/api-gateway|
| Pagos-Service | Cristobal Ruiz | https://github.com/GhostCriss/pagos-service |
| Envios-Service | Cristobal Ruiz | https://github.com/GhostCriss/envio-service |
| Inventario-Service | Cristobal Ruiz | https://github.com/GhostCriss/inventario-service |
| Pedidos-Service | Cristobal Ruiz | https://github.com/GhostCriss/gestion-pedidos-service |
| Favoritos-Service | Josefa Lagos | https://github.com/josefalagos/favorito-service |
| Promociones-service | Josefa Lagos | https://github.com/josefalagos/promociones-service |
| Reembolsos-service | Josefa Lagos | https://github.com/josefalagos/post-ventas_y_rembolsos-service |
| Carrito-service | Diego Sanzana | https://github.com/DIEGHOT/carrito-service |
| Catalogo-service | Diego Sanzana | https://github.com/DIEGHOT/catalogo-service |
| Autenticacion-Service | Diego Sanzana | https://github.com/DIEGHOT/autenticacion-service |

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
```
# Comunicación entre Microservicios (Hito 2)

### Diagrama de Dependencias y Flujos del Dominio
El ecosistema de Amazorg está dividido en tres zonas funcionales autónomas distribuidas por instancias dedicadas en la nube[cite: 973, 1011]:

```text
       ZONA B: EXPERIENCIA Y ACCESO (Diego)
      ┌─────────────────────────────────────┐
      │  ┌───────────┐       ┌───────────┐  │
      │  │ Catálogo  │◀──────│  Carrito  │  │
      │  │  (:8082)  │  GET  │  (:8083)  │  │
      │  └───────────┘       └─────┬─────┘  │
      └────────────────────────────┼────────┘
                                   │ Gatilla
                                   ▼ Compra
┌────────────────────────────────────────────────────────────────────────┐
│ ZONA A: NÚCLEO TRANSACCIONAL E INFRAESTRUCTURA BASE (Cristóbal)        │
│                                                                        │
│               ┌──────────────────────────────┐                         │
│   ┌──────────▶│  Gestión de Pedidos (:8086)  │◀────────────┐           │
│   │   GET     │      [Orquestador Core]      │   GET       │           │
│   │           └─┬───┬────────────┬────┬────┬──┘             │           │
│   │             │   │            │    │    │               │           │
│   │         POST│   │POST    POST│    │POST│POST           │           │
│   │             ▼   ▼            ▼    ▼    ▼               │           │
│   │   ┌───────────┐┌───────────┐┌──────┐┌──────────┐       │           │
│   │   │Inventario ││Autentica  ││Pagos ││Envíos    │       │           │
│   │   │  (:8087)  ││  (:8081)  ││(:8089)│(:8088)   │       │           │
│   │   └─────▲─────┘└───────────┘└──────┘└──────────┘       │           │
│   │         │                                              │           │
└───┼─────────┼──────────────────────────────────────────────┼───────────┘
    │         │ POST (Restaurar)                             │
    │         │                                              │
┌───┴─────────┴──────────────────────────────────────────────┴───────────┐
│ ZONA C: MARKETING Y POST-VENTA (Josefa)                                │
│                                                                        │
│  ┌──────────────────────────┐               ┌───────────────────────┐  │
│  │  Post-venta y Reembolsos  │─────────────▶│Promociones/Descuentos │  │
│  │          (:8090)         │     POST      │        (:8085)        │  │
│  └──────────────────────────┘               └───────────────────────┘  │
└────────────────────────────────────────────────────────────────────────┘
```

###  Matriz de Contratos Formales de la API
Los contratos HTTP definen las firmas de los endpoints remotos que actúan como proveedores de datos dentro de la red del ecosistema[cite: 1008, 1009]:

| Servicio Origen (Cliente) | Servicio Destino (Proveedor) | Encargado | Verbo | Ruta del Endpoint | Objeto Transportado (DTO) | Propósito del Flujo |
| :--- | :--- | :--- | :---: | :--- | :--- | :--- |
| **Pedidos** (:8086) | **Autenticación** (:8081) | Diego | `GET` | `/api/usuarios/{id}` | `UsuarioDTO` | Validar estado activo de cuenta del comprador[cite: 999, 1010]. |
| **Pedidos** (:8086) | **Inventario** (:8087) | Cristóbal | `POST` | `/api/inventario/descontar` | `StockRequestDTO` $\rightarrow$ `StockResponseDTO` | Validación de lote y descuento físico de existencias[cite: 999, 1010]. |
| **Pedidos** (:8086) | **Promociones** (:8085) | Josefa | `POST` | `/api/promociones/validar` | `CuponDTO` | Liquidar cupones de descuento activos sobre el total[cite: 999, 1010]. |
| **Pedidos** (:8086) | **Módulo de Pagos** (:8089) | Cristóbal | `POST` | `/api/pagos/procesar` | `PagoCreateDTO` $\rightarrow$ `PagoResponseDTO` | Transaccionar cargo bancario y visar estado financiero[cite: 1003, 1010]. |
| **Pedidos** (:8086) | **Envíos y Logística** (:8088) | Cristóbal | `POST` | `/api/envios/generar` | `EnvioRequestDTO` $\rightarrow$ `EnvioDTO` | Generar tracking de despacho post-pago confirmado[cite: 1004, 1010]. |
| **Post-venta** (:8090) | **Gestión de Pedidos** (:8086) | Cristóbal | `GET` | `/api/pedidos/{id}` | `PedidoHistorialDTO` | Asegurar inmutabilidad contable del cobro original[cite: 1007, 1010]. |
| **Post-venta** (:8090) | **Inventario** (:8087) | Cristóbal | `POST` | `/api/inventariodevolver/` | `RestaurarStockDTO` | Reincorporación física de productos devueltos a bodega[cite: 1007, 1010]. |
| **Carrito** (:8083) | **Catálogo** (:8082) | Diego | `GET` | `/api/productos/{id}/stock` | `ProductoStockDTO` | Comprobación en tiempo real previa a la pre-selección[cite: 984, 1010]. |

---

###  Escenario de Despliegue y Resiliencia Cloud
De acuerdo al diseño de infraestructura **Escenario B (Multi-Host VPC)**, los servicios interactúan cruzando la red interna de AWS mediante el mapeo dinámico de variables de entorno hacia IPs privadas fijas de instancias dedicadas[cite: 334, 335, 383, 966].

#### Directivas de Tolerancia a Fallos e Integridad (application.properties)
Cada cliente declarativo Feign tiene inyectadas propiedades estrictas de tiempo límite (Timeouts) para mitigar el bloqueo indefinido de hilos de ejecución y caídas en cascada ante interrupciones de red cloud[cite: 458, 621, 623, 1012, 1020]:

```properties
# Tiempos Límites para Conexiones Distribuidas (Milisegundos)
spring.cloud.openfeign.client.config.default.connectTimeout=3000
spring.cloud.openfeign.client.config.default.readTimeout=5000
[cite_start]
```
###  Muestra de funcionalidad con Videos

Microservicios de Cristobal: https://youtu.be/q8TV3921SYs

Microservicios de diego :https://youtu.be/kW6OHXu-Grw

Microservicios de Josefa: https://youtu.be/I8EwQusZzdk
