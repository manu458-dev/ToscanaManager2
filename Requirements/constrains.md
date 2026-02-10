# CONSTRAINTS.MD
Aquí se identifican las restricciones, tanto técnicas como no técnicas, del proyecto del Sistema de Gestión Integral — La Toscana.
---

## Restricciones Técnicas

| ID | Restricción | Tipo |
|----|-------------|------|
| RES-01 | El sistema **debe operar en modo offline** y sincronizar datos automáticamente cuando se resta   blezca la conexión a internet, dado que 3 de las 5 sucursales (Amecameca, Yecapixtla, Cuautla) presentan conectividad intermitente. | 🔧 Técnica |
| RES-02 | El sistema debe soportar hasta **40 usuarios concurrentes** distribuidos en las 5 sucursales, con picos de 10-12 usuarios simultáneos por sucursal grande. | 🔧 Técnica |
| RES-03 | La interfaz debe ser **responsive** y completamente funcional en tres tipos de dispositivos: PC de escritorio, tablets y smartphones. | 🔧 Técnica |
| RES-04 | El sistema debe ser compatible con **pantallas de TV** para la vista de preparación en cocina/barra, funcionando como un display de solo lectura con interacción mínima (marcar como "listo"). | 🔧 Técnica |
| RES-05 | El menú digital para clientes debe ser una **aplicación web accesible por código QR** (no una app nativa), sin requerir instalación por parte del comensal. | 🔧 Técnica |
| RES-06 | El sistema debe implementar un **modelo de roles y permisos diferenciados** para al menos 6 roles distintos (dueño, gerente, cajero, mesero, barista, cocinero), restringiendo el acceso a funcionalidades e información según el perfil. | 🔧 Técnica |
| RES-07 | La sincronización offline debe resolverse con una **estrategia de conflictos de datos** confiable, ya que múltiples dispositivos por sucursal pueden generar datos simultáneamente sin conexión (ej. varias tablets de meseros tomando órdenes). | 🔧 Técnica |
| RES-08 | El sistema debe procesar un volumen de entre **700 y 1,100 órdenes diarias** a nivel franquicia (~18,200-28,600 mensuales), garantizando rendimiento adecuado en operaciones de lectura/escritura. | 🔧 Técnica |
| RES-09 | El sistema debe operar correctamente sobre la **infraestructura existente** (5 PCs, 10-14 tablets, 5 TVs, smartphones personales), sin requerir inversión adicional en hardware especializado. | 🔧 Técnica |

## Restricciones No Técnicas

| ID | Restricción | Tipo |
|----|-------------|------|
| RES-10 | La fecha límite de entrega del sistema es **antes del 1 de abril de 2026** (6 sprints de 2 semanas = 12 semanas), lo que restringe la complejidad técnica de cada módulo y exige priorización estricta. | 📋 Calendario |
| RES-11 | La **información financiera** (ventas, cortes de caja, reportes, utilidades) debe estar protegida y ser accesible únicamente para el dueño, gerentes y el contador, lo cual impone restricciones de seguridad y control de acceso a los datos. | 📋 Negocio |