# ARQUITECTURA - Diseno Tecnico del Sistema

## Vision General

El Sistema Integral de Gestion Logistica (SIGL) sigue una arquitectura de capas con separacion clara de responsabilidades.

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLIENTES                                    │
├─────────────┬─────────────┬─────────────┬─────────────┬─────────┤
│  Web App    │  App Movil  │  WhatsApp   │  Portal     │  APIs   │
│  (Angular)  │  (Flutter)  │  Chatbot    │  Publico    │  Ext.   │
└──────┬──────┴──────┬──────┴──────┬──────┴──────┬──────┴────┬────┘
       │             │             │             │           │
       └─────────────┴─────────────┴─────────────┴───────────┘
                                   │
                           ┌───────┴───────┐
                           │    NGINX      │
                           │  (Reverse     │
                           │   Proxy)      │
                           └───────┬───────┘
                                   │
       ┌───────────────────────────┴───────────────────────────┐
       │                    API BACKEND                         │
       │                    (NestJS)                            │
       ├────────────────────────────────────────────────────────┤
       │  Auth │ Packages │ Routes │ Payments │ Notifications   │
       │       │          │        │          │ Storage         │
       └───────────────────────────┬────────────────────────────┘
                                   │
       ┌───────────────────────────┴───────────────────────────┐
       │                   SERVICIOS                            │
       ├──────────┬──────────┬──────────┬──────────┬───────────┤
       │   OCR    │   SMS    │  Email   │  Cache   │  Storage  │
       │ (Google) │ (Twilio) │(SendGrid)│ (Redis)  │  (MinIO)  │
       └──────────┴──────────┴──────────┴──────────┴───────────┘
                                   │
       ┌───────────────────────────┴───────────────────────────┐
       │                   DATOS                                │
       ├───────────────────────────┬───────────────────────────┤
       │       PostgreSQL          │         MinIO             │
       │     (Base de Datos)       │   (Almacenamiento S3)     │
       │       Prisma v7           │      Vinetas/Docs         │
       └───────────────────────────┴───────────────────────────┘
```

---

## Stack Tecnologico

| Capa | Tecnologia | Version |
|------|------------|---------|
| Frontend Web | Angular | 17+ |
| Frontend Movil | Flutter | 3.x |
| Backend | NestJS | 10+ |
| ORM | Prisma | 7.x |
| Base de Datos | PostgreSQL | 14+ |
| Almacenamiento | MinIO | Latest |
| Cache | Redis | 7+ |
| Proxy | Nginx | Alpine |

---

## Modelo de Datos

### Diagrama Entidad-Relacion

```
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│    PUNTO     │       │    USUARIO   │       │     ROL      │
├──────────────┤       ├──────────────┤       ├──────────────┤
│ id (PK)      │       │ id (PK)      │       │ id (PK)      │
│ nombre       │◄──────│ punto_id(FK) │       │ nombre       │
│ codigo       │       │ rol_id (FK)  │──────►│ permisos     │
│ direccion    │       │ nombre       │       └──────────────┘
│ telefono     │       │ email        │
│ ciudad       │       │ password     │
│ departamento │       │ activo       │
│ latitud      │       └──────┬───────┘
│ longitud     │              │
│ activo       │              │
└──────┬───────┘              │
       │                      │
       │    ┌─────────────────┴─────────────────┐
       │    │                                   │
       │    ▼                                   ▼
┌──────┴───────┐                    ┌───────────────────┐
│   PAQUETE    │                    │ HISTORIAL_ESTADO  │
├──────────────┤                    ├───────────────────┤
│ id (PK)      │                    │ id (PK)           │
│ codigo_rastr │◄───────────────────│ paquete_id (FK)   │
│ remitente_*  │                    │ estado            │
│ destinatar_* │                    │ usuario_id (FK)   │
│ punto_orig   │──────────┐         │ comentario        │
│ punto_dest   │────────┐ │         │ fecha_cambio      │
│ costo_envio  │        │ │         └───────────────────┘
│ valor_paq    │        │ │
│ estado_act   │        │ │         ┌───────────────────┐
│ ruta_id      │───┐    │ │         │   TRANSACCION     │
│ usuario_rec  │   │    │ │         ├───────────────────┤
│ usuario_ent  │   │    │ │         │ id (PK)           │
│ imagen_url   │   │    │ │         │ paquete_id (FK)   │◄──┐
│ created_at   │   │    │ │         │ tipo              │   │
└──────────────┘   │    │ │         │ monto             │   │
                   │    │ │         │ metodo_pago       │   │
                   │    │ │         │ punto_id (FK)     │   │
       ┌───────────┘    │ │         │ usuario_id (FK)   │   │
       │                │ │         │ referencia        │   │
       ▼                │ │         │ created_at        │   │
