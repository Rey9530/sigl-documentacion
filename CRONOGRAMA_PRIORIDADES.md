# CRONOGRAMA DE PRIORIDADES - Sistema SIGL

## Resumen Ejecutivo

El desarrollo del Sistema Integral de Gestion Logistica (SIGL) se divide en **3 fases principales** con un enfoque iterativo que permite entregar valor desde las primeras semanas.

| Fase | Duracion | Objetivo |
|------|----------|----------|
| Fase 1 (MVP) | Semanas 1-8 | Sistema funcional con recepcion movil, rastreo y notificaciones |
| Fase 2 (Mejoras) | Semanas 9-14 | Rutas, financiero, optimizaciones |
| Fase 3 (Avanzado) | Continuo | Chatbot WhatsApp, integraciones avanzadas |

> **NOTA CRITICA:** La App Movil Flutter es parte de Fase 1 porque la captura de vinetas se hace desde el telefono del empleado.

---

## Vista General del Cronograma

```
Semana   1   2   3   4   5   6   7   8   9  10  11  12  13  14   +
         |   |   |   |   |   |   |   |   |   |   |   |   |   |
FASE 1   ████████████████████████████████
         [      MVP - Prioridad CRITICA     ]
                                         |
FASE 2                                   ████████████████████████
                                         [Mejoras - Prioridad ALTA]
                                                                 |
FASE 3                                                           ██████████...
                                                                 [Avanzado - Continuo]
```

---

## FASE 1: MVP (Semanas 1-8)

### Objetivo
Sistema funcional con las caracteristicas esenciales: recepcion digital, rastreo y notificaciones basicas.

### Inversion Estimada: $8,000 - $12,000 USD

---

### Semana 1-2: FUNDAMENTOS

**Prioridad: CRITICA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Configurar entorno de desarrollo (Docker, PostgreSQL, Node.js) | Backend | Ninguna | [ ] Pendiente |
| 2 | Crear estructura de proyecto (repositorio Git) | Full Stack | Ninguna | [ ] Pendiente |
| 3 | Disenar e implementar schema Prisma (9 entidades) | Backend | Docker | [ ] Pendiente |
| 4 | Configurar autenticacion JWT (@nestjs/jwt, @nestjs/passport) | Backend | BD funcionando | [ ] Pendiente |
| 5 | Implementar CRUD de usuarios con validaciones | Backend | Auth | [ ] Pendiente |
| 6 | Implementar CRUD de puntos/sucursales | Backend | Auth | [ ] Pendiente |
| 7 | Configurar guards de roles (RolesGuard) | Backend | Usuarios | [ ] Pendiente |
| 8 | **Configurar proyecto Flutter (estructura base)** | **Mobile** | Ninguna | [ ] Pendiente |
| 9 | **Implementar autenticacion en Flutter (login)** | **Mobile** | Auth Backend | [ ] Pendiente |

**HITO Semana 2:** Entorno de desarrollo completo, base de datos funcionando y app Flutter con login.

---

### Semana 3-4: MODULO DE RECEPCION (App Movil + Backend)

**Prioridad: CRITICA**

> **IMPORTANTE:** La captura de vinetas se realiza desde la App Movil Flutter, NO desde Angular.

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | **Implementar captura de camara en Flutter** | **Mobile** | Flutter base | [ ] Pendiente |
| 2 | **Crear pantalla de captura de vineta (Flutter)** | **Mobile** | Camara | [ ] Pendiente |
| 3 | Integrar Google Cloud Vision API para OCR | Backend | API key | [ ] Pendiente |
| 4 | Desarrollar servicio de procesamiento OCR | Backend | Vision API | [ ] Pendiente |
| 5 | **Crear formulario de validacion/correccion en Flutter** | **Mobile** | OCR Backend | [ ] Pendiente |
| 6 | Implementar algoritmo de generacion de codigo de rastreo | Backend | Paquetes CRUD | [ ] Pendiente |
| 7 | Configurar servicio MinIO para almacenamiento de imagenes | Backend | Docker | [ ] Pendiente |
| 8 | **Desarrollar generacion de etiqueta QR en Flutter** | **Mobile** | Codigo rastreo | [ ] Pendiente |
| 9 | Implementar CRUD completo de paquetes | Backend | Auth + Puntos | [ ] Pendiente |
| 10 | **Integrar flujo completo en Flutter (foto -> OCR -> validar -> guardar)** | **Mobile** | Todo anterior | [ ] Pendiente |

