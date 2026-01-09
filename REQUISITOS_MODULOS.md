# REQUISITOS DE FINALIZACION POR MODULO - Sistema SIGL

## Proposito de Este Documento

Define los **criterios de aceptacion** que debe cumplir cada modulo para considerarse finalizado. Incluye requisitos funcionales, de rendimiento, seguridad y testing.

---

## MODULO 1: AUTENTICACION Y SEGURIDAD

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 1.1 | Login con credenciales | Genera token JWT valido por 30 minutos | [ ] |
| 1.2 | Refresh token | Permite renovar sesion sin re-login | [ ] |
| 1.3 | Logout | Invalida token actual del usuario | [ ] |
| 1.4 | CRUD usuarios | Crear, leer, actualizar, eliminar usuarios | [ ] |
| 1.5 | Asignacion de roles | Usuarios tienen rol asignado correctamente | [ ] |
| 1.6 | Guards por rol | Endpoints protegidos segun rol del usuario | [ ] |
| 1.7 | CRUD puntos/sucursales | Gestion completa de puntos de servicio | [ ] |

### Requisitos de Seguridad

| # | Requisito | Especificacion |
|---|-----------|----------------|
| S1 | Hashing de passwords | bcrypt con cost factor 12 |
| S2 | Rate limiting | 100 requests/minuto por IP |
| S3 | CORS | Configurado para dominios autorizados |
| S4 | Headers de seguridad | Helmet con CSP, HSTS, X-Frame-Options |
| S5 | Validacion de input | class-validator en todos los DTOs |

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo de login | < 500ms |
| Tiempo de validacion JWT | < 50ms |
| Concurrencia soportada | 100 usuarios simultaneos |

### Tests Requeridos

- [ ] Tests unitarios de AuthService (>80% cobertura)
- [ ] Tests e2e de login/logout/refresh
- [ ] Tests de autorizacion por rol
- [ ] Tests de rate limiting

---

## MODULO 2: RECEPCION DIGITAL

> **IMPORTANTE:** La recepcion digital se realiza desde la **App Movil Flutter**, NO desde Angular. El empleado usa su telefono para capturar la foto de la vineta.

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Fase | Estado |
|---|-----------|-------------------|------|--------|
| 2.1 | **Captura de imagen (Flutter)** | **Funciona en App Movil con acceso a camara** | **FASE 1 CRITICO** | [ ] |
| 2.2 | Envio de imagen | Imagen se sube correctamente al backend desde Flutter | FASE 1 | [ ] |
| 2.3 | OCR de vineta | Extrae texto de imagen manuscrita (Google Vision) | FASE 1 | [ ] |
| 2.4 | Precision OCR | > 90% de datos extraidos correctamente | FASE 1 | [ ] |
| 2.5 | **Formulario de validacion (Flutter)** | **Permite corregir datos extraidos en la app movil** | **FASE 1 CRITICO** | [ ] |
| 2.6 | Generacion de codigo | Codigo unico generado siempre (SV+fecha+punto+random) | FASE 1 | [ ] |
| 2.7 | Etiqueta QR | Genera etiqueta imprimible con codigo QR | FASE 1 | [ ] |
| 2.8 | Almacenamiento MinIO | Imagen guardada con URL accesible | FASE 1 | [ ] |
| 2.9 | Registro en BD | Paquete creado con todos los campos | FASE 1 | [ ] |

### Datos Extraidos por OCR

El sistema debe extraer automaticamente:
- Nombre del remitente
- Telefono del remitente
- Nombre del destinatario
- Telefono del destinatario
- Punto de destino
- Valor del paquete (si aplica)
- Costo de envio

### Formato de Codigo de Rastreo

```
SV + YYYYMMDD + PUNTO_CODE + RANDOM(5)
Ejemplo: SV20260108SS1A2B3
```

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo total de procesamiento | < 5 segundos |
| Tiempo de subida de imagen | < 2 segundos |
| Tiempo de respuesta OCR | < 3 segundos |

