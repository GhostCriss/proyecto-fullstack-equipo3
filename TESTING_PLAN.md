## Pruebas Unitarias y Cobertura de Reglas de Negocio

Este documento detalla las reglas de negocio invariables de nuestro sistema, el estado actual de las pruebas automatizadas y la deuda técnica asumida por cada integrante del equipo. 

>  **Nota:** Las reglas del Negocio estan en el archivo: [REGLAS_DE_NEGOCIO.md](./REGLAS_DE_NEGOCIO.md).

---

### 1. Cristóbal Ruiz - Microservicio de Inventario
**Repositorio a evaluar:** [Inventario-Service](https://github.com/GhostCriss/inventario-service)

#### Reglas de Negocio Críticas
1. **Validación de Stock Negativo**: El stock no puede volverse negativo bajo ninguna circunstancia.
2. **Productos Duplicados**: No se pueden crear productos duplicados (mismo nombre y categoría).
3. **Visibilidad de ID**: Los productos deben tener un ID visible y único.

#### Cobertura Actual
| Regla | Estado | Casos Cubiertos |
|---|---|---|
| 1. Validación de Stock Negativo | ✅ Cubierta | Stock suficiente (feliz), Stock 0 (error) |
| 2. Productos Duplicados | ⚠️ Pendiente | Solo caso feliz de creación |
| 3. Visibilidad de ID | ⚠️ Pendiente | Falta test de validación de formato de ID |

#### Reflexión y Deuda Técnica
* **Riesgo sin probar**: La creación de productos duplicados no tiene test de caso de error.
* **Acción Futura**: Agregar test verificando la excepción al enviar el mismo nombre y categoría al endpoint de creación.
* **Responsable**: Cristóbal Ruiz

---

### 2. Diego Sanzana - Microservicio de Autenticación
**Repositorio a evaluar:** [Autenticacion-Service](https://github.com/DIEGHOT/autenticacion-service)

#### Reglas de Negocio Críticas
1. **Registro Obligatorio**: Un cliente debe estar registrado para poder realizar un pedido.
2. **Unicidad de Correo**: Los datos del cliente deben ser únicos (correo) para evitar cuentas duplicadas.
3. **Cierre de Sesión**: La sesión se cierra automáticamente al salir de la aplicación.

#### Cobertura Actual
| Regla | Estado | Casos Cubiertos |
|---|---|---|
| 1. Registro Obligatorio | ✅ Cubierta | Validación de registro y login exitoso |
| 2. Unicidad de Correo | ✅ Cubierta | Verificación de exclusión de duplicados |
| 3. Cierre de Sesión | ✅ Cubierta | Validaciones de integridad de sesión |

#### Reflexión y Deuda Técnica
* **Estado**: Las pruebas unitarias fueron ejecutadas con éxito (BUILD SUCCESS).
* **Acción Futura**: Mantener la cobertura al implementar nuevas funcionalidades de seguridad.
* **Responsable**: Diego Sanzana
---

### 3. Josefa Lagos - Microservicio [POR DEFINIR]
**Repositorio a evaluar:** [Link pendiente]

#### Reglas de Negocio Críticas
1. **[Regla 1]**: [Descripción pendiente]
2. **[Regla 2]**: [Descripción pendiente]
3. **[Regla 3]**: [Descripción pendiente]

#### Cobertura Actual
| Regla | Estado | Casos Cubiertos |
|---|---|---|
| 1. [Regla 1] | ⚠️ Pendiente | [Caso pendiente] |
| 2. [Regla 2] | ⚠️ Pendiente | [Caso pendiente] |
| 3. [Regla 3] | ⚠️ Pendiente | [Caso pendiente] |

#### Reflexión y Deuda Técnica
* **Riesgo sin probar**: [Riesgo pendiente]
* **Acción Futura**: [Acción pendiente]
* **Responsable**: Josefa Lagos
