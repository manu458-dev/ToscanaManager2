# Architectural Concerns — Toscana Manager 2.0

Este documento identifica las preocupaciones arquitectónicas que deben abordarse durante el diseño del sistema para la franquicia de cafeterías La Toscana. Estas preocupaciones complementan los requisitos especificados en las historias de usuario y los escenarios de atributos de calidad.

---

## 1. Data Management and Persistence

### Data Lifecycle
- **C001.1.1: Políticas de retención de datos.** Definir cuánto tiempo se conservan órdenes históricas, cortes de caja, registros de inventario y logs de auditoría. Considerar requisitos fiscales mexicanos (SAT) que exigen conservar registros contables por al menos 5 años.
- **C001.1.2: Estrategia de archivado.** ¿Cómo se archivan datos históricos de ventas, inventario y cortes de caja para que no impacten el rendimiento de consultas operativas diarias, pero sigan disponibles para reportes del dueño (HU-15, HU-16)?
- **C001.1.3: Limpieza de datos transitorios.** Procedimientos para limpiar órdenes abandonadas, sesiones expiradas y datos temporales generados durante operación offline.

### Data Synchronization (Offline-Online)
- **C001.2.1: Estrategia de sincronización offline-online.** ¿Cómo garantizar la consistencia de datos cuando 3 de las 5 sucursales (Amecameca, Yecapixtla, Cuautla) operan con conectividad intermitente y múltiples dispositivos generan datos simultáneamente sin conexión (HU-27)? Se debe definir una estrategia de resolución de conflictos (ej. last-write-wins, merge basado en timestamps, CRDTs).
- **C001.2.2: Persistencia local de datos.** ¿Dónde y cómo se almacenarán los datos localmente en cada dispositivo mientras opera en modo offline? Se debe elegir una tecnología de almacenamiento local (ej. IndexedDB, SQLite, PouchDB) que funcione en PCs, tablets y smartphones.
- **C001.2.3: Validación de datos post-sincronización.** Mecanismos para validar la integridad de los datos después de cada sincronización, detectando y reportando inconsistencias (ej. inventario negativo, doble registro de una orden).

### Data Backup and Recovery
- **C001.3.1: Frecuencia y retención de respaldos.** Definir la frecuencia de respaldos (diario, por turno, continuo) y periodos de retención, considerando que la franquicia maneja entre $33,000 y $58,000 MXN/mes en transacciones.
- **C001.3.2: Objetivos RPO y RTO.** Definir el Recovery Point Objective (máximo de datos que se pueden perder) y Recovery Time Objective (tiempo máximo de recuperación), especialmente para datos financieros y de inventario.
- **C001.3.3: Respaldo en entorno offline.** ¿Cómo se respaldan los datos locales en sucursales que operan con conectividad intermitente antes de la sincronización con el servidor central?

### Data Migration
- **C001.4.1: Migración de datos iniciales.** Estrategia para migrar datos existentes (catálogo de productos, proveedores, recetas, inventario actual) desde los registros manuales y hojas de cálculo actuales hacia el nuevo sistema.
- **C001.4.2: Compatibilidad hacia atrás en esquema.** ¿Cómo se manejan los cambios de esquema de base de datos durante actualizaciones del sistema sin perder datos ni interrumpir la operación?

---

## 2. Integration Architecture

### Inter-Module Integration
- **C002.1.1: Integración entre módulos interdependientes.** El sistema tiene alta interdependencia (órdenes → cobro → corte de caja → reportes; recetas → inventario → proveedores). ¿Cómo se define la arquitectura interna para que los módulos sean cohesivos pero mantengan bajo acoplamiento, permitiendo desarrollo incremental por sprints (HU-01→HU-05→HU-10→HU-12)?
- **C002.1.2: Atomicidad de operaciones cross-module.** El inventario debe descontarse automáticamente por cada venta con base en las recetas registradas (HU-21). ¿Cómo se maneja la atomicidad de esta operación (venta + descuento de insumos) especialmente en modo offline, evitando inconsistencias entre inventario real y registrado?
- **C002.1.3: Dependencias entre historias de usuario.** Gestión de las dependencias técnicas identificadas: HU-15/HU-16/HU-17 dependen de datos generados por HU-01/HU-05/HU-10/HU-12; HU-20 depende de inventario operativo; HU-22/HU-23 dependen de catálogo centralizado (HU-18).

