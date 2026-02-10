# Atributos de calidad

Atributos de calidad investigados en el libro: Designing Software Architectures: A Practical Approach, 2nd Edition.

|No.|Nombre inglés|Nombre español|
|---|---|---|
|1|Performance|Rendimiento|
|2|Security|Seguridad|
|3|Scalability|Escalabilidad|
|4|Reliability|Confiabilidad|
|5|Usability|Usabilidad|
|6|Maintainability|Mantenibilidad|
|7|Availability|Disponibilidad|
|8|Interoperability|Interoperabilidad|
|9|Modifiability|Modificabilidad|
|10|Testability|Capacidad de prueba|
---

## Atributos de calidad seleccionados a priori para el proyecto
En primera instancia, se seleccionaron los siguiente atributos de calidad:

* PERFORMANCE (latencia en flujos críticos: órdenes -> cocina/barra, cobro y sincronización offline)
* SECURITY (roles/permisos y datos financieros restringidos)
* USABILITY (múltiples roles y dispositivos)
* AVAILABILITY (operación L-S 8AM-10PM en 5 sucursales)
* RELIABILITY (modo offline obligatorio y sincronización sin pérdida de datos)
* MODIFIABILITY (desarrollo incremental y extensibilidad futura)

# Creación de escenarios
Una vez que se han identificado los atributos de calidad relevantes para el proyecto, alineadas a los objetivos y necesidades del negocio, se procede a la creación de los escenarios con el formato de 6 partes:
1. Estímulo
2. Origen del estimulo
3. Entorno
4. Artefacto
5. Respuesta del sistema
6. Medida de la respuesta

## PERFORMANCE

### Escenario QA-PERF-01: Latencia en envío de orden a cocina/barra

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un mesero confirma una orden de 5 productos desde su tablet. |
| **Origen del estímulo** | Mesero operando durante hora pico (1:00 PM – 3:00 PM). |
| **Entorno** | Operación normal con conexión a internet estable; 10-12 usuarios concurrentes en la sucursal. |
| **Artefacto** | Módulo de gestión de órdenes y vista de preparación (cocina/barra). |
| **Respuesta del sistema** | El sistema registra la orden y la despliega en la pantalla de cocina/barra. |
| **Medida de la respuesta** | La orden aparece en la pantalla de preparación en ≤ 2 segundos desde la confirmación del mesero. |

### Escenario QA-PERF-02: Tiempo de generación de corte de caja

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | El gerente de sucursal solicita generar el corte de caja al cierre del día. |
| **Origen del estímulo** | Gerente de sucursal al finalizar la jornada (10:00 PM). |
| **Entorno** | Operación normal; se han procesado entre 150 y 250 transacciones durante el día. |
| **Artefacto** | Módulo de corte de caja. |
| **Respuesta del sistema** | El sistema calcula el resumen financiero del día (ventas totales, desglose por método de pago, diferencias con efectivo contado). |
| **Medida de la respuesta** | El resumen se presenta en ≤ 5 segundos tras la solicitud del gerente. |

### Escenario QA-PERF-03: Búsqueda de productos en catálogo durante toma de orden

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un mesero escribe el nombre parcial de un producto en el buscador del catálogo mientras toma una orden en mesa. |
| **Origen del estímulo** | Mesero atendiendo a un comensal durante hora pico. |
| **Entorno** | Operación normal; catálogo con 80-120 productos registrados; 10+ usuarios concurrentes en la sucursal. |
| **Artefacto** | Módulo de gestión de órdenes (componente de búsqueda en catálogo). |
| **Respuesta del sistema** | El sistema filtra y muestra los productos que coinciden con el texto ingresado, incluyendo nombre, precio y disponibilidad. |
| **Medida de la respuesta** | Los resultados de búsqueda se despliegan en ≤ 1 segundo tras cada carácter ingresado. |

---

## SECURITY

### Escenario QA-SEC-01: Acceso no autorizado a datos financieros

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un usuario con rol de mesero intenta acceder al módulo de reportes financieros. |
| **Origen del estímulo** | Mesero autenticado en el sistema. |
| **Entorno** | Operación normal; el mesero utiliza su tablet asignada. |
| **Artefacto** | Módulo de reportes y análisis financiero, sistema de roles y permisos. |
| **Respuesta del sistema** | El sistema deniega el acceso, muestra un mensaje indicando permisos insuficientes y registra el intento en el log de auditoría. |
| **Medida de la respuesta** | El 100% de los intentos de acceso no autorizado son bloqueados; el evento queda registrado en ≤ 1 segundo. |