┌──────────────┐        │ │         └───────────────────┘   │
│    RUTA      │        │ │                                 │
├──────────────┤        │ │         ┌───────────────────┐   │
│ id (PK)      │        │ │         │    REEMBOLSO      │   │
│ codigo       │        │ │         ├───────────────────┤   │
│ punto_orig   │◄───────┘ │         │ id (PK)           │   │
│ punto_dest   │◄─────────┘         │ paquete_id (FK)   │───┘
│ fecha_salida │                    │ monto             │
│ fecha_lleg_e │                    │ estado            │
│ fecha_lleg_r │                    │ punto_disp_id(FK) │
│ estado       │                    │ beneficiario_*    │
│ conductor_id │                    │ created_at        │
│ vehiculo     │                    └───────────────────┘
│ cant_paquete │
└──────────────┘        ┌───────────────────┐
                        │   NOTIFICACION    │
                        ├───────────────────┤
                        │ id (PK)           │
                        │ paquete_id (FK)   │
                        │ tipo              │
                        │ destinatario      │
                        │ canal (SMS/EMAIL) │
                        │ estado            │
                        │ intentos          │
                        │ error_mensaje     │
                        │ enviado_at        │
                        └───────────────────┘
```

---

## Schema Prisma v7

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

enum PackageStatus {
  RECIBIDO_ORIGEN
  EN_TRANSITO
  LLEGO_DESTINO
  ENTREGADO
  EN_DEVOLUCION
  DEVUELTO
  CANCELADO
}

enum Role {
  SUPER_ADMIN
  ADMINISTRADOR
  EMPLEADO_PUNTO
  CONDUCTOR
}

model Point {
  id           Int       @id @default(autoincrement())
  name         String    @db.VarChar(100)
  code         String    @unique @db.VarChar(20)
  address      String
  phone        String?   @db.VarChar(20)
  email        String?   @db.VarChar(100)
  city         String?   @db.VarChar(100)
  department   String?   @db.VarChar(100)
  latitude     Decimal?  @db.Decimal(10, 8)
  longitude    Decimal?  @db.Decimal(11, 8)
  schedule     String?   @db.VarChar(200)
  active       Boolean   @default(true)
  createdAt    DateTime  @default(now())

  users        User[]
  packagesFrom Package[] @relation("OriginPoint")
  packagesTo   Package[] @relation("DestinationPoint")
  routesFrom   Route[]   @relation("RouteOrigin")
  routesTo     Route[]   @relation("RouteDestination")

  @@map("points")
}

model User {
  id           Int       @id @default(autoincrement())
  name         String    @db.VarChar(200)
  email        String    @unique @db.VarChar(100)
  phone        String?   @db.VarChar(20)
  passwordHash String    @db.VarChar(255)
  role         Role      @default(EMPLEADO_PUNTO)
  pointId      Int?
  point        Point?    @relation(fields: [pointId], references: [id])
  active       Boolean   @default(true)
  lastAccess   DateTime?
  createdAt    DateTime  @default(now())

  receivedPackages  Package[]       @relation("ReceptionUser")
  deliveredPackages Package[]       @relation("DeliveryUser")
  statusHistory     StatusHistory[]
  transactions      Transaction[]
  routes            Route[]         @relation("DriverRoutes")

  @@map("users")
}

model Package {
  id                Int           @id @default(autoincrement())
  trackingCode      String        @unique @db.VarChar(50)
  senderName        String        @db.VarChar(200)
  senderPhone       String        @db.VarChar(20)
  senderDui         String?       @db.VarChar(20)
  recipientName     String        @db.VarChar(200)
  recipientPhone    String        @db.VarChar(20)
  originId          Int
  origin            Point         @relation("OriginPoint", fields: [originId], references: [id])
  destinationId     Int
  destination       Point         @relation("DestinationPoint", fields: [destinationId], references: [id])
  shippingCost      Decimal       @db.Decimal(10, 2)
  packageValue      Decimal?      @db.Decimal(10, 2)
  currentStatus     PackageStatus @default(RECIBIDO_ORIGEN)
  routeId           Int?
  route             Route?        @relation(fields: [routeId], references: [id])
  labelImageUrl     String?       @db.VarChar(500)
  receptionUserId   Int?
  receptionUser     User?         @relation("ReceptionUser", fields: [receptionUserId], references: [id])
  deliveryUserId    Int?
  deliveryUser      User?         @relation("DeliveryUser", fields: [deliveryUserId], references: [id])
  description       String?
  notes             String?
  createdAt         DateTime      @default(now())
  updatedAt         DateTime      @updatedAt

  statusHistory     StatusHistory[]
  transactions      Transaction[]
  refunds           Refund[]
  notifications     Notification[]

  @@index([currentStatus])
  @@index([originId])
  @@index([destinationId])
  @@index([createdAt])
  @@index([senderPhone])
  @@index([recipientPhone])
  @@map("packages")
}

model StatusHistory {
  id        Int           @id @default(autoincrement())
  packageId Int
  package   Package       @relation(fields: [packageId], references: [id])
  status    PackageStatus
  userId    Int?
  user      User?         @relation(fields: [userId], references: [id])
  comment   String?
  createdAt DateTime      @default(now())

  @@index([packageId])
  @@index([createdAt])
  @@map("status_history")
}

model Route {
  id                 Int       @id @default(autoincrement())
  code               String    @unique @db.VarChar(50)
  originId           Int
  origin             Point     @relation("RouteOrigin", fields: [originId], references: [id])
  destinationId      Int
  destination        Point     @relation("RouteDestination", fields: [destinationId], references: [id])
  departureDate      DateTime
  estimatedArrival   DateTime
  actualArrival      DateTime?
  status             String    @default("PROGRAMADA") @db.VarChar(30)
  driverId           Int?
  driver             User?     @relation("DriverRoutes", fields: [driverId], references: [id])
  vehiclePlate       String?   @db.VarChar(20)
  packageCount       Int       @default(0)
  createdAt          DateTime  @default(now())

  packages           Package[]

  @@map("routes")
}

model Transaction {
  id            Int      @id @default(autoincrement())
  packageId     Int
  package       Package  @relation(fields: [packageId], references: [id])
  type          String   @db.VarChar(50)
  amount        Decimal  @db.Decimal(10, 2)
  paymentMethod String   @db.VarChar(30)
  pointId       Int
  userId        Int
  user          User     @relation(fields: [userId], references: [id])
  reference     String?  @db.VarChar(100)
  createdAt     DateTime @default(now())

  @@index([packageId])
  @@index([type])
  @@index([createdAt])
  @@map("transactions")
}

model Refund {
  id              Int      @id @default(autoincrement())
  packageId       Int
  package         Package  @relation(fields: [packageId], references: [id])
  amount          Decimal  @db.Decimal(10, 2)
  status          String   @default("PENDIENTE") @db.VarChar(30)
  availablePointId Int
  beneficiaryName String   @db.VarChar(200)
  beneficiaryPhone String  @db.VarChar(20)
  createdAt       DateTime @default(now())
  paidAt          DateTime?

  @@index([status])
  @@index([beneficiaryPhone])
  @@map("refunds")
}

model Notification {
  id           Int      @id @default(autoincrement())
  packageId    Int
  package      Package  @relation(fields: [packageId], references: [id])
  type         String   @db.VarChar(50)
  recipient    String   @db.VarChar(200)
  channel      String   @db.VarChar(20)
  status       String   @default("PENDIENTE") @db.VarChar(30)
  attempts     Int      @default(0)
  errorMessage String?
  sentAt       DateTime?
  createdAt    DateTime @default(now())

  @@index([packageId])
  @@index([status])
  @@map("notifications")
}
```