### Real-Time Communication
- **C002.2.1: Mecanismo de comunicación en tiempo real.** Las órdenes deben reflejarse inmediatamente en las pantallas de cocina/barra y notificar a los meseros cuando están listas (HU-02, HU-03, HU-09). ¿Qué mecanismo se utilizará (WebSockets, Server-Sent Events, polling) y cómo funcionará en sucursales con conectividad intermitente?
- **C002.2.2: Cola de mensajes en modo offline.** ¿Cómo se manejan las notificaciones y actualizaciones en tiempo real cuando la sucursal está operando sin conexión? Estrategia de encolamiento y entrega garantizada de mensajes.

### External Service Integration
- **C002.3.1: Integración con sistemas de pago.** ¿Cómo se integra el registro de pago con tarjeta (HU-05) si se requiere conexión a procesadores de pago? Estrategia de fallback para pagos en modo offline.
- **C002.3.2: Integración con impresoras de comprobantes.** Estrategia de compatibilidad con impresoras térmicas de tickets para generar comprobantes de venta (HU-05) en las 5 sucursales.

---

## 3. Security Architecture

### Access Control
- **C003.1.1: Implementación de RBAC.** Se requieren al menos 6 roles diferenciados (dueño, gerente, cajero, mesero, barista, cocinero) con permisos granulares (HU-28). ¿Cómo se implementa un sistema de autenticación y autorización robusto que no añada fricción operativa al personal durante el servicio?
- **C003.1.2: Granularidad de permisos.** Definir la matriz de permisos por rol: información financiera restringida a dueño/gerente/contador; acceso a inventario limitado a gerentes; operación de órdenes para meseros, etc.
- **C003.1.3: Gestión de sesiones.** ¿Cómo se gestionan las sesiones de usuario en dispositivos compartidos (ej. tablet del mesero usada por varios meseros en distintos turnos)? Tiempo de expiración, cierre de sesión automático, sesiones concurrentes.
- **C003.1.4: Autenticación en modo offline.** ¿Cómo se valida la identidad del usuario cuando el dispositivo no tiene conexión al servidor central?

### Data Protection
- **C003.2.1: Protección de datos financieros.** La información de ventas, cortes de caja y reportes financieros ($33,000–$58,000 MXN/mes) debe estar cifrada en tránsito y en reposo. Estándares de cifrado a aplicar.
- **C003.2.2: Almacenamiento seguro local.** Los datos almacenados localmente en dispositivos (modo offline) deben estar protegidos contra acceso no autorizado en caso de pérdida o robo del dispositivo.

### Audit and Traceability
- **C003.3.1: Trazabilidad completa de operaciones.** El sistema debe registrar quién realizó qué acción y cuándo (órdenes, cobros, modificaciones de inventario, cortes de caja). ¿Cómo se diseña un sistema de logs/auditoría que permita rastrear acciones sin impactar el rendimiento ni el almacenamiento?
- **C003.3.2: Detección de anomalías financieras.** ¿Cómo se implementa la detección automática de faltantes en cortes de caja (HU-11) y la identificación de su origen?

---

## 4. Operational Architecture

### Deployment and Updates
- **C004.1.1: Estrategia de despliegue multi-sucursal.** Con 5 sucursales, ~25 dispositivos y conectividad variable, ¿cómo se despliegan actualizaciones del sistema sin interrumpir la operación diaria (lunes a sábado, 8:00 AM a 10:00 PM)?
- **C004.1.2: Versionamiento y rollback.** ¿Cómo se maneja el versionamiento del sistema y la capacidad de revertir actualizaciones problemáticas en sucursales con conectividad intermitente?
- **C004.1.3: Consistencia de versiones entre sucursales.** Estrategia para garantizar que todas las sucursales operen con la misma versión del sistema, especialmente cuando algunas no tienen conectividad continua.

### Monitoring and Observability
- **C004.2.1: Monitoreo remoto de sucursales.** ¿Cómo se monitorea el estado del sistema en las 5 sucursales desde una ubicación central? Alertas de caída, errores, problemas de sincronización.
- **C004.2.2: Estándares de logging.** Definir niveles de log, formato, rotación y retención para diagnóstico de problemas sin consumir almacenamiento excesivo en dispositivos limitados.
- **C004.2.3: Métricas de operación.** ¿Qué métricas se recolectan (tiempo de respuesta, tasa de errores, estado de sincronización, uso de almacenamiento local) y cómo se analizan?

