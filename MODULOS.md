# MODULOS - Sistema Integral de Gestion Logistica (SIGL)

## Resumen

El sistema SIGL se compone de **9 modulos principales** con **49 sub-modulos** que cubren todas las operaciones de la empresa de paqueteria.

---

## Modulo 1: AUTENTICACION Y SEGURIDAD (Base)

**Descripcion:** Sistema de control de acceso y gestion de usuarios. Es el modulo base del que dependen todos los demas.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 1.1 | Login/Logout | Sistema de autenticacion JWT con tokens de acceso y refresh | Fase 1 |
| 1.2 | Gestion de Usuarios | CRUD completo de usuarios con validaciones | Fase 1 |
| 1.3 | Sistema de Roles | 4 niveles de acceso diferenciados | Fase 1 |
| 1.4 | Gestion de Puntos/Sucursales | CRUD de puntos de servicio geograficos | Fase 1 |
| 1.5 | 2FA | Autenticacion de dos factores para administradores | Fase 2 |

### Roles del Sistema

```
SUPER_ADMIN      -> Acceso total al sistema
ADMINISTRADOR    -> Gestion de usuarios, reportes, configuraciones
EMPLEADO_PUNTO   -> Operaciones de recepcion, entrega, pagos
CONDUCTOR        -> Vista de rutas asignadas, cambios de estado basicos
```

---

## Modulo 2: RECEPCION DIGITAL

**Descripcion:** Digitalizacion de vinetas manuscritas mediante OCR/IA y registro de paquetes en el sistema.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 2.1 | Captura de Vineta | Interfaz web/movil para tomar foto de vineta manuscrita | Fase 1 |
| 2.2 | OCR/IA | Integracion con Google Cloud Vision API para extraccion automatica de texto | Fase 1 |
| 2.3 | Validacion de Datos | Formulario de correccion/confirmacion de datos extraidos | Fase 1 |
| 2.4 | Generacion de Codigo de Rastreo | Algoritmo de generacion de codigo unico (SV+fecha+punto+random) | Fase 1 |
| 2.5 | Impresion de Etiqueta | Generacion de etiqueta con codigo QR para pegar en paquete | Fase 1 |
| 2.6 | Almacenamiento de Imagenes | Servicio MinIO (S3-compatible) para guardar imagenes de vinetas | Fase 1 |

### Flujo de Recepcion

```
1. Empleado toma foto de vineta -> 2. OCR extrae datos -> 3. Empleado valida/corrige
                                                                    |
4. Sistema genera codigo rastreo <- 5. Imprime etiqueta QR <- 6. Guarda en BD
```

---

## Modulo 3: RASTREO EN TIEMPO REAL

**Descripcion:** Sistema de seguimiento de paquetes con portal publico y gestion de estados.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 3.1 | Portal Publico de Rastreo | UI sin login para consulta de paquetes por codigo | Fase 1 |
| 3.2 | API de Estados | Endpoints para cambio de estado con validacion de transiciones | Fase 1 |
| 3.3 | Historial de Estados | Registro cronologico de todos los cambios de estado con usuario/timestamp | Fase 1 |
| 3.4 | Busqueda Avanzada | Busqueda por codigo, telefono remitente, telefono destinatario, nombre | Fase 1 |
| 3.5 | Maquina de Estados | 7 estados con transiciones validadas | Fase 1 |

### Estados del Paquete

```
RECIBIDO_ORIGEN ──► EN_TRANSITO ──► LLEGO_DESTINO ──► ENTREGADO
       │                │                 │
       ▼                ▼                 ▼
   CANCELADO      EN_DEVOLUCION ──► DEVUELTO
```

---

## Modulo 4: NOTIFICACIONES AUTOMATICAS

**Descripcion:** Sistema de envio automatico de SMS y emails a clientes.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 4.1 | Integracion SMS (Twilio) | Envio de SMS automaticos al cambiar estado | Fase 1 |
| 4.2 | Integracion Email (SendGrid) | Envio de emails automaticos | Fase 1 |
| 4.3 | Plantillas de Notificacion | 8 tipos de plantillas personalizables | Fase 1 |
| 4.4 | Sistema de Reintentos | Cola de reintentos con backoff exponencial | Fase 1 |
| 4.5 | Historial de Notificaciones | Registro de todas las notificaciones enviadas/fallidas | Fase 1 |

### Tipos de Notificacion

1. Paquete recibido en origen
2. Paquete en transito
3. Paquete llego a destino (listo para retirar)
4. Paquete entregado
5. Reembolso disponible
6. Recordatorio de retiro (3 dias)
7. Paquete en devolucion
8. Paquete devuelto al remitente

---

## Modulo 5: GESTION DE RUTAS Y TRANSPORTE

