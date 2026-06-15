## Pruebas Unitarias y Cobertura de Reglas de Negocio

Este documento detalla las reglas de negocio invariables de nuestro sistema, el estado actual de las pruebas automatizadas y la deuda técnica asumida por cada integrante del equipo.

---

### 1. Cristóbal Ruiz - Microservicios de Inventario y Pedidos

#### Reglas de Negocio Críticas
1. **Validación de Stock Negativo**: El stock no puede volverse negativo bajo ninguna circunstancia[cite: 12].
2. **Productos Duplicados**: No se pueden crear productos duplicados (mismo nombre y categoría)[cite: 12].
3. **Flujo de Pedido**: El estado del pedido sigue un flujo definido (PENDIENTE -> EN PROCESO -> ENVIADO -> ENTREGADO)[cite: 12].

#### Cobertura Actual
| Regla | Estado | Casos Cubiertos |
|---|---|---|
| 1. Validación de Stock Negativo | ✅ Cubierta | Stock suficiente (feliz), Stock 0 (error) |
| 2. Productos Duplicados | ⚠️ Pendiente | Solo caso feliz de creación |
| 3. Flujo de Pedido | ⚠️ Pendiente | Sin pruebas de transición de estado |

#### Reflexión y Deuda Técnica
* **Riesgo sin probar**: La creación de productos duplicados no tiene test de caso de error.
* **Acción Futura**: Agregar test verificando la excepción al enviar el mismo nombre y categoría al endpoint de creación.
* **Responsable**: Cristóbal Ruiz

---

### 2. Diego Sanzana - Microservicios de Carrito y Autenticación

#### Reglas de Negocio Críticas
1. **Límite de Carrito**: No se puede agregar al carrito más cantidad que el stock disponible[cite: 12].
2. **Registro Obligatorio**: Un cliente debe estar registrado para poder realizar un pedido[cite: 12].
3. **Unicidad de Correo**: Los datos del cliente deben ser únicos (correo) para evitar cuentas duplicadas[cite: 12].

#### Cobertura Actual
| Regla | Estado | Casos Cubiertos |
|---|---|---|
| 1. Límite de Carrito | ✅ Cubierta | Agregar cantidad dentro del límite permitido |
| 2. Registro Obligatorio | ⚠️ Pendiente | Validaciones pendientes en el Controlador |
| 3. Unicidad de Correo | ⚠️ Pendiente | Falta test de error por registro con correo existente |

#### Reflexión y Deuda Técnica
* **Riesgo sin probar**: Un usuario no registrado podría intentar hacer un bypass del checkout.
* **Acción Futura**: Implementar pruebas unitarias en el filtro de seguridad y en el servicio de autenticación.
* **Responsable**: Diego Sanzana

---

### 3. Josefa Lagos - Microservicios de Promociones y Reembolsos

#### Reglas de Negocio Críticas
1. **Vigencia de Promociones**: Los descuentos tienen validación de vigencia y límites de uso[cite: 12].
2. **Sistema de Reembolsos**: Existe un sistema de reembolso para pedidos cancelados, vinculado al pedido original[cite: 12].
3. **Cancelación Válida**: Los pedidos en proceso pueden ser cancelados solo si su estado es "PENDIENTE" o "EN PROCESO"[cite: 12].

#### Cobertura Actual
| Regla | Estado | Casos Cubiertos |
|---|---|---|
| 1. Vigencia de Promociones | ✅ Cubierta | Descuento activo aplicado correctamente |
| 2. Sistema de Reembolsos | ⚠️ Pendiente | Solo creación de la solicitud de reembolso |
| 3. Cancelación Válida | ⚠️ Pendiente | Falta prueba de error al intentar cancelar un pedido ENVIADO |

#### Reflexión y Deuda Técnica
* **Riesgo sin probar**: Aplicación exitosa de códigos de descuentos expirados o con su límite de usos agotado.
* **Acción Futura**: Desarrollar asserts (MockMvc) para confirmar el rechazo HTTP 400 de cupones inválidos.
* **Responsable**: Josefa Lagos