### Escenario QA-SEC-02: Modificación indebida de una orden cerrada

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un cajero intenta modificar los productos o el total de una orden que ya fue cobrada y cerrada. |
| **Origen del estímulo** | Cajero autenticado en el sistema. |
| **Entorno** | Operación normal; la orden fue cerrada hace menos de 1 hora. |
| **Artefacto** | Módulo de gestión de órdenes y punto de venta. |
| **Respuesta del sistema** | El sistema impide la modificación, informa que la orden está cerrada y requiere autorización de un gerente para cualquier ajuste post-cierre. |
| **Medida de la respuesta** | El 100% de las modificaciones a órdenes cerradas quedan bloqueadas sin autorización de nivel superior; cada intento se registra con trazabilidad completa. |

### Escenario QA-SEC-03: Caducidad de sesión por inactividad

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un cajero deja el punto de venta desatendido sin cerrar sesión durante su hora de comida. |
| **Origen del estímulo** | Cajero autenticado que abandona el dispositivo sin cerrar sesión. |
| **Entorno** | Operación normal; el dispositivo permanece encendido y accesible físicamente en la sucursal. |
| **Artefacto** | Sistema de autenticación y gestión de sesiones. |
| **Respuesta del sistema** | El sistema cierra automáticamente la sesión activa tras un período de inactividad y requiere re-autenticación para continuar operando. |
| **Medida de la respuesta** | La sesión se cierra automáticamente tras ≤ 10 minutos de inactividad; se requiere credenciales completas para reanudar; el evento queda registrado en el log de auditoría. |

---

## USABILITY

### Escenario QA-USA-01: Registro de orden por mesero nuevo

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un mesero que nunca ha utilizado el sistema necesita registrar una orden de mesa. |
| **Origen del estímulo** | Mesero recién contratado, sin capacitación previa en el sistema. |
| **Entorno** | Primer día de uso del sistema; operación en horario normal. |
| **Artefacto** | Módulo de gestión de órdenes (interfaz en tablet). |
| **Respuesta del sistema** | El sistema presenta una interfaz intuitiva con flujo guiado: selección de mesa → búsqueda de productos → confirmación de orden. |
| **Medida de la respuesta** | El mesero logra completar su primera orden correctamente en ≤ 3 minutos sin asistencia externa. |

### Escenario QA-USA-02: Consulta de menú digital por comensal

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un comensal escanea el código QR de la mesa para consultar el menú. |
| **Origen del estímulo** | Comensal sin experiencia técnica avanzada, usando su smartphone personal. |
| **Entorno** | Operación normal; el comensal accede al menú web desde un navegador móvil. |
| **Artefacto** | Menú digital para clientes (aplicación web responsive). |
| **Respuesta del sistema** | El sistema despliega el menú con productos organizados por categorías, fotos, precios actualizados e indicadores de disponibilidad. |
| **Medida de la respuesta** | El comensal puede localizar un producto específico y consultar su precio en ≤ 30 segundos; la satisfacción de usabilidad es ≥ 4/5 en encuestas. |

### Escenario QA-USA-03: Recuperación ante error en proceso de cobro

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Un cajero selecciona un método de pago incorrecto (tarjeta en lugar de efectivo) y necesita corregirlo antes de cerrar la transacción. |
| **Origen del estímulo** | Cajero operando bajo alta carga de trabajo durante hora pico. |
| **Entorno** | Operación normal; la transacción aún no ha sido cerrada/confirmada. |
| **Artefacto** | Módulo de punto de venta y cobro. |
| **Respuesta del sistema** | El sistema permite al cajero deshacer o modificar el método de pago de forma clara, con un botón visible de "Corregir" o "Deshacer", sin perder los datos de la orden. |
| **Medida de la respuesta** | El cajero corrige el error en ≤ 2 pasos (clics/taps); la tasa de errores irrecuperables en cobro es del 0%. |

---

## AVAILABILITY

### Escenario QA-AVA-01: Disponibilidad durante horario operativo completo

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Múltiples usuarios de las 5 sucursales acceden al sistema simultáneamente durante todo el horario operativo. |
| **Origen del estímulo** | Operación diaria regular de lunes a sábado, 8:00 AM a 10:00 PM. |
| **Entorno** | Operación normal; hasta 40 usuarios concurrentes distribuidos en 5 sucursales. |
| **Artefacto** | Sistema de gestión integral (todos los módulos). |
| **Respuesta del sistema** | El sistema permanece operativo y responde a todas las solicitudes durante el horario completo sin interrupciones no planificadas. |
| **Medida de la respuesta** | El sistema mantiene una disponibilidad ≥ 99.5% durante el horario operativo mensual (≤ 2.5 horas de caída no planificada al mes). |