### Tests Requeridos

- [ ] Test de integracion con Google Vision API
- [ ] Test de generacion de codigo unico (sin colisiones)
- [ ] Test de almacenamiento en MinIO
- [ ] Test de creacion de paquete completo

---

## MODULO 3: RASTREO EN TIEMPO REAL

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 3.1 | Portal publico | Accesible sin autenticacion | [ ] |
| 3.2 | Busqueda por codigo | Retorna paquete con historial | [ ] |
| 3.3 | Busqueda por telefono | Retorna paquetes del remitente/destinatario | [ ] |
| 3.4 | Busqueda por nombre | Retorna paquetes coincidentes | [ ] |
| 3.5 | Historial de estados | Muestra todos los cambios cronologicamente | [ ] |
| 3.6 | API de cambio de estado | PUT /api/packages/:id/status funciona | [ ] |
| 3.7 | Validacion de transiciones | Rechaza transiciones invalidas | [ ] |

### Maquina de Estados

**Transiciones validas:**

```
Estado Actual        | Estados Siguientes Permitidos
---------------------|------------------------------
RECIBIDO_ORIGEN      | EN_TRANSITO, CANCELADO
EN_TRANSITO          | LLEGO_DESTINO, EN_DEVOLUCION
LLEGO_DESTINO        | ENTREGADO, EN_DEVOLUCION
ENTREGADO            | (ninguno - estado final)
EN_DEVOLUCION        | DEVUELTO
DEVUELTO             | (ninguno - estado final)
CANCELADO            | (ninguno - estado final)
```

**Transiciones invalidas deben retornar:**
```json
{
  "statusCode": 400,
  "message": "Transicion no permitida de ENTREGADO a EN_TRANSITO",
  "error": "Bad Request"
}
```

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo de busqueda | < 2 segundos |
| Resultados por pagina | Maximo 50 |
| Cache de consultas frecuentes | TTL 60 segundos |

### Tests Requeridos

- [ ] Test de todas las transiciones validas
- [ ] Test de rechazo de transiciones invalidas
- [ ] Test de busqueda por cada criterio
- [ ] Test de paginacion

---

## MODULO 4: NOTIFICACIONES AUTOMATICAS

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 4.1 | Envio SMS | SMS enviado via Twilio al cambiar estado | [ ] |
| 4.2 | Envio Email | Email enviado via SendGrid al cambiar estado | [ ] |
| 4.3 | Plantillas | 8 tipos de plantillas configurables | [ ] |
| 4.4 | Reintentos | 3 reintentos con backoff exponencial | [ ] |
| 4.5 | Historial | Registro de todas las notificaciones | [ ] |
| 4.6 | Fallback | Si SMS falla, intenta Email | [ ] |

### Tipos de Notificacion Requeridos

| # | Tipo | Trigger | Destinatario |
|---|------|---------|--------------|
| 1 | Paquete recibido | Estado = RECIBIDO_ORIGEN | Remitente |
| 2 | Paquete en transito | Estado = EN_TRANSITO | Destinatario |
| 3 | Paquete llego a destino | Estado = LLEGO_DESTINO | Destinatario |
| 4 | Paquete entregado | Estado = ENTREGADO | Remitente |
| 5 | Reembolso disponible | Reembolso.estado = APROBADO | Remitente |
| 6 | Recordatorio retiro | 3 dias sin movimiento en destino | Destinatario |
| 7 | Paquete en devolucion | Estado = EN_DEVOLUCION | Remitente |
| 8 | Paquete devuelto | Estado = DEVUELTO | Remitente |

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo de envio SMS | < 30 segundos despues de trigger |
| Tiempo de envio Email | < 60 segundos despues de trigger |
| Tasa de entrega SMS | > 95% |
| Tasa de entrega Email | > 98% |

### Estructura de Historial

