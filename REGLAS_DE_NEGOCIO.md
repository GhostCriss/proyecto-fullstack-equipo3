# Reglas de Negocio del Sistema

Este documento define las 28 reglas de negocio críticas, sus motivos e impactos técnicos, asegurando la integridad, consistencia y correcto flujo del sistema.

**1. Un cliente debe estar registrado para poder realizar un pedido**
* **Motivo:** No se permiten compras sin una cuenta activa.
* **Impacto técnico:** Validaciones de existencia del cliente; uso de claves foráneas o referencias en los documentos de pedidos.

**2. Un producto debe tener stock disponible para ser vendido**
* **Motivo:** No se pueden generar pedidos con inventario negativo.
* **Impacto técnico:** Actualización y validación del stock al confirmar el pedido.

**3. Todo pedido debe asociar uno o más productos**
* **Motivo:** Un pedido no puede estar vacío.
* **Impacto técnico:** Lógica de validación que exige al menos un elemento en el detalle del pedido.

**4. El total del pedido debe calcularse automáticamente**
* **Motivo:** Evita errores de manipulación del usuario y mantiene consistencia.
* **Impacto técnico:** Funciones internas que sumen cantidades por el precio unitario.

**5. No se puede modificar el precio histórico de un pedido ya realizado**
* **Motivo:** Resguardo de auditoría y contabilidad.
* **Impacto técnico:** El precio se copia y congela en el detalle del pedido al momento de la compra.

**6. Una entrega está vinculada a un único pedido**
* **Motivo:** Tracking individual por compra.
* **Impacto técnico:** Referencia uno a uno entre el registro de entrega y el pedido original.

**7. No se puede eliminar un cliente con pedidos asociados**
* **Motivo:** Regla contable e integridad del historial.
* **Impacto técnico:** Restricciones FK o validación por consulta antes de proceder con la eliminación.

**8. El estado del pedido sigue un flujo definido**
* **Motivo:** Control logístico.
* **Impacto técnico:** Transición estricta: PENDIENTE -> EN PROCESO -> ENVIADO -> ENTREGADO.

**9. El stock no puede volverse negativo bajo ninguna circunstancia**
* **Motivo:** Evitar ventas que el sistema no puede cumplir.
* **Impacto técnico:** Triggers de base de datos o validaciones estrictas antes de insertar registros.

**10. Los datos del cliente deben ser únicos (correo)**
* **Motivo:** Evitar cuentas duplicadas.
* **Impacto técnico:** Restricción UNIQUE en base de datos o índices únicos.

**11. Los productos deben tener un ID visible y único**
* **Motivo:** Facilitar identificación y trazabilidad.
* **Impacto técnico:** Campo generado automáticamente (ej: PROD-001) con índice único.

**12. Los productos pueden cambiar de estado (activo/inactivo)**
* **Motivo:** Permitir ocultar productos sin eliminarlos del sistema.
* **Impacto técnico:** Campo booleano de estado; los inactivos se ocultan de la tienda pero se mantienen en el historial.

**13. No se pueden crear productos duplicados (mismo nombre y categoría)**
* **Motivo:** Evitar confusión y mantener el catálogo limpio.
* **Impacto técnico:** Validación previa a la inserción o uso de índices compuestos.

**14. Los pedidos deben registrar qué productos llevó cada cliente**
* **Motivo:** Historial para auditoría y recomendaciones.
* **Impacto técnico:** Arreglo detallado de productos con precios y cantidades que perdura en el tiempo.

**15. El sistema debe mantener un historial de productos comprados por cliente**
* **Motivo:** Análisis de comportamiento y recomendaciones.
* **Impacto técnico:** Consultas agrupadas sobre los pedidos finalizados de cada usuario.

**16. No se puede eliminar un producto si está asociado a pedidos**
* **Motivo:** Proteger la integridad referencial y el historial de ventas.
* **Impacto técnico:** Solo se permite desactivar el producto mediante un cambio de estado, bloqueando la eliminación física.

**17. Los clientes pueden ser gestionados desde el panel admin**
* **Motivo:** Administración completa del sistema desde una interfaz centralizada.
* **Impacto técnico:** Formulario de creación, actualización y eliminación con validaciones de integridad.

**18. El teléfono debe ser solo numérico con validación**
* **Motivo:** Evitar errores de formato y facilitar la comunicación.
* **Impacto técnico:** Validación de patrones en frontend y verificación de tipo en backend.

**19. Los usuarios pueden cambiar y cerrar sesión**
* **Motivo:** Seguridad y privacidad de datos.
* **Impacto técnico:** Limpieza de almacenamiento local y sistema de gestión de tokens o sesiones.

**20. No se puede agregar al carrito más cantidad que el stock disponible**
* **Motivo:** Evitar pedidos rechazados y mala experiencia de usuario.
* **Impacto técnico:** Validación en tiempo real y límites máximos en la interfaz.

**21. Los pedidos tienen fecha límite de envío (1-2 días)**
* **Motivo:** Compromiso logístico.
* **Impacto técnico:** Cálculo automático de fechas y alertas para estados "RETRASADO".

**22. Los pedidos en proceso pueden ser cancelados**
* **Motivo:** Permitir correcciones por parte del cliente.
* **Impacto técnico:** Solo aplicable a estados "PENDIENTE" o "EN PROCESO", con devolución automática de stock.

**23. Sistema de reembolso para pedidos cancelados o con problemas**
* **Motivo:** Garantía del cliente.
* **Impacto técnico:** Colección de reembolsos vinculada al pedido, con flujo de estados (SOLICITADO -> APROBADO -> PROCESADO -> COMPLETADO).

**24. La sesión se cierra automáticamente al salir de la aplicación**
* **Motivo:** Seguridad de datos del usuario.
* **Impacto técnico:** Limpieza automática de datos de sesión al detectar cierre de ventana.

**25. Sistema de gestión de pagos**
* **Motivo:** Control financiero.
* **Impacto técnico:** Registro de transacciones con métodos de pago y estados definidos.

**26. Los pagos deben ser confirmados antes de procesar el pedido**
* **Motivo:** Evitar pérdidas.
* **Impacto técnico:** El pedido no avanza a "EN PROCESO" hasta recibir la confirmación de pago. Cancelación automática a las 24 horas.

**27. Sistema de descuentos y promociones**
* **Motivo:** Fidelización y marketing.
* **Impacto técnico:** Validación de códigos por vigencia, límites de uso y montos mínimos.

**28. Validaciones completas en frontend y backend**
* **Motivo:** Seguridad y experiencia de usuario.
* **Impacto técnico:** Sanitización de datos, mensajes de error descriptivos y doble validación de todos los inputs.