**Descripcion:** Administracion de rutas de transporte entre sucursales y asignacion de paquetes.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 5.1 | CRUD de Rutas | Crear rutas con origen, destino, fecha programada | Fase 2 |
| 5.2 | Asignacion de Paquetes | Asignar multiples paquetes a una ruta especifica | Fase 2 |
| 5.3 | Asignacion de Conductores | Vincular conductor y vehiculo a ruta | Fase 2 |
| 5.4 | Estados de Ruta | PROGRAMADA, EN_PROGRESO, COMPLETADA, CANCELADA | Fase 2 |
| 5.5 | Manifiesto de Carga | Generacion de documento PDF imprimible para conductor | Fase 2 |
| 5.6 | Vista Conductor | Panel simplificado para conductores con sus rutas asignadas | Fase 2 |
| 5.7 | GPS/Geolocalizacion | Seguimiento en tiempo real de vehiculos (opcional) | Fase 3 |

### Estados de Ruta

```
PROGRAMADA ──► EN_PROGRESO ──► COMPLETADA
     │
     ▼
 CANCELADA
```

---

## Modulo 6: GESTION FINANCIERA

**Descripcion:** Control de pagos, reembolsos y conciliacion entre sucursales.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 6.1 | Registro de Pagos | CRUD de transacciones (efectivo, transferencia, tarjeta) | Fase 2 |
| 6.2 | Control de Reembolsos | Workflow de reembolsos: PENDIENTE -> APROBADO -> PAGADO | Fase 2 |
| 6.3 | Caja por Punto/Sucursal | Balance y cierre de caja por ubicacion | Fase 2 |
| 6.4 | Conciliacion entre Sucursales | Sistema de cuentas por cobrar/pagar entre puntos | Fase 2 |
| 6.5 | Reportes Financieros | Exportacion a Excel/PDF | Fase 2 |
| 6.6 | Auditoria de Transacciones | Log completo de todas las operaciones financieras | Fase 2 |

### Workflow de Reembolso

```
Destinatario paga en destino -> Se registra deuda hacia remitente
                                         |
Remitente consulta saldo disponible <- Sistema notifica automaticamente
                                         |
Remitente retira en punto de origen <- Estado: PAGADO
```

---

## Modulo 7: DASHBOARD ADMINISTRATIVO

**Descripcion:** Panel de control con metricas, graficos y reportes para la gerencia.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 7.1 | KPIs en Tiempo Real | Metricas actualizadas: paquetes procesados, tiempos, estados | Fase 1/2 |
| 7.2 | Graficos Interactivos | Visualizacion de tendencias con Chart.js | Fase 2 |
| 7.3 | Alertas Proactivas | Notificaciones de anomalias o paquetes rezagados | Fase 2 |
| 7.4 | Exportacion de Reportes | PDF/Excel de datos operativos y financieros | Fase 2 |
| 7.5 | Filtros Avanzados | Por fecha, punto, estado, empleado | Fase 2 |

### KPIs Principales

- Total paquetes procesados (hoy/semana/mes)
- Paquetes por estado actual
- Tiempo promedio de entrega
- Paquetes por punto de servicio
- Ingresos por periodo
- Tasa de devolucion
- Paquetes rezagados (>5 dias)

---

## Modulo 8: CHATBOT WHATSAPP

**Descripcion:** Asistente virtual 24/7 para atencion al cliente via WhatsApp.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 8.1 | Integracion WhatsApp Business API | Conexion con Meta/WhatsApp | Fase 3 |
| 8.2 | Flujos Conversacionales | Rastreo por mensaje, FAQ, horarios | Fase 3 |
| 8.3 | IA/NLP | Interpretacion de consultas en lenguaje natural (GPT/Claude) | Fase 3 |
| 8.4 | Escalamiento a Humano | Transferencia a agente cuando el bot no puede resolver | Fase 3 |
| 8.5 | Notificaciones Proactivas | Mensajes automaticos de estado por WhatsApp | Fase 3 |

### Capacidades del Chatbot

- Rastreo de paquetes por codigo
- Informacion de horarios y ubicaciones
- Consulta de precios/tarifas
- Estado de reembolsos
- Atencion 24/7 sin intervencion humana (70-80% de consultas)

---

## Modulo 9: APP MOVIL (Flutter) - FASE 1 CRITICO

**Descripcion:** Aplicacion nativa para empleados en iOS y Android. **PUNTO DE ENTRADA PRINCIPAL** para la recepcion de paquetes - la captura de fotos de vinetas se realiza exclusivamente desde la app movil.

> **IMPORTANTE:** La App Movil es CRITICA para el MVP porque todo el proceso de recepcion de paquetes INICIA desde el telefono del empleado. Sin la app movil funcional, no hay recepcion digital.