```typescript
interface NotificationHistory {
  id: number;
  packageId: number;
  type: string;           // 'SMS' | 'EMAIL'
  template: string;       // Tipo de notificacion
  recipient: string;      // Telefono o email
  status: string;         // 'PENDIENTE' | 'ENVIADO' | 'FALLIDO'
  attempts: number;       // 0-3
  errorMessage?: string;
  sentAt?: Date;
  createdAt: Date;
}
```

### Tests Requeridos

- [ ] Test de integracion con Twilio (mock)
- [ ] Test de integracion con SendGrid (mock)
- [ ] Test de sistema de reintentos
- [ ] Test de fallback SMS -> Email

---

## MODULO 5: GESTION DE RUTAS Y TRANSPORTE

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 5.1 | CRUD rutas | Crear, listar, actualizar, eliminar rutas | [ ] |
| 5.2 | Asignacion paquetes | Asignar multiples paquetes a una ruta | [ ] |
| 5.3 | Asignacion conductor | Solo usuarios con rol CONDUCTOR pueden asignarse | [ ] |
| 5.4 | Estados de ruta | PROGRAMADA, EN_PROGRESO, COMPLETADA, CANCELADA | [ ] |
| 5.5 | Cambio automatico | Al activar ruta, paquetes cambian a EN_TRANSITO | [ ] |
| 5.6 | Manifiesto PDF | Documento imprimible con lista de paquetes | [ ] |
| 5.7 | Vista conductor | Panel simplificado con paquetes de su ruta | [ ] |

### Estados de Ruta

```
Estado Actual    | Estados Siguientes Permitidos
-----------------|------------------------------
PROGRAMADA       | EN_PROGRESO, CANCELADA
EN_PROGRESO      | COMPLETADA
COMPLETADA       | (ninguno - estado final)
CANCELADA        | (ninguno - estado final)
```

### Contenido del Manifiesto

El PDF de manifiesto debe incluir:
- Fecha y hora de salida
- Origen y destino de la ruta
- Nombre del conductor
- Placa del vehiculo
- Lista de paquetes con:
  - Codigo de rastreo
  - Destinatario
  - Telefono destinatario
  - Punto de destino especifico
- Firma del conductor (espacio en blanco)
- Total de paquetes

### Tests Requeridos

- [ ] Test de CRUD de rutas
- [ ] Test de asignacion de paquetes
- [ ] Test de validacion de rol conductor
- [ ] Test de generacion de manifiesto PDF

---

## MODULO 6: GESTION FINANCIERA

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 6.1 | Registro de pagos | Soporta efectivo, transferencia, tarjeta | [ ] |
| 6.2 | Workflow reembolsos | PENDIENTE -> APROBADO -> PAGADO | [ ] |
| 6.3 | Caja por punto | Balance en tiempo real por sucursal | [ ] |
| 6.4 | Cierre de caja | Reporte diario con totales | [ ] |
| 6.5 | Conciliacion | Cuentas por cobrar/pagar entre puntos | [ ] |
| 6.6 | Exportacion | Reportes en Excel y PDF | [ ] |
| 6.7 | Auditoria | Log de toda transaccion con usuario/timestamp | [ ] |

### Workflow de Reembolso

```
1. Destinatario paga en punto destino
   └─> Se crea registro de Reembolso con estado PENDIENTE

2. Sistema notifica a remitente (SMS/Email)
   └─> Estado cambia a APROBADO

3. Remitente retira dinero en punto origen
   └─> Estado cambia a PAGADO
   └─> Se registra transaccion de salida
```

### Precision Requerida

| Metrica | Valor Objetivo |
|---------|----------------|
| Discrepancia en balances | 0% |
| Trazabilidad | 100% de transacciones auditables |
| Tiempo de conciliacion | < 5 segundos |

### Estructura de Transaccion

```typescript
interface Transaction {
  id: number;
  packageId: number;
  type: string;           // 'PAGO_ENVIO' | 'COBRO_DESTINO' | 'REEMBOLSO'
  amount: Decimal;
  paymentMethod: string;  // 'EFECTIVO' | 'TRANSFERENCIA' | 'TARJETA'
  pointId: number;        // Punto donde se realizo
  userId: number;         // Usuario que registro
  reference?: string;     // Referencia externa
  createdAt: Date;
}
```