---

## Estados de Paquete

### Maquina de Estados

```
                    ┌─────────────────────────────────────┐
                    │                                     │
                    ▼                                     │
┌────────────┐    ┌────────────┐    ┌────────────┐    ┌───┴────────┐
│ RECIBIDO   │───►│ EN_TRANSITO│───►│   LLEGO    │───►│ ENTREGADO  │
│  ORIGEN    │    │            │    │  DESTINO   │    │            │
└────────────┘    └─────┬──────┘    └─────┬──────┘    └────────────┘
      │                 │                 │
      │                 │                 │
      ▼                 ▼                 ▼
┌────────────┐    ┌────────────┐    ┌────────────┐
│ CANCELADO  │    │    EN      │───►│  DEVUELTO  │
│            │    │ DEVOLUCION │    │            │
└────────────┘    └────────────┘    └────────────┘
```

### Transiciones Validas

| Estado Actual | Estados Siguientes Permitidos |
|---------------|------------------------------|
| RECIBIDO_ORIGEN | EN_TRANSITO, CANCELADO |
| EN_TRANSITO | LLEGO_DESTINO, EN_DEVOLUCION |
| LLEGO_DESTINO | ENTREGADO, EN_DEVOLUCION |
| ENTREGADO | (ninguno - estado final) |
| EN_DEVOLUCION | DEVUELTO |
| DEVUELTO | (ninguno - estado final) |
| CANCELADO | (ninguno - estado final) |