| ID | Sub-modulo | Descripcion | Fase |
|----|------------|-------------|------|
| 9.1 | App Flutter iOS/Android | Aplicacion nativa con UI optimizada | **Fase 1** |
| 9.2 | Captura de Vineta (Camara) | Camara optimizada para capturar vinetas manuscritas | **Fase 1 CRITICO** |
| 9.3 | Integracion OCR | Envio de imagen al backend y recepcion de datos extraidos | **Fase 1 CRITICO** |
| 9.4 | Validacion de Datos | Formulario para corregir/confirmar datos del OCR | **Fase 1 CRITICO** |
| 9.5 | Generacion Etiqueta QR | Impresion de etiqueta con codigo de rastreo | **Fase 1** |
| 9.6 | Lista de Paquetes | Vista de paquetes del punto de servicio | Fase 1 |
| 9.7 | Cambio de Estado | Actualizar estado de paquetes desde el movil | Fase 1 |
| 9.8 | Escaner QR | Lectura rapida de codigos de paquetes | Fase 1 |
| 9.9 | Modo Offline | Cache local para operacion sin conexion | Fase 2 |
| 9.10 | Push Notifications | Alertas nativas en dispositivo | Fase 2 |

### Flujo de Recepcion desde App Movil

```
1. EMPLEADO abre App Flutter en su telefono
         |
         v
2. Toma FOTO de la vineta manuscrita (camara del dispositivo)
         |
         v
3. App envia imagen al BACKEND (NestJS)
         |
         v
4. Backend procesa con Google Vision API (OCR)
         |
         v
5. App recibe datos extraidos y los muestra al empleado
         |
         v
6. Empleado VALIDA/CORRIGE los datos en la app
         |
         v
7. App confirma y guarda el paquete en el sistema
         |
         v
8. Se genera codigo de rastreo y etiqueta QR (imprimible)
```

---

## Resumen de Modulos por Fase

| Fase | Modulos | Descripcion |
|------|---------|-------------|
| **Fase 1 (MVP)** | 1, 2, 3, 4, 7 (parcial), **9** | Sistema funcional con recepcion movil |
| **Fase 2 (Mejoras)** | 5, 6, 7 (completo) | Rutas, financiero, optimizaciones |
| **Fase 3 (Avanzado)** | 8 | Chatbot WhatsApp |

> **NOTA CRITICA:** El Modulo 9 (App Movil) se movio a Fase 1 porque es el PUNTO DE ENTRADA para la recepcion de paquetes. El empleado usa su telefono para capturar la foto de la vineta.

---

## Dependencias entre Modulos

```
┌──────────────────────────────────────────────────────────────────┐
│  AUTENTICACION (1)  <-- Modulo base, requerido por todos        │
└───────────────────────────────────┬──────────────────────────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    │                               │
                    ▼                               ▼
        ┌───────────────────┐           ┌───────────────────┐
        │  APP MOVIL (9)    │           │  FRONTEND WEB     │
        │  (PUNTO ENTRADA)  │           │  (ANGULAR)        │
        │  Captura foto     │           │  Gestion/Admin    │
        └─────────┬─────────┘           └─────────┬─────────┘
                  │                               │
                  │    ┌──────────────────────────┘
                  │    │
                  ▼    ▼
        ┌───────────────────┐
        │  RECEPCION (2)    │ <-- Backend procesa foto con OCR
        │  OCR + Paquetes   │
        └─────────┬─────────┘
                  │
                  ▼
        ┌───────────────────┐         ┌───────────────────┐
        │  RASTREO (3)      │────────►│  NOTIFICACIONES   │
        │  Estados          │         │       (4)         │
        └─────────┬─────────┘         └───────────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
┌───────────────┐   ┌───────────────┐
│   RUTAS (5)   │◄─►│ FINANCIERO(6) │
└───────┬───────┘   └───────┬───────┘
        │                   │
        └─────────┬─────────┘
                  ▼
        ┌───────────────────┐
        │   DASHBOARD (7)   │
        └─────────┬─────────┘
                  │
                  ▼
        ┌───────────────────┐
        │   CHATBOT (8)     │  <-- Fase 3
        └───────────────────┘
```

### Flujo Principal del Sistema

```
EMPLEADO CON TELEFONO (App Flutter)
         │
         │ 1. Toma foto de vineta
         ▼
    BACKEND (NestJS)
         │
         │ 2. Procesa con OCR (Google Vision)
         │ 3. Genera codigo de rastreo
         ▼
    APP MOVIL (Flutter)
         │
         │ 4. Empleado valida datos
         │ 5. Confirma paquete
         ▼
    FRONTEND WEB (Angular)
         │
         │ 6. Gestion administrativa
         │ 7. Dashboard y reportes
         ▼
    CLIENTES (Portal publico)
         │
         └── 8. Rastrean sus paquetes
```

---

*Documento generado: Enero 2026*
*Proyecto: Sistema Integral de Gestion Logistica (SIGL)*