### Tests Requeridos

- [ ] Test de registro de pagos
- [ ] Test de workflow completo de reembolso
- [ ] Test de balance de caja
- [ ] Test de cierre de caja
- [ ] Test de conciliacion entre puntos

---

## MODULO 7: DASHBOARD ADMINISTRATIVO

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 7.1 | KPIs tiempo real | Actualizacion cada 30 segundos maximo | [ ] |
| 7.2 | Graficos interactivos | Chart.js o similar integrado | [ ] |
| 7.3 | Filtros por fecha | Rango de fechas seleccionable | [ ] |
| 7.4 | Filtros por punto | Seleccion de sucursal especifica | [ ] |
| 7.5 | Filtros por estado | Seleccion multiple de estados | [ ] |
| 7.6 | Exportacion | PDF y Excel de datos filtrados | [ ] |
| 7.7 | Alertas | Notificacion de paquetes >5 dias sin movimiento | [ ] |
| 7.8 | Responsive | Funciona en desktop y tablet | [ ] |

### KPIs Requeridos

| # | KPI | Descripcion | Visualizacion |
|---|-----|-------------|---------------|
| 1 | Total paquetes | Hoy / esta semana / este mes | Numero grande |
| 2 | Por estado | Cantidad por cada estado | Grafico de dona |
| 3 | Tiempo promedio | Dias desde recepcion hasta entrega | Numero + tendencia |
| 4 | Por punto | Paquetes por sucursal | Grafico de barras |
| 5 | Ingresos | Total de ingresos por periodo | Numero + grafico linea |
| 6 | Tasa devolucion | Porcentaje de paquetes devueltos | Porcentaje |
| 7 | Rezagados | Paquetes >5 dias sin movimiento | Lista con alerta |

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo de carga inicial | < 3 segundos |
| Actualizacion de KPIs | < 1 segundo |
| Exportacion de datos | < 5 segundos para 10,000 registros |

### Tests Requeridos

- [ ] Test de calculo correcto de KPIs
- [ ] Test de filtros combinados
- [ ] Test de exportacion a PDF/Excel
- [ ] Test de responsive design

---

## MODULO 8: CHATBOT WHATSAPP

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Estado |
|---|-----------|-------------------|--------|
| 8.1 | Conexion WhatsApp | Conexion estable con WhatsApp Business API | [ ] |
| 8.2 | Rastreo por mensaje | Responde consultas de rastreo correctamente | [ ] |
| 8.3 | FAQ automatico | Responde preguntas de horarios, ubicaciones, precios | [ ] |
| 8.4 | Lenguaje natural | Comprende variaciones del espanol salvadoreno | [ ] |
| 8.5 | Escalamiento | Transfiere a humano cuando no puede resolver | [ ] |
| 8.6 | Disponibilidad | Funciona 24/7 | [ ] |

### Capacidades Conversacionales

El chatbot debe manejar:
- "Donde esta mi paquete SV20260108SS1A2B3"
- "Mi encargo ya llego?" (con busqueda por telefono)
- "Cuanto cuesta enviar a Santa Ana?"
- "Cual es el horario del punto de San Salvador?"
- "Quiero hablar con una persona"

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo de respuesta | < 30 segundos |
| Tasa de resolucion sin humano | 70-80% |
| Uptime | 99.9% |

### Tests Requeridos

- [ ] Test de integracion con WhatsApp API (sandbox)
- [ ] Test de flujos conversacionales
- [ ] Test de comprension de variaciones de lenguaje
- [ ] Test de escalamiento a humano

---

## MODULO 9: APP MOVIL - FASE 1 CRITICA

> **IMPORTANTE:** La App Movil Flutter es el **PUNTO DE ENTRADA** para la recepcion de paquetes. Sin la app funcionando correctamente, no hay recepcion digital. Por esto es FASE 1 CRITICA.

