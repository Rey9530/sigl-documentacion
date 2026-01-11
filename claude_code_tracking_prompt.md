# Prompt para Claude Code — Implementación de Tracking (Angular + Backend)

Revisa los archivos `.md` en la raíz del proyecto para comprender la arquitectura, módulos existentes y convenciones del sistema de logística.

## Contexto del proyecto
- Frontend (Angular): `@frontend-tracking/`
- Backend: `@backend/`
- Estado actual: **no existen endpoints de tracking** para ser consumidos por `@frontend-tracking/`

## Objetivo
Implementar un sistema completo de rastreo de paquetes para clientes finales, creando:
1) Un **módulo de tracking en el backend** con endpoints REST.
2) Las funcionalidades necesarias en el **frontend Angular** (`@frontend-tracking/`) para que el cliente pueda buscar y visualizar el rastreo.

---

## 1) Backend (`@backend/`) — Crear módulo de Tracking

### 1.1 Endpoints requeridos (mínimo)
Implementa endpoints REST para permitir rastreo por número de guía:

- `GET /api/tracking/:trackingNumber`
  - Devuelve el estado actual del paquete + información relevante (ubicación actual, ETA si aplica, etc.).
- `GET /api/tracking/:trackingNumber/history`
  - Devuelve el historial de eventos del paquete (timeline).

### 1.2 Requerimientos funcionales
- Validación de `trackingNumber` (formato y presencia).
- Manejo de errores consistente:
  - `404` si el tracking no existe.
  - `400` si el formato es inválido.
  - `500` para errores inesperados (sin filtrar detalles sensibles).
- Respuestas JSON consistentes y tipadas (DTOs).
- Seguir la arquitectura existente del backend (carpetas, patrones, estilo, logging, middlewares, etc.).

### 1.3 Estructura sugerida (ajustar al framework existente)
- `tracking.controller` / `tracking.routes`
- `tracking.service`
- `tracking.dto` / `tracking.model`
- validadores

### 1.4 Contrato de respuesta (propuesta)
> Ajusta el contrato a los modelos reales existentes si ya hay entidades relacionadas (orders, shipments, packages, etc.), pero mantén consistencia y claridad.

Ejemplo de respuesta para `GET /api/tracking/:trackingNumber`:

```json
{
  "trackingNumber": "string",
  "status": "pending | in_transit | delivered | exception",
  "currentLocation": "string",
  "estimatedDelivery": "ISO-8601 string | null",
  "carrier": "string | null",
  "recipient": {
    "name": "string | null",
    "city": "string | null",
    "country": "string | null"
  }
}
```

Ejemplo de respuesta para `GET /api/tracking/:trackingNumber/history`:

```json
{
  "trackingNumber": "string",
  "events": [
    {
      "date": "ISO-8601 string",
      "location": "string",
      "status": "string",
      "description": "string"
    }
  ]
}
```

---

## 2) Frontend (`@frontend-tracking/`) — Tracking para clientes (Angular)

### 2.1 Funcionalidades requeridas (mínimo)
- Búsqueda por número de tracking.
- Visualización del **estado actual** del envío.
- Visualización de **historial de eventos** en formato timeline.
- Manejo de estados:
  - loading
  - success
  - error (tracking inexistente, error backend, sin conexión)
- Validación del input (formato mínimo / requerido).
- UI responsive y clara (cliente final).

### 2.2 Componentes sugeridos (ajustar a la estructura real)
- `TrackingSearchComponent`
  - Formulario (Reactive Forms) para tracking number
  - Validaciones y UX de errores
- `TrackingResultComponent`
  - Resumen del estado actual
- `TrackingTimelineComponent`
  - Lista/timeline de eventos
- `TrackingErrorComponent` (opcional)
  - Mensajes de error reutilizables

### 2.3 Servicios y tipado
- Crear `TrackingService` con `HttpClient` para consumir:
  - `GET /api/tracking/:trackingNumber`
  - `GET /api/tracking/:trackingNumber/history`
- Modelos TypeScript (`interfaces`) alineados a las respuestas del backend.
- Manejo RxJS con:
  - `catchError`
  - `finalize`
  - `map` si aplica
- Opcional: Interceptor para errores HTTP y/o base URL.

### 2.4 Routing
- Página de búsqueda: `/tracking`
- Página de resultados: `/tracking/:trackingNumber`
- Recomendada: Lazy-loading del módulo de tracking si encaja con el proyecto.

### 2.5 UI/UX
- Mobile-first
- Estados de carga (spinner/skeleton)
- Mensajes claros (ej: “No encontramos ese número de guía”)
- Timeline visual legible

---

## 3) Integración & Config
- Configurar CORS en backend si el frontend y backend corren en orígenes distintos.
- En frontend, usar `environment.ts` para la URL base del API.
- Mantener consistencia de códigos de error y mensajes entre backend y frontend.

---

## 4) Entregables
- Backend: módulo de tracking implementado y endpoints funcionando.
- Frontend: módulo/pantallas de tracking funcionales y responsive.
- Integración end-to-end funcionando.
- Breve documentación/nota de decisiones técnicas y supuestos.

---

## Restricciones
- Mantener el estilo y convenciones existentes del repositorio.
- No introducir dependencias innecesarias.
- Si el proyecto ya usa una librería UI (Angular Material / PrimeNG), reutilizarla; si no, mantener UI simple sin agregar peso.

## Supuestos
Si falta información (por ejemplo: dónde están los eventos de tracking en BD), documenta el supuesto y crea una implementación razonable siguiendo los modelos existentes del sistema (shipments, orders, packages, etc.).
