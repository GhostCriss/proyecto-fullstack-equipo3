# Proyecto Semestral: Equipo 3

## Microservicios y Repositorios
| Servicio | Encargado | Repositorio |
| :--- | :--- | :--- |
| Pagos-Service | Cristobal Ruiz | https://github.com/GhostCriss/pagos-service |
| Envios-Logistica | Cristobal Ruiz | https://github.com/GhostCriss/envio-service |
| Inventario-Service | Cristobal Ruiz | https://github.com/GhostCriss/inventario-service |
| Gestión-Pedidos | Cristobal Ruiz | https://github.com/GhostCriss/gestion-pedidos-service |
| Favoritos-Service | Josefa Lagos | https://github.com/josefalagos/favorito-service |
| Promociones-service | Josefa Lagos | https://github.com/josefalagos/promociones-service |
| Carrito-service | Diego Sanzana | https://github.com/DIEGHOT/carrito-service |

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