**HITO Semana 4:** Empleado puede registrar paquete desde su telefono: tomar foto, validar datos OCR, generar codigo.

---

### Semana 5-6: RASTREO Y ESTADOS

**Prioridad: CRITICA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Desarrollar API de cambio de estado (PUT /api/packages/:id/status) | Backend | Paquetes | [ ] Pendiente |
| 2 | Implementar maquina de estados con validaciones de transiciones | Backend | API estados | [ ] Pendiente |
| 3 | Crear portal publico de rastreo (UI sin login) | Frontend | API | [ ] Pendiente |
| 4 | Implementar historial de estados (StatusHistory entity) | Backend | Estados | [ ] Pendiente |
| 5 | Desarrollar busqueda por telefono y codigo | Full Stack | API | [ ] Pendiente |
| 6 | Implementar busqueda por nombre remitente/destinatario | Full Stack | API | [ ] Pendiente |

**HITO Semana 6:** Clientes pueden rastrear paquetes online mediante portal publico.

---

### Semana 7-8: NOTIFICACIONES Y DASHBOARD BASICO

**Prioridad: ALTA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Integrar Twilio para envio de SMS | Backend | Estados | [ ] Pendiente |
| 2 | Integrar SendGrid para envio de emails | Backend | Estados | [ ] Pendiente |
| 3 | Crear plantillas de notificacion (8 tipos) | Backend | Twilio + SendGrid | [ ] Pendiente |
| 4 | Implementar sistema de reintentos (cola con backoff) | Backend | Notificaciones | [ ] Pendiente |
| 5 | Desarrollar dashboard basico para empleados | Frontend | Auth + Paquetes | [ ] Pendiente |
| 6 | Implementar lista de paquetes con filtros basicos | Frontend | Dashboard | [ ] Pendiente |
| 7 | Crear conteo de paquetes por estado (widgets) | Full Stack | Dashboard | [ ] Pendiente |
| 8 | Generar documentacion Swagger de APIs | Backend | APIs completas | [ ] Pendiente |

**HITO Semana 8 (MVP):** Sistema funcional listo para pruebas con usuarios reales.

---

### Entregables Fase 1 (Checklist)

- [ ] Sistema de login con roles funcionando
- [ ] Recepcion de paquetes con OCR operativo
- [ ] Portal de rastreo publico accesible
- [ ] Notificaciones SMS/Email enviandose automaticamente
- [ ] Dashboard basico de empleados
- [ ] Documentacion Swagger completa
- [ ] Tests con cobertura >70%

---

## FASE 2: MEJORAS Y OPTIMIZACION (Semanas 9-14)

### Objetivo
Completar gestion financiera, sistema de rutas y optimizar rendimiento.

---

### Semana 9-10: MODULO FINANCIERO

**Prioridad: ALTA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Implementar CRUD de transacciones/pagos | Backend | Paquetes | [ ] Pendiente |
| 2 | Desarrollar workflow de reembolsos (3 estados) | Full Stack | Transacciones | [ ] Pendiente |
| 3 | Crear sistema de caja por punto/sucursal | Backend | Transacciones | [ ] Pendiente |
| 4 | Implementar conciliacion entre sucursales | Backend | Caja | [ ] Pendiente |
| 5 | Desarrollar exportacion de reportes Excel/PDF | Full Stack | Financiero | [ ] Pendiente |
| 6 | Implementar log de auditoria financiera | Backend | Transacciones | [ ] Pendiente |

**HITO Semana 10:** Gestion completa de pagos y reembolsos operativa.

---

### Semana 11-12: RUTAS Y CONDUCTORES

**Prioridad: ALTA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Implementar CRUD de rutas | Backend | Paquetes + Puntos | [ ] Pendiente |
| 2 | Desarrollar asignacion masiva de paquetes a rutas | Full Stack | Rutas | [ ] Pendiente |
| 3 | Crear asignacion de conductores con validacion | Full Stack | Rutas + Usuarios | [ ] Pendiente |
| 4 | Implementar estados de ruta con transiciones | Backend | Rutas | [ ] Pendiente |
| 5 | Desarrollar vista simplificada para conductores | Frontend | Rutas | [ ] Pendiente |
| 6 | Crear generador de manifiesto de carga (PDF) | Full Stack | Rutas | [ ] Pendiente |