### Escenario QA-AVA-02: Recuperación ante fallo del servidor

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | El servidor en la nube experimenta una caída inesperada durante hora pico de servicio. |
| **Origen del estímulo** | Fallo de infraestructura del proveedor de nube (hardware o red). |
| **Entorno** | Hora pico (1:00 PM – 3:00 PM) con 30+ usuarios activos en las 5 sucursales. |
| **Artefacto** | Infraestructura del sistema (servidor de aplicaciones y base de datos). |
| **Respuesta del sistema** | Las sucursales con conexión intermitente continúan operando en modo offline; el servidor se restaura y sincroniza los datos acumulados. |
| **Medida de la respuesta** | El servicio en la nube se restaura en ≤ 15 minutos; no se produce pérdida de datos en las sucursales que operaron offline durante la caída. |

### Escenario QA-AVA-03: Mantenimiento planificado sin interrupción de operación

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | El equipo de desarrollo necesita desplegar una actualización del sistema con correcciones y mejoras. |
| **Origen del estímulo** | Equipo de desarrollo ejecutando un despliegue planificado. |
| **Entorno** | Domingo (día no operativo) o franja de baja actividad (antes de 8:00 AM en día hábil). |
| **Artefacto** | Infraestructura de despliegue del sistema (servidor de aplicaciones, base de datos, dispositivos en sucursales). |
| **Respuesta del sistema** | El sistema aplica la actualización sin afectar la operación activa; si el despliegue ocurre en horario operativo, utiliza una estrategia de despliegue progresivo (rolling update). |
| **Medida de la respuesta** | El tiempo de indisponibilidad durante el despliegue es de 0 minutos en horario operativo; las sucursales no perciben interrupción del servicio. |

---

## RELIABILITY

### Escenario QA-REL-01: Sincronización offline sin pérdida de datos

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Una sucursal con conexión intermitente pierde la conectividad durante 2 horas de operación; se registran 40 órdenes localmente. |
| **Origen del estímulo** | Corte de internet en la sucursal de Amecameca. |
| **Entorno** | Modo offline; la sucursal opera con almacenamiento local en los dispositivos. |
| **Artefacto** | Módulo de sincronización offline y base de datos local. |
| **Respuesta del sistema** | El sistema almacena todas las transacciones localmente y, al restablecerse la conexión, sincroniza automáticamente con el servidor central resolviendo conflictos. |
| **Medida de la respuesta** | El 100% de las transacciones registradas offline se sincronizan correctamente; 0 registros perdidos o duplicados tras la reconexión. |

### Escenario QA-REL-02: Integridad de datos en transacciones concurrentes

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Dos cajeros en la misma sucursal cobran simultáneamente dos mesas diferentes que comparten un descuento de promoción con inventario limitado. |
| **Origen del estímulo** | Cajeros operando en paralelo durante hora pico. |
| **Entorno** | Operación normal con conexión estable; alta concurrencia en punto de venta. |
| **Artefacto** | Módulo de punto de venta, gestión de inventario y promociones. |
| **Respuesta del sistema** | El sistema procesa ambas transacciones manteniendo la consistencia del inventario y la correcta aplicación de promociones sin sobreventa ni descuentos duplicados. |
| **Medida de la respuesta** | El 100% de las transacciones concurrentes mantienen integridad referencial; el balance de inventario es consistente al final del día con una diferencia ≤ 0.1%. |

### Escenario QA-REL-03: Consistencia de datos entre sucursales tras sincronización múltiple

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | Las sucursales de Amecameca, Yecapixtla y Cuautla operaron en modo offline durante distintos intervalos del día y se reconectan en momentos diferentes. |
| **Origen del estímulo** | Restablecimiento escalonado de la conectividad a internet en las 3 sucursales con conexión intermitente. |
| **Entorno** | Modo de re-sincronización; el servidor central recibe datos acumulados de 3 sucursales en un período de 30 minutos. |
| **Artefacto** | Módulo de sincronización, base de datos central, módulo de inventario y reportes consolidados. |
| **Respuesta del sistema** | El sistema procesa las sincronizaciones de las 3 sucursales, resuelve conflictos de orden temporal, actualiza el inventario global y genera reportes consolidados consistentes. |
| **Medida de la respuesta** | Los reportes consolidados del dueño reflejan el 100% de las transacciones de todas las sucursales; 0 inconsistencias en inventario global tras la sincronización completa. |

---

## MODIFIABILITY

### Escenario QA-MOD-01: Incorporación de un nuevo módulo funcional

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | El dueño solicita agregar un módulo de programa de fidelización de clientes al sistema existente. |
| **Origen del estímulo** | Requerimiento de negocio post-lanzamiento (evolución planificada). |
| **Entorno** | Sistema en producción, operando normalmente en las 5 sucursales. |
| **Artefacto** | Arquitectura del sistema (backend, frontend, base de datos). |
| **Respuesta del sistema** | El equipo de desarrollo implementa el módulo sin modificar los módulos existentes (órdenes, cobro, inventario) y lo integra mediante interfaces bien definidas. |
| **Medida de la respuesta** | El nuevo módulo se desarrolla, prueba y despliega en ≤ 2 sprints (4 semanas) sin generar regresiones en los módulos existentes. |

