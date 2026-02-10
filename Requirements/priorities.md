# PRIORITIES.md
En este documento, se priorizan los atributos de calidad.
## Utility Tree

| ID Escenario | Atributo | Descripción corta | Importancia de negocio | Dificultad técnica | Prioridad |
|---|---|---|---|---|---|
| QA-PERF-01 | Performance | Latencia orden → cocina/barra ≤ 2s | Alta | Media | **Media** |
| QA-PERF-02 | Performance | Generación de corte de caja ≤ 5s | Media | Alta | **Media** |
| QA-PERF-03 | Performance | Búsqueda en catálogo ≤ 1s | Media | Baja | **Baja** |
| QA-SEC-01 | Security | Bloqueo acceso financiero por rol | Alta | Baja | **Media** |
| QA-SEC-02 | Security | Protección de órdenes cerradas | Alta | Media | **Media** |
| QA-SEC-03 | Security | Caducidad de sesión por inactividad | Media | Media | **Media** |
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
| **Media** | QA-PERF-01, QA-PERF-02, QA-SEC-01, QA-SEC-02, QA-SEC-03, QA-USA-01, QA-USA-03, QA-AVA-01, QA-REL-02, QA-MOD-01 |
| **Baja** | QA-PERF-03, QA-USA-02, QA-AVA-03, QA-MOD-02, QA-MOD-03 |