**HITO Semana 12:** Sistema de rutas completamente funcional.

---

### Semana 13-14: OPTIMIZACIONES

**Prioridad: MEDIA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Implementar cache con Redis | Backend | Infraestructura | [ ] Pendiente |
| 2 | Optimizar queries con indices de BD | Backend | BD | [ ] Pendiente |
| 3 | Implementar 2FA para administradores | Full Stack | Auth | [ ] Pendiente |
| 4 | Aplicar mejoras UX basadas en feedback | Frontend | Testing | [ ] Pendiente |
| 5 | Desarrollar dashboard ejecutivo con KPIs avanzados | Full Stack | Datos acumulados | [ ] Pendiente |
| 6 | Crear sistema de alertas de paquetes rezagados | Full Stack | Dashboard | [ ] Pendiente |

**HITO Semana 14 (Fase 2):** Sistema optimizado con rutas y financiero completo.

---

### Entregables Fase 2 (Checklist)

- [ ] Gestion completa de pagos y reembolsos
- [ ] Sistema de rutas operativo
- [ ] Asignacion de conductores funcionando
- [ ] Cache Redis implementado
- [ ] 2FA para administradores
- [ ] Dashboard ejecutivo con KPIs
- [ ] Alertas automaticas de paquetes rezagados
- [ ] Rendimiento optimizado (<2s respuesta)

---

## FASE 3: FUNCIONALIDADES AVANZADAS (Continuo)

### Objetivo
Expandir capacidades con chatbot WhatsApp, analytics avanzado e integraciones.

> **NOTA:** La App Movil Flutter ya NO esta en Fase 3 - se movio a **Fase 1** porque es critica para la recepcion de paquetes.

---

### Sprint A: CHATBOT WHATSAPP

**Prioridad: MEDIA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Integrar WhatsApp Business API (Meta) | Backend | APIs de rastreo | [ ] Pendiente |
| 2 | Desarrollar flujos conversacionales basicos | Full Stack | WhatsApp API | [ ] Pendiente |
| 3 | Implementar rastreo por mensaje | Backend | Flujos | [ ] Pendiente |
| 4 | Integrar IA/NLP para comprension de lenguaje natural | Backend | Flujos | [ ] Pendiente |
| 5 | Desarrollar escalamiento a agente humano | Full Stack | Flujos | [ ] Pendiente |
| 6 | Implementar notificaciones proactivas por WhatsApp | Backend | Estados | [ ] Pendiente |

**Inversion Estimada:** $8,000 - $12,000 USD

---

### Sprint B: MEJORAS APP MOVIL (Fase 2-3)

**Prioridad: MEDIA** (funcionalidades avanzadas, la base ya esta en Fase 1)

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Implementar modo offline con cache local | Mobile | App base Fase 1 | [ ] Pendiente |
| 2 | Configurar push notifications (Firebase) | Mobile | App base | [ ] Pendiente |
| 3 | Optimizar camara para condiciones de baja luz | Mobile | Camara base | [ ] Pendiente |
| 4 | Agregar funcionalidades de conductor (rutas asignadas) | Mobile | Rutas Backend | [ ] Pendiente |

---

### Sprint C: ANALYTICS AVANZADO

**Prioridad: BAJA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Desarrollar dashboard ejecutivo avanzado | Full Stack | Datos historicos | [ ] Pendiente |
| 2 | Implementar prediccion de demanda (ML basico) | Backend | Datos acumulados | [ ] Pendiente |
| 3 | Crear optimizacion de rutas algoritmica | Backend | Rutas + GPS | [ ] Pendiente |
| 4 | Desarrollar alertas predictivas | Backend | ML | [ ] Pendiente |

---

### Sprint D: INTEGRACIONES EXTERNAS

**Prioridad: BAJA**

