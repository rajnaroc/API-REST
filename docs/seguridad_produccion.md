# Mejoras para la puesta en producción segura

Este documento complementa las prácticas existentes incorporando mejoras para fortalecer el ciclo de vida seguro de la API.

## Diseño y arquitectura
- **Amenazas y modelado continuo:** ejecutar threat modeling ligero por cada cambio significativo; documentar supuestos y mitigaciones para endpoints y flujos críticos.
- **Segregación estricta de entornos:** separar redes y credenciales para dev/stage/prod, usando controles de acceso diferenciados y rutas administrativas detrás de VPN o mTLS.
- **Principio de mínimos privilegios en datos:** limitar columnas y campos sensibles en respuestas; aplicar paginación obligatoria y límites de tamaño consistentes.
- **Gestión de secretos reforzada:** rotación automática, escaneo de secretos en commits (pre-commit/CI) y uso de cuentas de servicio con expiración.

## Supply chain y build
- **SBOM y firmas:** generar SBOM (CycloneDX) en cada build y firmar imágenes/artefactos (cosign). Validar firmas en el pipeline de entrega.
- **Protección del pipeline:** exigir firmas de commits/tags, proteger ramas principales y forzar revisiones con lista de chequeo de seguridad.
- **Hardening de Docker/OCI:** usar usuarios no-root, root filesystem de solo lectura y dropping de capabilities; escanear imágenes y capas intermedias.

## Despliegue y plataforma
- **Controles de ingreso y L7:** reforzar el gateway con detección de bots, validación de esquemas (JSON Schema) y rate limiting adaptativo por identidad.
- **Políticas de red declarativas:** egress/ingress minimal con NetworkPolicies/SG; inspección TLS saliente para detectar exfiltración.
- **Observabilidad segura:** logging estructurado con datos personales minimizados y tokenización de identificadores sensibles; trazas con sampling adaptativo para picos.
- **Resiliencia y DR:** backups cifrados con restauraciones ensayadas; pruebas de conmutación por error planificadas y monitoreadas.

## Operación continua
- **DAST y fuzzing recurrente:** programar scans dinámicos y fuzzing en staging y producción con límites de impacto y exclusiones documentadas.
- **Respuesta a incidentes y detección:** reglas de detección para comportamientos anómalos (picos de 401/403/429, patrones de inyección) integradas con alerting y runbooks.
- **Gestión de vulnerabilidades:** ventanas regulares de parcheo, priorización basada en riesgo y KPIs (MTTR de vulnerabilidades, cobertura de escaneo de dependencias).
- **Pruebas de caos de seguridad:** ejercicios de rotación de llaves, expiración de tokens y caída de servicios de identidad para validar tolerancia.

## Checklist accionable inicial
1. Activar escaneo de secretos en pre-commit/CI y rotación automática de credenciales de CI/CD.
2. Generar y publicar SBOM firmado en cada release; bloquear despliegues sin firma válida del artefacto.
3. Aplicar rate limiting adaptativo en el gateway y validación de esquemas para todas las rutas públicas.
4. Definir políticas de retención y tokenización de logs para datos sensibles; habilitar alertas por anomalías de autenticación.
5. Programar ejercicios trimestrales de restauración de backups y pruebas de failover controladas.