---

## Flujos de Datos

### Flujo de Recepcion de Paquete

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Empleado │    │ Angular/ │    │  NestJS  │    │  MinIO   │    │ Google   │
│          │    │ Flutter  │    │          │    │          │    │ Vision   │
└────┬─────┘    └────┬─────┘    └────┬─────┘    └────┬─────┘    └────┬─────┘
     │               │               │               │               │
     │ 1. Foto vineta│               │               │               │
     │──────────────►│               │               │               │
     │               │ 2. Upload     │               │               │
     │               │──────────────►│               │               │
     │               │               │ 3. Guardar    │               │
     │               │               │──────────────►│               │
     │               │               │               │               │
     │               │               │ 4. OCR        │               │
     │               │               │──────────────────────────────►│
     │               │               │               │               │
     │               │               │◄──────────────────────────────│
     │               │               │ 5. Texto      │               │
     │               │ 6. Datos      │               │               │
     │               │◄──────────────│               │               │
     │ 7. Validar    │               │               │               │
     │◄──────────────│               │               │               │
     │               │               │               │               │
     │ 8. Confirmar  │               │               │               │
     │──────────────►│               │               │               │
     │               │ 9. Crear pkg  │               │               │
     │               │──────────────►│               │               │
     │               │               │ 10. Save DB   │               │
     │               │               │───────┐       │               │
     │               │               │◄──────┘       │               │
     │               │ 11. Codigo    │               │               │
     │               │◄──────────────│               │               │
     │ 12. Imprimir  │               │               │               │
     │◄──────────────│               │               │               │
```

### Flujo de Notificaciones

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ NestJS   │    │ Notif.   │    │  Twilio  │    │ Cliente  │
│ Service  │    │ Module   │    │          │    │          │
└────┬─────┘    └────┬─────┘    └────┬─────┘    └────┬─────┘
     │               │               │               │
     │ 1. Cambio     │               │               │
     │    estado     │               │               │
     │──────────────►│               │               │
     │               │ 2. Crear      │               │
     │               │    notif.     │               │
     │               │───────┐       │               │
     │               │◄──────┘       │               │
     │               │               │               │
     │               │ 3. Enviar SMS │               │
     │               │──────────────►│               │
     │               │               │ 4. Deliver    │
     │               │               │──────────────►│
     │               │               │               │
     │               │ 5. Webhook    │               │
     │               │◄──────────────│               │
     │               │               │               │
     │               │ 6. Update     │               │
     │               │    status     │               │
     │               │───────┐       │               │
     │               │◄──────┘       │               │
```

---

## Integraciones Externas

### MinIO (Almacenamiento S3-Compatible)