| # | Tarea | Responsable | Dependencias | Estado |
|---|-------|-------------|--------------|--------|
| 1 | Integrar pasarelas de pago (Stripe/Wompi) | Backend | Financiero | [ ] Pendiente |
| 2 | Desarrollar APIs publicas para terceros | Backend | APIs maduras | [ ] Pendiente |
| 3 | Implementar webhooks para sistemas externos | Backend | APIs | [ ] Pendiente |
| 4 | Configurar SSO empresarial (SAML/OAuth) | Backend | Auth avanzado | [ ] Pendiente |

---

## Orden de Implementacion por Dependencias

```
SEMANA 1-2                SEMANA 3-4               SEMANA 5-6              SEMANA 7-8
┌──────────┐            ┌──────────┐            ┌──────────┐            ┌──────────┐
│ Docker   │            │ OCR/IA   │            │ Estados  │            │ SMS/Email│
│ + BD     │───────────►│ Backend  │───────────►│ Rastreo  │───────────►│ Dashboard│
│ + Auth   │            │          │            │ Publico  │            │ Angular  │
└──────────┘            └──────────┘            └──────────┘            └──────────┘
      │                       │
      │                       │
      ▼                       ▼
┌──────────┐            ┌──────────┐
│ Flutter  │───────────►│ Flutter  │
│ Login    │            │ Camara   │
│ Base     │            │ Recepcion│ ◄── CRITICO: Punto de entrada
└──────────┘            └──────────┘
      │                                                                       │
      └───────────────────────────────────────────────────────────────────────┘
                                      MVP COMPLETO

SEMANA 9-10              SEMANA 11-12             SEMANA 13-14
┌──────────┐            ┌──────────┐            ┌──────────┐
│Transaccio│───────────►│ Rutas    │───────────►│ Redis    │
│Reembolsos│            │Conductore│            │ 2FA      │
│ Caja     │            │Manifiesto│            │ KPIs     │
└──────────┘            └──────────┘            └──────────┘
      │                                               │
      └───────────────────────────────────────────────┘
                    FASE 2 COMPLETA

CONTINUO (FASE 3)
┌──────────┐    ┌──────────┐    ┌──────────┐
│ Chatbot  │    │Analytics │    │Integracio│
│ WhatsApp │    │ ML       │    │ Pagos    │
└──────────┘    └──────────┘    └──────────┘
```

---

## Equipo Recomendado

| Rol | Cantidad | Dedicacion | Responsabilidades |
|-----|----------|------------|-------------------|
| Backend Developer (NestJS) | 1 | 100% | APIs, integraciones, BD, OCR |
| **Mobile Developer (Flutter)** | 1 | **100% (Fase 1)** | **App movil, captura de vinetas (CRITICO)** |
| Frontend Web Developer (Angular) | 1 | 100% | UI web, dashboard, portal rastreo |
| Full Stack / Lead | 1 | 50-100% | Arquitectura, code review |
| QA Tester | 1 | 50% | Testing, documentacion |

> **NOTA:** El Mobile Developer es CRITICO desde Fase 1 porque la recepcion de paquetes se hace desde la App Flutter.

---

## Riesgos y Contingencias

| Riesgo | Probabilidad | Plan de Contingencia |
|--------|--------------|---------------------|
| OCR impreciso | Media | Fallback a entrada manual |
| Retraso en desarrollo | Alta | Buffer del 20% en cada fase |
| SMS no entregados | Media | Dual channel (SMS + Email) |
| Resistencia usuarios | Alta | Capacitacion intensiva |
| Cambios de alcance | Alta | Proceso formal de cambios |

---

## Hitos Principales

| Semana | Hito | Criterio de Exito |
|--------|------|-------------------|
| 2 | Entorno listo | Docker + BD + Auth + **Flutter base con login** |
| 4 | **Recepcion movil funcional** | **Empleado puede tomar foto y registrar paquete desde telefono** |
| 6 | Rastreo online | Clientes consultan paquetes via portal web |
| 8 | **MVP Completo** | Sistema en produccion con recepcion movil |
| 10 | Financiero operativo | Pagos y reembolsos funcionando |
| 12 | Rutas completas | Asignacion de conductores operativa |
| 14 | **Fase 2 completa** | Sistema optimizado |

---

*Documento generado: Enero 2026*
*Proyecto: Sistema Integral de Gestion Logistica (SIGL)*