### Disaster Recovery and Availability
- **C004.3.1: Continuidad operativa ante caída del servidor central.** Si el servidor central cae, ¿las sucursales pueden seguir operando? La arquitectura debe garantizar que las operaciones críticas (toma de órdenes, cobro, corte de caja) continúen sin interrupción.
- **C004.3.2: Estrategia de recuperación ante desastres.** Plan de recuperación que contemple pérdida de datos, fallo de hardware y desastres naturales para una franquicia que depende del sistema para su operación diaria.
- **C004.3.3: Redundancia de datos críticos.** Estrategia para mantener copias redundantes de datos financieros y de inventario que son vitales para el negocio.

---

## 5. Development Architecture

### Code Organization
- **C005.1.1: Definición de límites de módulos.** Definir claramente los límites y responsabilidades de cada módulo (órdenes, POS, inventario, recetas, reportes, menú digital, proveedores, promociones, clientes) para permitir desarrollo para paralelo por el equipo.
- **C005.1.2: Gestión de dependencias compartidas.** Estrategia para manejar código compartid entre módulos (modelos de datos, utilidades, componentes UI reutilizables) sin crear acoplamiento excesivo.
- **C005.1.3: Estrategia de reutilización de código.** ¿Cómo se reutilizan componentes entre la app de meseros, la interfaz de caja, las pantallas de cocina/barra y el menú digital QR?

### Development Workflow
- **C005.2.1: Pipeline de integración continua.** Diseño del pipeline de CI/CD considerando que el equipo debe entregar por sprints incrementales y el sistema tiene múltiples puntos de despliegue (servidor central, dispositivos locales).
- **C005.2.2: Gestión de ambientes.** Estrategia de ambientes (desarrollo, staging, producción) considerando que producción involucra 5 sucursales con condiciones de conectividad diferentes.

### Technical Debt
- **C005.3.1: Gestión de deuda técnica.** ¿Cómo se rastrea y prioriza la deuda técnica durante el desarrollo incremental por sprints, especialmente cuando se toman atajos para cumplir necesidades operativas urgentes?
- **C005.3.2: Documentación técnica.** Requisitos de documentación para APIs internas, esquemas de datos, procedimientos de sincronización y resolución de conflictos.

---

## 6. User Experience Architecture

### Multi-Device Adaptability
- **C006.1.1: Heterogeneidad de dispositivos.** El sistema debe funcionar en PCs de escritorio, tablets, smartphones y pantallas de TV. ¿Cómo se diseña una arquitectura de frontend que sea adaptable a capacidades de hardware y tamaños de pantalla tan distintos sin duplicar código?
- **C006.1.2: Rendimiento en dispositivos limitados.** Las tablets y smartphones de los meseros pueden tener capacidad de procesamiento y memoria limitadas. ¿Cómo se garantiza que la aplicación sea ligera, rápida y no consuma recursos excesivos en estos dispositivos?
- **C006.1.3: Experiencia offline.** ¿Cómo se comunica al usuario el estado de conexión y las limitaciones del modo offline? Diseño de estados de carga, errores y notificaciones de sincronización pendiente.

### Usability in High-Pressure Environments
- **C006.2.1: Usabilidad bajo presión operativa.** El personal de servicio (meseros, cajeros, baristas) opera en condiciones de alto ritmo. ¿Cómo se diseñan interfaces que minimicen la cantidad de pasos/toques necesarios para completar tareas frecuentes (registrar orden, cobrar, marcar producto listo)?
- **C006.2.2: Capacitación y curva de aprendizaje.** Con ~35-40 usuarios de perfiles diversos (desde meseros hasta el dueño), ¿cómo se diseña el sistema para que sea intuitivo y requiera mínima capacitación?

### Digital Menu (Customer-Facing)
- **C006.3.1: Experiencia del menú QR.** El menú digital vía QR (HU-22, HU-23) es la única interfaz expuesta al comensal. ¿Cómo se garantiza una experiencia rápida, atractiva y funcional sin requerir descarga de aplicación, considerando la diversidad de dispositivos de los comensales?

---

## 7. Scalability and Performance

