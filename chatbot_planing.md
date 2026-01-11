# Implementación Demo: WhatsApp (Baileys) + IA + Logística (NestJS/Prisma/Postgres) + Docker

## Rol
Actúa como un Ingeniero de Software Senior experto en **NestJS**, **Prisma**, **PostgreSQL**, **Docker** y automatización de WhatsApp con **Baileys** (`@whiskeysockets/baileys`). https://github.com/WhiskeySockets/Baileys Tienes acceso al repositorio y a la infraestructura actual del proyecto.

## Objetivo
Construir una **demo rápida** para un sistema logístico donde:
- Un cliente escribe por **WhatsApp**.
- El sistema usa **IA (GPT-4o-mini)** para entender la intención.
- Consulta una **DB (PostgreSQL)** para tracking de paquetes y datos relacionados.
- Responde por WhatsApp.
- Soporta **FAQ**.
- Soporta modo **HUMANO** (agentes) con “handoff” configurable.

## Requisito crítico: Multi-sucursal ("Puntos de servicio")
Tenemos múltiples sucursales llamadas **Puntos de servicio**.

### Bandeja de WhatsApp por Punto de Servicio
- **Cada Punto de servicio tiene su propio número/instancia de WhatsApp.**
- Debe existir una **bandeja** (inbox) aislada por Punto de servicio:
  - Conversaciones y mensajes deben estar asociados a `servicePointId`.
  - La lógica del bot no debe mezclar conversaciones entre Puntos.

### Reconexion por QR
- Si un WhatsApp se desconecta, **cualquier usuario autorizado** del Punto de servicio debe poder **reconectar** escaneando el QR nuevamente.
- Debe haber una forma de:
  - Ver estado (conectado/desconectado) por Punto.
  - Pedir/generar QR para un Punto específico.

### Múltiples instancias Baileys simultáneas
- Diseña un **Connection/Session Manager** que mantenga **múltiples sockets Baileys** activos, uno por Punto de servicio.

## Tecnologías obligatorias
- **NestJS** como framework principal.
- **Prisma** como ORM.
- **PostgreSQL** como DB.
- **Docker**: la app debe poder levantarse dockerizada.
- IA: **GPT-4o-mini**.

## Entregables
1. **Diseño/estructura** de módulos NestJS y dónde integrar cada cosa.
2. **Migraciones Prisma** necesarias.
3. Implementación de:
   - Multi-instancia Baileys por Punto de servicio.
   - Persistencia de sesiones de Baileys por Punto.
   - Web/API para: estado, generar QR, controlar modo bot/humano.
   - Orquestación IA + tools (funciones) + respuesta.
   - Persistencia de mensajes.
4. **Dockerfile** y `docker-compose.yml` (para demo) incluyendo Postgres.
5. Documentación de:
   - Cómo agregar un Punto de servicio.
   - Cómo conectar/reconectar WhatsApp de un Punto.
   - Cómo probar el flujo end-to-end.

## Modelo de datos (Prisma) – guía
Adapta a los modelos actuales del repo. Si ya existen tablas equivalentes, **no dupliques**: integra.

### Requeridos (mínimo)
- `ServicePoint` (Punto de servicio)
  - `id`
  - `name`
  - `phoneNumber` (el número de WhatsApp del Punto, si aplica)
  - `whatsappStatus` (CONNECTED/DISCONNECTED/CONNECTING)
  - `lastQrAt` (timestamp)
  - Campos necesarios para operación

- `Conversation`
  - `id`
  - `servicePointId`
  - `customerPhone`
  - `status` (`BOT` | `HUMAN`)
  - `assignedToUserId` (opcional)
  - `createdAt`, `lastMessageAt`

- `Message`
  - `id`
  - `conversationId`
  - `servicePointId`
  - `direction` (`IN` | `OUT`)
  - `content`
  - `senderType` (`CUSTOMER` | `BOT` | `AGENT`)
  - `providerMessageId` (para idempotencia)
  - `timestamp`

- `Shipment` (envíos/paquetes)
  - `id`
  - `servicePointId`
  - `trackingNumber` (número de guía)
  - `status`
  - `origin`, `destination`, `eta`, `lastUpdate`

- `Faq`
  - `id`
  - `servicePointId` (opcional; si es null => global)
  - `question`
  - `answer`
  - `keywords` (opcional)

## Definición importante
- **Número de guía / trackingNumber**: código que el cliente usa para rastrear el paquete (ej. `ABC123456`).