### Criterios de Aceptacion Funcionales

| # | Requisito | Criterio de Exito | Fase | Estado |
|---|-----------|-------------------|------|--------|
| 9.1 | Compatibilidad iOS | Funciona en iOS 14+ | **FASE 1** | [ ] |
| 9.2 | Compatibilidad Android | Funciona en Android 8+ | **FASE 1** | [ ] |
| 9.3 | Login sincronizado | Mismas credenciales que web | **FASE 1** | [ ] |
| 9.4 | **Captura de vineta (CRITICO)** | **Camara funciona para capturar vinetas** | **FASE 1 CRITICO** | [ ] |
| 9.5 | **Integracion OCR (CRITICO)** | **Envia imagen al backend, recibe datos extraidos** | **FASE 1 CRITICO** | [ ] |
| 9.6 | **Validacion de datos (CRITICO)** | **Formulario para corregir datos OCR** | **FASE 1 CRITICO** | [ ] |
| 9.7 | **Generacion etiqueta QR** | **Muestra/imprime etiqueta con codigo rastreo** | **FASE 1** | [ ] |
| 9.8 | Lista de paquetes | Ver paquetes del punto de servicio | FASE 1 | [ ] |
| 9.9 | Cambio de estado | Actualizar estado de paquetes | FASE 1 | [ ] |
| 9.10 | Escaner QR | Deteccion en < 2 segundos | FASE 1 | [ ] |
| 9.11 | Modo offline | Operaciones basicas sin conexion | FASE 2 | [ ] |
| 9.12 | Sincronizacion | Datos se sincronizan al recuperar conexion | FASE 2 | [ ] |
| 9.13 | Push notifications | Alertas nativas en dispositivo | FASE 2 | [ ] |

### Flujo Critico de Recepcion (FASE 1)

```
1. Empleado abre la app y se autentica
         |
         v
2. Selecciona "Nuevo Paquete"
         |
         v
3. Toma FOTO de la vineta manuscrita (CAMARA - CRITICO)
         |
         v
4. App envia imagen al backend
         |
         v
5. Backend procesa con OCR (Google Vision)
         |
         v
6. App muestra datos extraidos para VALIDACION (CRITICO)
         |
         v
7. Empleado corrige si es necesario y confirma
         |
         v
8. Sistema genera codigo de rastreo y etiqueta QR
         |
         v
9. Empleado imprime etiqueta (opcional)
```

### Operaciones Offline (FASE 2)

En modo offline, la app debe permitir:
- Ver paquetes cacheados localmente
- Registrar nuevos paquetes (sincroniza despues)
- Cambiar estado de paquetes (sincroniza despues)
- Ver rutas asignadas

### Requisitos de Rendimiento

| Metrica | Valor Objetivo |
|---------|----------------|
| Tiempo de inicio | < 3 segundos |
| Tiempo de login | < 2 segundos |
| Deteccion QR | < 2 segundos |
| Tamano de app | < 50 MB |

### Tests Requeridos

- [ ] Test en dispositivo iOS fisico
- [ ] Test en dispositivo Android fisico
- [ ] Test de modo offline y sincronizacion
- [ ] Test de push notifications

---

## Criterios Generales de Calidad

### Cobertura de Testing

| Tipo de Test | Cobertura Minima |
|--------------|------------------|
| Unit tests | 70% |
| Integration tests | Criticos cubiertos |
| E2E tests | Flujos principales |

### Estandares de Codigo

- ESLint sin errores
- Prettier formateado
- Comentarios en funciones complejas
- Documentacion Swagger completa

### Documentacion Requerida

- [ ] README con instrucciones de instalacion
- [ ] Documentacion Swagger de APIs
- [ ] Guia de deployment
- [ ] Manual de usuario basico

---

*Documento generado: Enero 2026*
*Proyecto: Sistema Integral de Gestion Logistica (SIGL)*