### Multi-Branch Scalability
- **C007.1.1: Estructura de datos multi-sucursal.** El sistema debe manejar 5 sucursales con ~35-40 usuarios concurrentes y entre 700 y 1,100 órdenes diarias. ¿Cómo se estructura la base de datos y el backend para soportar este volumen sin degradar el rendimiento?
- **C007.1.2: Aislamiento vs. datos compartidos.** ¿Cómo se aíslan o comparten los datos entre sucursales? El catálogo de productos y recetas es compartido (HU-18), pero las órdenes, inventario y cortes de caja son por sucursal.
- **C007.1.3: Crecimiento futuro.** ¿La arquitectura soporta la adición de nuevas sucursales sin rediseño significativo?

### Performance Baselines
- **C007.2.1: Tiempos de respuesta objetivo.** Definir tiempos de respuesta aceptables para operaciones críticas: registro de orden (<2s), envío a pantalla de cocina (<1s), consulta de inventario (<3s), generación de corte de caja (<5s).
- **C007.2.2: Pruebas de carga.** Estrategia de pruebas de carga que simule picos operativos (hora pico con 5 sucursales operando simultáneamente).

---

## 8. Business Architecture

### Business Continuity
- **C008.1.1: Identificación de procesos críticos.** Determinar cuáles procesos de negocio no pueden detenerse bajo ninguna circunstancia (toma de órdenes, cobro, corte de caja) vs. cuáles pueden tolerar interrupción temporal (reportes, catálogo de proveedores, promociones).
- **C008.1.2: SLAs internos.** Definir acuerdos de nivel de servicio internos para disponibilidad del sistema (ej. 99.5% en horario operativo de 8:00 AM a 10:00 PM, lunes a sábado).

### Cost Management
- **C008.2.1: Costos de infraestructura.** ¿Cuál es el presupuesto disponible para hosting, servicios cloud, licencias y mantenimiento? La solución debe ser sostenible para una franquicia de 5 sucursales.
- **C008.2.2: Análisis costo-beneficio.** ¿El costo del sistema justifica la reducción de pérdidas proyectada ($33,000–$58,000 MXN/mes)? Considerar costos de hardware (tablets, pantallas TV, impresoras) por sucursal.

---

## 9. Compliance and Legal

### Regulatory Requirements
- **C009.1.1: Cumplimiento fiscal mexicano (SAT).** ¿El sistema debe generar CFDI o solo comprobantes internos? Requisitos de facturación electrónica y retención de registros fiscales.
- **C009.1.2: Regulaciones de protección de datos.** Cumplimiento con la Ley Federal de Protección de Datos Personales en Posesión de los Particulares (LFPDPPP) para datos de clientes (HU-26) y empleados.
- **C009.1.3: Regulaciones de transacciones financieras.** ¿Qué requisitos regulatorios aplican al registro de transacciones financieras (ventas, cobros, cortes de caja) en el contexto de una franquicia?

### Legal Considerations
- **C009.2.1: Términos de uso del menú digital.** Aviso de privacidad y términos de uso para el acceso al menú digital QR por parte de comensales (HU-22).

---

## 10. Testing Architecture

### Test Strategy
- **C010.1.1: Pruebas en entorno offline.** ¿Cómo se prueban los flujos de sincronización offline-online, resolución de conflictos y operación sin conectividad de forma reproducible?
- **C010.1.2: Gestión de datos de prueba.** Estrategia para generar datos de prueba realistas (catálogos, órdenes, inventario, cortes) que reflejen el volumen operativo real de la franquicia.
- **C010.1.3: Pruebas multi-dispositivo.** ¿Cómo se valida que el sistema funciona correctamente en PCs, tablets, smartphones y pantallas de TV con diferentes capacidades de hardware?
- **C010.1.4: Pruebas de rendimiento.** Validar tiempos de respuesta y comportamiento bajo carga con 35-40 usuarios concurrentes procesando 700-1,100 órdenes diarias en 5 sucursales.

### Quality Assurance
- **C010.2.1: Quality gates por sprint.** Definir criterios de aceptación técnicos que cada módulo debe cumplir antes de desplegarse a producción (cobertura de tests, rendimiento, seguridad).
- **C010.2.2: Validación de integridad financiera.** ¿Cómo se verifica que los cálculos financieros (totales de órdenes, cortes de caja, reportes) son correctos y consistentes entre módulos?