## Módulos NestJS sugeridos
- `WhatsAppModule`
  - `BaileysConnectionManagerService`
    - Administra sockets por `servicePointId`.
    - Mantiene un `Map<servicePointId, socket>`.
    - Expone: `start(servicePointId)`, `stop(servicePointId)`, `getStatus(servicePointId)`, `requestQr(servicePointId)`.
  - `WhatsAppWebhook/EventsService`
    - Suscribe a eventos de Baileys (mensajes entrantes, conexión, desconexión).

- `AiModule`
  - `AiOrchestratorService`
    - Llama a **GPT-4o-mini**.
    - Implementa tool-calling/funciones:
      - `consultar_guia(trackingNumber, servicePointId)`
      - `consultar_faq(query, servicePointId)`
      - `transferir_a_humano(reason, servicePointId, customerPhone)`

- `InboxModule`
  - `ConversationsService` / `MessagesService`
  - Endpoints para agentes:
    - Listar conversaciones por Punto de servicio.
    - Cambiar estado `BOT/HUMAN`.
    - Enviar mensaje como humano (a través de la instancia Baileys del Punto correspondiente).

- `AdminModule`
  - Endpoints para administración:
    - CRUD de `ServicePoint`.
    - Ver estado de WhatsApp por Punto.
    - Generar QR para reconectar.

## Flujo de procesamiento (debe implementarse)
1. **Mensaje entrante** llega por Baileys para un `servicePointId`.
2. Resolver/crear `Conversation` por `(servicePointId, customerPhone)`.
3. Guardar `Message` entrante.
4. **Idempotencia**: si `providerMessageId` ya existe, no procesar/responder.
5. Si `Conversation.status == HUMAN`, **no** auto-responder.
6. Si está en BOT:
   - Llamar `AiOrchestratorService`.
   - IA decide si usar tools.
   - Consultar DB con Prisma (shipments/faq).
   - Construir respuesta corta y clara.
7. Enviar respuesta por Baileys usando la **instancia del mismo Punto**.
8. Guardar `Message` saliente.

## Reglas de negocio mínimas
- Si el cliente pide tracking y no da número de guía, pedirlo con ejemplo.
- Si el tracking no existe, responder que no se encontró y pedir verificación.
- Para FAQs, responder con base en DB (no inventar políticas).
- Si el usuario se enoja, pide humano, o la confianza es baja, usar `transferir_a_humano`.

## IA: requisitos concretos
- Modelo: **GPT-4o-mini**.
- Respuestas cortas para WhatsApp.
- Prohibido inventar estados de envíos.
- Preferir tools cuando la pregunta involucre datos de DB.

## Manejo de sesión Baileys / reconexión
- Persistir credenciales por `servicePointId` (carpeta o almacenamiento) de manera que:
  - Reiniciar contenedor no pierda sesión (montar volumen en Docker).
  - Permita regenerar QR si se invalida.
- Al desconectarse:
  - Marcar `ServicePoint.whatsappStatus = DISCONNECTED`.
  - Exponer endpoint para solicitar QR del Punto.

## Docker (demo)
- Proveer:
  - `Dockerfile` para NestJS.
  - `docker-compose.yml` con:
    - API NestJS
    - Postgres
    - Volumen persistente para Postgres
    - Volumen persistente para sesiones Baileys (por ejemplo `/app/baileys_auth`)
- Documentar variables de entorno necesarias.

## Seguridad / permisos (mínimo viable)
- Definir un mecanismo simple para autorizar quién puede:
  - Generar QR de un Punto.
  - Cambiar una conversación a modo HUMANO.
  - Enviar mensajes como agente.

## Criterios de aceptación
- Puedo crear 2 Puntos de servicio y conectar 2 WhatsApps diferentes.
- Mensajes entrantes de cada WhatsApp se guardan con su `servicePointId`.
- El bot responde con tracking consultando Postgres.
- El bot responde FAQs consultando Postgres.
- Puedo pasar una conversación a HUMANO y el bot deja de responder.
- Si se desconecta un Punto, puedo pedir QR y reconectar sin afectar otros.
- Todo corre dockerizado para demo.

## Instrucciones para ti (Claude Code)
1. Revisa el repositorio y detecta:
   - Módulos existentes NestJS.
   - Estructura Prisma actual.
   - Convenciones (nombres, DTOs, controllers, services).
2. Propón cambios concretos y aplícalos.
3. Si falta info crítica (nombres reales de tablas/relaciones), pregunta antes de migrar.