```
┌─────────────────────────────────────────────────────────────┐
│                         MinIO                                │
├─────────────────────────────────────────────────────────────┤
│  Endpoint: localhost:9000 (desarrollo)                       │
│  API: Compatible con AWS S3                                  │
│  Console: localhost:9001                                     │
│  Costo: Self-hosted (solo infraestructura)                  │
│                                                              │
│  Buckets:                                                    │
│  ├── sigl-vinetas      # Imagenes de vinetas                │
│  ├── sigl-documentos   # Documentos generados               │
│  └── sigl-reportes     # Reportes exportados                │
└─────────────────────────────────────────────────────────────┘

Configuracion NestJS:
{
  endPoint: 'minio',
  port: 9000,
  useSSL: false,
  accessKey: 'minioadmin',
  secretKey: 'minioadmin'
}
```

### Google Cloud Vision (OCR)

```
┌─────────────────────────────────────────────────────────────┐
│                    Google Cloud Vision                       │
├─────────────────────────────────────────────────────────────┤
│  Endpoint: vision.googleapis.com                             │
│  Metodo: textDetection                                       │
│  Auth: Service Account (JSON credentials)                    │
│  Costo: $1.50 / 1000 imagenes                               │
│  Precision: 92-98% en texto manuscrito                       │
└─────────────────────────────────────────────────────────────┘
```

### Twilio (SMS)

```
┌─────────────────────────────────────────────────────────────┐
│                       Twilio SMS                             │
├─────────────────────────────────────────────────────────────┤
│  Endpoint: api.twilio.com/2010-04-01/Messages               │
│  Auth: Account SID + Auth Token                              │
│  Costo: ~$0.05 / SMS (El Salvador)                          │
│  Delivery: 95%+ en <30 segundos                             │
└─────────────────────────────────────────────────────────────┘
```

### SendGrid (Email)

```
┌─────────────────────────────────────────────────────────────┐
│                      SendGrid Email                          │
├─────────────────────────────────────────────────────────────┤
│  Endpoint: api.sendgrid.com/v3/mail/send                    │
│  Auth: API Key (Bearer token)                                │
│  Costo: Gratis hasta 100/dia                                │
│  Templates: Dinamicos con variables                         │
└─────────────────────────────────────────────────────────────┘
```

---

## Infraestructura

### Arquitectura de Despliegue

```
┌─────────────────────────────────────────────────────────────┐
│                    Servidor / VPS                            │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                    Docker                            │    │
│  │                                                      │    │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐        │    │
│  │  │   Nginx   │  │  NestJS   │  │   Redis   │        │    │
│  │  │  :80/443  │  │   :3000   │  │   :6379   │        │    │
│  │  └───────────┘  └───────────┘  └───────────┘        │    │
│  │                                                      │    │
│  │  ┌───────────┐  ┌───────────┐                       │    │
│  │  │PostgreSQL │  │   MinIO   │                       │    │
│  │  │   :5432   │  │:9000/9001 │                       │    │
│  │  └───────────┘  └───────────┘                       │    │
│  │                                                      │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
│  Volumenes persistentes:                                     │
│  ├── postgres_data   # Datos de base de datos               │
│  ├── minio_data      # Archivos almacenados                 │
│  └── redis_data      # Cache persistente                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Docker Compose Completo

```yaml
version: '3.8'

services:
  # Reverse Proxy
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - api
    restart: unless-stopped

  # Backend API (NestJS)
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://sigl:sigl123@postgres:5432/sigl
      - JWT_SECRET=${JWT_SECRET}
      - JWT_EXPIRES_IN=30m
      - MINIO_ENDPOINT=minio
      - MINIO_PORT=9000
      - MINIO_USE_SSL=false
      - MINIO_ACCESS_KEY=minioadmin
      - MINIO_SECRET_KEY=minioadmin
      - MINIO_BUCKET=sigl-storage
      - REDIS_URL=redis://redis:6379
    depends_on:
      postgres:
        condition: service_healthy
      minio:
        condition: service_started
      redis:
        condition: service_started
    restart: unless-stopped

  # Base de Datos
  postgres:
    image: postgres:14-alpine
    environment:
      POSTGRES_USER: sigl
      POSTGRES_PASSWORD: sigl123
      POSTGRES_DB: sigl
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U sigl"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # Almacenamiento de Archivos
  minio:
    image: minio/minio:latest
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    volumes:
      - minio_data:/data
    ports:
      - "9000:9000"   # API S3
      - "9001:9001"   # Console Web
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 30s
      timeout: 20s
      retries: 3
    restart: unless-stopped

  # Cache
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    ports:
      - "6379:6379"
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # MinIO Setup (crea buckets iniciales)
  minio-setup:
    image: minio/mc:latest
    depends_on:
      minio:
        condition: service_healthy
    entrypoint: >
      /bin/sh -c "
      mc alias set myminio http://minio:9000 minioadmin minioadmin;
      mc mb --ignore-existing myminio/sigl-vinetas;
      mc mb --ignore-existing myminio/sigl-documentos;
      mc mb --ignore-existing myminio/sigl-reportes;
      mc anonymous set download myminio/sigl-vinetas;
      exit 0;
      "