### Escenario QA-MOD-02: Adición de una nueva sucursal

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | La franquicia abre una sexta sucursal y necesita integrarla al sistema existente. |
| **Origen del estímulo** | Expansión del negocio por decisión del dueño. |
| **Entorno** | Sistema en producción con 5 sucursales activas. |
| **Artefacto** | Configuración del sistema, módulo de administración de sucursales. |
| **Respuesta del sistema** | El sistema permite dar de alta la nueva sucursal, asignar usuarios, configurar inventario inicial y comenzar a operar sin afectar a las sucursales existentes. |
| **Medida de la respuesta** | La nueva sucursal queda operativa en ≤ 1 día hábil de configuración; 0 interrupciones en las sucursales existentes durante el proceso. |

### Escenario QA-MOD-03: Cambio en reglas de negocio de promociones

| Parte | Descripción |
|-------|-------------|
| **Estímulo** | El dueño solicita que las promociones puedan aplicarse condicionalmente (por ejemplo: "2x1 solo de 2:00 PM a 5:00 PM" o "descuento del 15% en compras mayores a $200 MXN"). |
| **Origen del estímulo** | Evolución de la estrategia comercial del negocio. |
| **Entorno** | Sistema en producción; el módulo de promociones ya opera con descuentos simples (porcentaje fijo). |
| **Artefacto** | Módulo de gestión de promociones, motor de reglas de negocio. |
| **Respuesta del sistema** | El equipo de desarrollo extiende las reglas de negocio del módulo de promociones sin modificar los módulos de órdenes, cobro ni inventario. |
| **Medida de la respuesta** | La modificación se implementa y despliega en ≤ 1 sprint (2 semanas); los módulos no afectados mantienen 0 regresiones en sus pruebas automatizadas. |

## Utility Tree

| ID Escenario | Atributo | Descripción corta | Importancia de negocio | Dificultad técnica | Prioridad |
|---|---|---|---|---|---|
| QA-PERF-01 | Performance | Latencia orden → cocina/barra ≤ 2s | Alta | Media | **Media** |
| QA-PERF-02 | Performance | Generación de corte de caja ≤ 5s | Media | Baja | **Baja** |
| QA-PERF-03 | Performance | Búsqueda en catálogo ≤ 1s | Media | Baja | **Baja** |
| QA-SEC-01 | Security | Bloqueo acceso financiero por rol | Alta | Baja | **Media** |
| QA-SEC-02 | Security | Protección de órdenes cerradas | Alta | Media | **Media** |
| QA-SEC-03 | Security | Caducidad de sesión por inactividad | Media | Baja | **Baja** |
| QA-USA-01 | Usability | Mesero nuevo registra orden ≤ 3 min | Alta | Media | **Media** |
| QA-USA-02 | Usability | Comensal consulta menú QR ≤ 30s | Media | Baja | **Baja** |
| QA-USA-03 | Usability | Recuperación de error en cobro | Alta | Baja | **Media** |
| QA-AVA-01 | Availability | Uptime ≥ 99.5% en horario operativo | Alta | Media | **Media** |
| QA-AVA-02 | Availability | Recuperación ante fallo servidor ≤ 15 min | Alta | Alta | **Alta** |
| QA-AVA-03 | Availability | Despliegue sin interrupción (rolling update) | Baja | Alta | **Baja** |
| QA-REL-01 | Reliability | Sincronización offline 0 pérdidas | Alta | Alta | **Alta** |
| QA-REL-02 | Reliability | Integridad en transacciones concurrentes | Alta | Media | **Media** |
| QA-REL-03 | Reliability | Consistencia multi-sucursal post-sync | Alta | Alta | **Alta** |
| QA-MOD-01 | Modifiability | Nuevo módulo ≤ 2 sprints, 0 regresiones | Media | Media | **Media** |
| QA-MOD-02 | Modifiability | Nueva sucursal operativa ≤ 1 día | Media | Baja | **Baja** |
| QA-MOD-03 | Modifiability | Cambio reglas de promociones ≤ 1 sprint | Baja | Media | **Baja** |

### Resumen por prioridad

| Prioridad | Escenarios |
|-----------|------------|
| **Alta** | QA-AVA-02, QA-REL-01, QA-REL-03 |
| **Media** | QA-PERF-01, QA-SEC-01, QA-SEC-02, QA-USA-01, QA-USA-03, QA-AVA-01, QA-REL-02, QA-MOD-01 |
| **Baja** | QA-PERF-02, QA-PERF-03, QA-SEC-03, QA-USA-02, QA-AVA-03, QA-MOD-02, QA-MOD-03 |

# Escenarios