volumes:
  postgres_data:
    driver: local
  minio_data:
    driver: local
  redis_data:
    driver: local

networks:
  default:
    name: sigl-network
```

### Dockerfile Backend (NestJS)

```dockerfile
# Dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
COPY prisma ./prisma/

RUN npm ci

COPY . .

RUN npx prisma generate
RUN npm run build

# Production
FROM node:20-alpine

WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/package*.json ./

EXPOSE 3000

CMD ["npm", "run", "start:prod"]
```

---

## Escalabilidad

### Fase 1 (MVP): Servidor Unico
- 1 VPS con Docker Compose
- PostgreSQL en contenedor
- MinIO local
- Capacidad: ~1000 paquetes/dia

### Fase 2: Separacion de Servicios
- API en contenedor dedicado
- PostgreSQL managed (opcional)
- MinIO con mas almacenamiento
- Redis para cache
- Capacidad: ~5000 paquetes/dia

### Fase 3+: Arquitectura Distribuida
- Load balancer (Nginx/HAProxy)
- Multiples instancias NestJS
- PostgreSQL con replicas
- MinIO cluster
- CDN para assets estaticos
- Capacidad: ~50000+ paquetes/dia

---

## Seguridad

### Capas de Seguridad

```
┌─────────────────────────────────────────────────────────────┐
│                    Capa de Red                               │
│  - Firewall (UFW)                                           │
│  - Solo puertos 80, 443, 22                                 │
│  - SSL/TLS obligatorio (Let's Encrypt)                      │
└─────────────────────────────────────────────────────────────┘
                           │
┌─────────────────────────────────────────────────────────────┐
│                   Capa de Aplicacion (NestJS)               │
│  - JWT con expiracion (@nestjs/jwt)                         │
│  - Rate limiting (@nestjs/throttler)                        │
│  - CORS configurado                                          │
│  - Helmet (headers de seguridad)                            │
│  - Guards y Decoradores para roles                          │
└─────────────────────────────────────────────────────────────┘
                           │
┌─────────────────────────────────────────────────────────────┐
│                    Capa de Datos                             │
│  - Prisma ORM (SQL injection prevention)                     │
│  - bcrypt para passwords (cost 12)                          │
│  - Encriptacion en transito (TLS)                           │
│  - Backups automaticos PostgreSQL                           │
│  - MinIO con politicas de acceso                            │
└─────────────────────────────────────────────────────────────┘
```

---

## Monitoreo

### Herramientas Recomendadas

| Aspecto | Herramienta | Costo |
|---------|-------------|-------|
| APM | Sentry | $0-26/mes |
| Logs | Pino + Logrotate | $0 |
| Metricas | Prometheus + Grafana | $0 |
| Uptime | UptimeRobot | $0 |
| Alertas | Slack/Discord/Telegram | $0 |

### Metricas Clave

- Response time promedio (< 200ms)
- Error rate (< 1%)
- CPU usage (< 70%)
- Memory usage (< 80%)
- Database connections (< 80% pool)
- MinIO storage usage
- SMS delivery rate (> 95%)

---

## Referencias

- Ver `sistema_logistica_diseno_completo.md` para casos de uso detallados
- Ver `DESARROLLO.md` para guia de implementacion
- Ver `PLANIFICACION.md` para cronograma

---

*Arquitectura tecnica - Sistema Logistico SIGL*
*Stack: NestJS + Angular + Flutter + PostgreSQL + Prisma v7 + MinIO*
