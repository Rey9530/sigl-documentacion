# DESARROLLO - Guia Tecnica para Desarrolladores

## Stack Tecnologico

### Backend (NestJS)
```
NestJS 10+
├── @nestjs/core              # Framework principal
├── @nestjs/platform-express  # HTTP adapter
├── TypeScript 5.x            # Tipado estatico
├── Prisma 7.x                # ORM
├── @nestjs/jwt               # Autenticacion JWT
├── @nestjs/passport          # Estrategias auth
├── bcrypt                    # Hashing passwords
├── class-validator           # Validacion DTOs
├── class-transformer         # Transformacion datos
├── @nestjs/schedule          # Tareas programadas
├── nestjs-pino               # Logging
└── jest                      # Testing
```

### Frontend Web (Angular)
```
Angular 17+
├── Angular CLI               # Herramienta de desarrollo
├── TypeScript 5.x            # Tipado estatico
├── Angular Router            # Navegacion
├── Angular Material / PrimeNG # Componentes UI
├── HttpClient                # Cliente HTTP
├── RxJS                      # Programacion reactiva
├── NgRx (opcional)           # Estado global
├── Karma + Jasmine           # Testing
└── Standalone Components     # Arquitectura moderna
```

### Frontend Movil (Flutter)
```
Flutter 3.x
├── Dart 3.x                  # Lenguaje
├── Provider / Riverpod       # Estado
├── Dio                       # HTTP client
├── GetIt                     # Inyeccion dependencias
├── Flutter Secure Storage    # Almacenamiento seguro
├── Image Picker              # Captura de camara
├── Flutter Local Notifications # Notificaciones
└── flutter_test              # Testing
```

### Base de Datos
```
PostgreSQL 14+
├── Prisma 7 Migrations       # Versionado de schema
├── pg_trgm                   # Busqueda fuzzy
└── Indices optimizados       # Performance
```

### Almacenamiento (MinIO)
```
MinIO
├── S3-compatible API         # Compatible con AWS S3
├── Buckets por tipo          # vinetas, documentos, reportes
└── Politicas de acceso       # Seguridad granular
```

### Infraestructura
```
Docker + Docker Compose
├── NestJS container
├── PostgreSQL container
├── MinIO container
├── Redis container (Fase 2)
└── Nginx (reverse proxy)
```

---

## Estructura del Proyecto

### Backend (NestJS)
```
/backend
├── /src
│   ├── /modules
│   │   ├── /auth
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── strategies/
│   │   │   │   └── jwt.strategy.ts
│   │   │   └── dto/
│   │   │       ├── login.dto.ts
│   │   │       └── register.dto.ts
│   │   │
│   │   ├── /packages
│   │   │   ├── packages.module.ts
│   │   │   ├── packages.controller.ts
│   │   │   ├── packages.service.ts
│   │   │   └── dto/
│   │   │       ├── create-package.dto.ts
│   │   │       └── update-status.dto.ts
│   │   │
│   │   ├── /routes
│   │   │   ├── routes.module.ts
│   │   │   ├── routes.controller.ts
│   │   │   └── routes.service.ts
│   │   │
│   │   ├── /payments
│   │   │   ├── payments.module.ts
│   │   │   ├── payments.controller.ts
│   │   │   └── payments.service.ts
│   │   │
│   │   ├── /notifications
│   │   │   ├── notifications.module.ts
│   │   │   ├── notifications.service.ts
│   │   │   └── templates/
│   │   │
│   │   └── /storage
│   │       ├── storage.module.ts
│   │       └── minio.service.ts
│   │
│   ├── /common
│   │   ├── /decorators
│   │   │   ├── roles.decorator.ts
│   │   │   └── current-user.decorator.ts
│   │   ├── /guards
│   │   │   ├── jwt-auth.guard.ts
│   │   │   └── roles.guard.ts
│   │   ├── /interceptors
│   │   │   ├── transform.interceptor.ts
│   │   │   └── logging.interceptor.ts
│   │   ├── /pipes
│   │   │   └── validation.pipe.ts
│   │   └── /filters
│   │       └── http-exception.filter.ts
│   │
│   ├── /prisma
│   │   ├── prisma.module.ts
│   │   └── prisma.service.ts
│   │
│   ├── /config
│   │   ├── configuration.ts
│   │   └── validation.ts
│   │
│   ├── app.module.ts
│   └── main.ts
│
├── /prisma
│   ├── schema.prisma
│   ├── /migrations
│   └── seed.ts
│
├── /test
│   ├── /unit
│   └── /e2e
│
├── docker-compose.yml
├── Dockerfile
├── .env.example
├── nest-cli.json
├── tsconfig.json
└── package.json
```

### Frontend Web (Angular)
```
/frontend-web
├── /src
│   ├── /app
│   │   ├── /core
│   │   │   ├── /guards
│   │   │   │   ├── auth.guard.ts
│   │   │   │   └── role.guard.ts
│   │   │   ├── /interceptors
│   │   │   │   ├── auth.interceptor.ts
│   │   │   │   └── error.interceptor.ts
│   │   │   ├── /services
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── api.service.ts
│   │   │   │   └── storage.service.ts
│   │   │   └── core.module.ts
│   │   │
│   │   ├── /shared
│   │   │   ├── /components
│   │   │   │   ├── header/
│   │   │   │   ├── sidebar/
│   │   │   │   ├── loading/
│   │   │   │   └── confirm-dialog/
│   │   │   ├── /pipes
│   │   │   │   └── date-format.pipe.ts
│   │   │   ├── /directives
│   │   │   │   └── has-role.directive.ts
│   │   │   └── shared.module.ts
│   │   │
│   │   ├── /features
│   │   │   ├── /auth
│   │   │   │   ├── login/
│   │   │   │   │   ├── login.component.ts
│   │   │   │   │   └── login.component.html
│   │   │   │   └── auth.routes.ts
│   │   │   │
│   │   │   ├── /reception
│   │   │   │   ├── reception.component.ts
│   │   │   │   ├── camera-capture/
│   │   │   │   ├── ocr-result/
│   │   │   │   └── reception.routes.ts
│   │   │   │
│   │   │   ├── /tracking
│   │   │   │   ├── tracking.component.ts
│   │   │   │   ├── tracking-result/
│   │   │   │   └── tracking.routes.ts
│   │   │   │
│   │   │   ├── /dashboard
│   │   │   │   ├── dashboard.component.ts
│   │   │   │   ├── package-list/
│   │   │   │   ├── stats-cards/
│   │   │   │   └── dashboard.routes.ts
│   │   │   │
│   │   │   └── /payments
│   │   │       ├── payments.component.ts
│   │   │       ├── refunds-list/
│   │   │       └── payments.routes.ts
│   │   │
│   │   ├── /models
│   │   │   ├── package.model.ts
│   │   │   ├── user.model.ts
│   │   │   └── route.model.ts
│   │   │
│   │   ├── app.component.ts
│   │   ├── app.config.ts
│   │   └── app.routes.ts
│   │
│   ├── /environments
│   │   ├── environment.ts
│   │   └── environment.prod.ts
│   │
│   ├── /assets
│   ├── index.html
│   ├── main.ts
│   └── styles.scss
│
├── angular.json
├── tsconfig.json
└── package.json
```

### Frontend Movil (Flutter)
```
/frontend-mobile
├── /lib
│   ├── /core
│   │   ├── /constants
│   │   │   ├── api_constants.dart
│   │   │   └── app_constants.dart
│   │   ├── /services
│   │   │   ├── api_service.dart
│   │   │   ├── auth_service.dart
│   │   │   ├── storage_service.dart
│   │   │   └── notification_service.dart
│   │   ├── /models
│   │   │   ├── package_model.dart
│   │   │   ├── user_model.dart
│   │   │   └── route_model.dart
│   │   ├── /utils
│   │   │   ├── validators.dart
│   │   │   └── formatters.dart
│   │   └── /providers
│   │       ├── auth_provider.dart
│   │       └── package_provider.dart
│   │
│   ├── /features
│   │   ├── /auth
│   │   │   ├── login_screen.dart
│   │   │   └── widgets/
│   │   ├── /reception
│   │   │   ├── reception_screen.dart
│   │   │   ├── camera_screen.dart
│   │   │   └── widgets/
│   │   ├── /tracking
│   │   │   ├── tracking_screen.dart
│   │   │   └── widgets/
│   │   ├── /delivery
│   │   │   ├── delivery_screen.dart
│   │   │   └── widgets/
│   │   └── /routes
│   │       ├── routes_screen.dart
│   │       └── widgets/
│   │
│   ├── /widgets
│   │   ├── custom_button.dart
│   │   ├── custom_text_field.dart
│   │   ├── loading_widget.dart
│   │   └── package_card.dart
│   │
│   ├── /config
│   │   ├── routes.dart
│   │   └── theme.dart
│   │
│   └── main.dart
│
├── /test
│   ├── /unit
│   └── /widget
│
├── pubspec.yaml
└── analysis_options.yaml
```

---

## Patrones de Diseno en NestJS

### 1. Modulos con Inyeccion de Dependencias

```typescript
// modules/packages/packages.module.ts
import { Module } from '@nestjs/common';
import { PackagesController } from './packages.controller';
import { PackagesService } from './packages.service';
import { PrismaModule } from '../../prisma/prisma.module';
import { StorageModule } from '../storage/storage.module';
import { NotificationsModule } from '../notifications/notifications.module';

@Module({
  imports: [PrismaModule, StorageModule, NotificationsModule],
  controllers: [PackagesController],
  providers: [PackagesService],
  exports: [PackagesService],
})
export class PackagesModule {}
```

### 2. Servicio con Prisma v7

```typescript
// modules/packages/packages.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { PrismaService } from '../../prisma/prisma.service';
import { CreatePackageDto } from './dto/create-package.dto';
import { PackageStatus } from '@prisma/client';

@Injectable()
export class PackagesService {
  constructor(private prisma: PrismaService) {}

  async findByTrackingCode(code: string) {
    const pkg = await this.prisma.package.findUnique({
      where: { trackingCode: code },
      include: {
        statusHistory: { orderBy: { createdAt: 'desc' } },
        origin: true,
        destination: true,
      },
    });

    if (!pkg) {
      throw new NotFoundException(`Paquete ${code} no encontrado`);
    }

    return pkg;
  }

  async updateStatus(id: number, status: PackageStatus, userId: number) {
    return this.prisma.$transaction(async (tx) => {
      const updated = await tx.package.update({
        where: { id },
        data: { currentStatus: status },
      });

      await tx.statusHistory.create({
        data: {
          packageId: id,
          status,
          userId,
        },
      });

      return updated;
    });
  }

  async create(dto: CreatePackageDto, userId: number) {
    const trackingCode = this.generateTrackingCode(dto.originCode);

    return this.prisma.package.create({
      data: {
        trackingCode,
        senderName: dto.senderName,
        senderPhone: dto.senderPhone,
        recipientName: dto.recipientName,
        recipientPhone: dto.recipientPhone,
        originId: dto.originPointId,
        destinationId: dto.destinationPointId,
        shippingCost: dto.shippingCost,
        packageValue: dto.packageValue,
        receptionUserId: userId,
      },
    });
  }

  private generateTrackingCode(originCode: string): string {
    const date = new Date().toISOString().slice(0, 10).replace(/-/g, '');
    const random = Math.random().toString(36).substring(2, 8).toUpperCase();
    return `SV${date}${originCode}${random}`;
  }
}
```

### 3. Controller con Decoradores

```typescript
// modules/packages/packages.controller.ts
import {
  Controller,
  Get,
  Post,
  Put,
  Body,
  Param,
  Query,
  UseGuards,
  ParseIntPipe,
} from '@nestjs/common';
import { ApiTags, ApiOperation, ApiBearerAuth } from '@nestjs/swagger';
import { PackagesService } from './packages.service';
import { CreatePackageDto } from './dto/create-package.dto';
import { UpdateStatusDto } from './dto/update-status.dto';
import { JwtAuthGuard } from '../../common/guards/jwt-auth.guard';
import { RolesGuard } from '../../common/guards/roles.guard';
import { Roles } from '../../common/decorators/roles.decorator';
import { CurrentUser } from '../../common/decorators/current-user.decorator';
import { Role } from '@prisma/client';

@ApiTags('packages')
@Controller('api/packages')
export class PackagesController {
  constructor(private readonly packagesService: PackagesService) {}

  @Get('track/:code')
  @ApiOperation({ summary: 'Rastreo publico de paquete' })
  async trackPackage(@Param('code') code: string) {
    return this.packagesService.findByTrackingCode(code);
  }

  @Post()
  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.SUPER_ADMIN, Role.EMPLEADO_PUNTO)
  @ApiBearerAuth()
  @ApiOperation({ summary: 'Crear nuevo paquete' })
  async create(
    @Body() dto: CreatePackageDto,
    @CurrentUser() user: { id: number },
  ) {
    return this.packagesService.create(dto, user.id);
  }

  @Put(':id/status')
  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.SUPER_ADMIN, Role.EMPLEADO_PUNTO, Role.CONDUCTOR)
  @ApiBearerAuth()
  async updateStatus(
    @Param('id', ParseIntPipe) id: number,
    @Body() dto: UpdateStatusDto,
    @CurrentUser() user: { id: number },
  ) {
    return this.packagesService.updateStatus(id, dto.status, user.id);
  }
}
```

### 4. DTO con Validacion

```typescript
// modules/packages/dto/create-package.dto.ts
import { IsString, IsNumber, IsOptional, Matches, Min, Max } from 'class-validator';
import { ApiProperty } from '@nestjs/swagger';

export class CreatePackageDto {
  @ApiProperty({ example: 'Juan Perez' })
  @IsString()
  @Min(2)
  @Max(200)
  senderName: string;

  @ApiProperty({ example: '+50378001234' })
  @IsString()
  @Matches(/^\+?[0-9]{8,15}$/, { message: 'Telefono invalido' })
  senderPhone: string;

  @ApiProperty({ example: 'Maria Garcia' })
  @IsString()
  @Min(2)
  @Max(200)
  recipientName: string;

  @ApiProperty({ example: '+50378005678' })
  @IsString()
  @Matches(/^\+?[0-9]{8,15}$/, { message: 'Telefono invalido' })
  recipientPhone: string;

  @ApiProperty({ example: 1 })
  @IsNumber()
  originPointId: number;

  @ApiProperty({ example: 'SS' })
  @IsString()
  originCode: string;

  @ApiProperty({ example: 2 })
  @IsNumber()
  destinationPointId: number;

  @ApiProperty({ example: 5.00 })
  @IsNumber()
  @Min(0)
  shippingCost: number;

  @ApiProperty({ example: 100.00, required: false })
  @IsOptional()
  @IsNumber()
  @Min(0)
  packageValue?: number;
}
```

### 5. Servicio MinIO para Almacenamiento

```typescript
// modules/storage/minio.service.ts
import { Injectable, OnModuleInit } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import * as Minio from 'minio';

@Injectable()
export class MinioService implements OnModuleInit {
  private client: Minio.Client;
  private bucketName: string;

  constructor(private configService: ConfigService) {
    this.client = new Minio.Client({
      endPoint: this.configService.get('MINIO_ENDPOINT'),
      port: this.configService.get('MINIO_PORT'),
      useSSL: this.configService.get('MINIO_USE_SSL') === 'true',
      accessKey: this.configService.get('MINIO_ACCESS_KEY'),
      secretKey: this.configService.get('MINIO_SECRET_KEY'),
    });
    this.bucketName = this.configService.get('MINIO_BUCKET');
  }

  async onModuleInit() {
    const exists = await this.client.bucketExists(this.bucketName);
    if (!exists) {
      await this.client.makeBucket(this.bucketName);
    }
  }

  async uploadFile(
    file: Express.Multer.File,
    folder: string,
  ): Promise<string> {
    const fileName = `${folder}/${Date.now()}-${file.originalname}`;

    await this.client.putObject(
      this.bucketName,
      fileName,
      file.buffer,
      file.size,
      { 'Content-Type': file.mimetype },
    );

    return fileName;
  }

  async getFileUrl(fileName: string): Promise<string> {
    return this.client.presignedGetObject(this.bucketName, fileName, 3600);
  }

  async deleteFile(fileName: string): Promise<void> {
    await this.client.removeObject(this.bucketName, fileName);
  }
}
```

---

## Ejemplos Angular

### Servicio HTTP

```typescript
// core/services/api.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';

@Injectable({ providedIn: 'root' })
export class ApiService {
  private baseUrl = environment.apiUrl;

  constructor(private http: HttpClient) {}

  get<T>(path: string, params?: any): Observable<T> {
    const httpParams = new HttpParams({ fromObject: params || {} });
    return this.http.get<T>(`${this.baseUrl}${path}`, { params: httpParams });
  }

  post<T>(path: string, body: any): Observable<T> {
    return this.http.post<T>(`${this.baseUrl}${path}`, body);
  }

  put<T>(path: string, body: any): Observable<T> {
    return this.http.put<T>(`${this.baseUrl}${path}`, body);
  }

  delete<T>(path: string): Observable<T> {
    return this.http.delete<T>(`${this.baseUrl}${path}`);
  }
}
```

### Componente Standalone

```typescript
// features/tracking/tracking.component.ts
import { Component, signal } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { MatInputModule } from '@angular/material/input';
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { ApiService } from '../../core/services/api.service';
import { Package } from '../../models/package.model';

@Component({
  selector: 'app-tracking',
  standalone: true,
  imports: [
    CommonModule,
    FormsModule,
    MatInputModule,
    MatButtonModule,
    MatCardModule,
  ],
  template: `
    <div class="tracking-container">
      <mat-card>
        <mat-card-header>
          <mat-card-title>Rastreo de Paquete</mat-card-title>
        </mat-card-header>
        <mat-card-content>
          <mat-form-field appearance="outline" class="full-width">
            <mat-label>Codigo de rastreo</mat-label>
            <input matInput [(ngModel)]="trackingCode" placeholder="SV20260108SS..." />
          </mat-form-field>
          <button mat-raised-button color="primary" (click)="search()">
            Buscar
          </button>
        </mat-card-content>
      </mat-card>

      @if (package()) {
        <mat-card class="result-card">
          <mat-card-header>
            <mat-card-title>{{ package()?.trackingCode }}</mat-card-title>
            <mat-card-subtitle>Estado: {{ package()?.currentStatus }}</mat-card-subtitle>
          </mat-card-header>
          <mat-card-content>
            <p><strong>Remitente:</strong> {{ package()?.senderName }}</p>
            <p><strong>Destinatario:</strong> {{ package()?.recipientName }}</p>
            <p><strong>Origen:</strong> {{ package()?.origin?.nombre }}</p>
            <p><strong>Destino:</strong> {{ package()?.destination?.nombre }}</p>
          </mat-card-content>
        </mat-card>
      }
    </div>
  `,
})
export class TrackingComponent {
  trackingCode = '';
  package = signal<Package | null>(null);

  constructor(private api: ApiService) {}

  search() {
    if (!this.trackingCode) return;

    this.api.get<Package>(`/api/packages/track/${this.trackingCode}`)
      .subscribe({
        next: (pkg) => this.package.set(pkg),
        error: () => this.package.set(null),
      });
  }
}
```

---

## Ejemplos Flutter

### Servicio API

```dart
// core/services/api_service.dart
import 'package:dio/dio.dart';
import '../constants/api_constants.dart';
import 'storage_service.dart';

class ApiService {
  late Dio _dio;
  final StorageService _storage;

  ApiService(this._storage) {
    _dio = Dio(BaseOptions(
      baseUrl: ApiConstants.baseUrl,
      connectTimeout: const Duration(seconds: 10),
      receiveTimeout: const Duration(seconds: 10),
    ));

    _dio.interceptors.add(InterceptorsWrapper(
      onRequest: (options, handler) async {
        final token = await _storage.getToken();
        if (token != null) {
          options.headers['Authorization'] = 'Bearer $token';
        }
        return handler.next(options);
      },
      onError: (error, handler) {
        if (error.response?.statusCode == 401) {
          // Handle token refresh or logout
        }
        return handler.next(error);
      },
    ));
  }

  Future<Response<T>> get<T>(String path, {Map<String, dynamic>? params}) {
    return _dio.get<T>(path, queryParameters: params);
  }

  Future<Response<T>> post<T>(String path, dynamic data) {
    return _dio.post<T>(path, data: data);
  }

  Future<Response<T>> put<T>(String path, dynamic data) {
    return _dio.put<T>(path, data: data);
  }

  Future<Response<T>> delete<T>(String path) {
    return _dio.delete<T>(path);
  }
}
```

### Pantalla de Rastreo

```dart
// features/tracking/tracking_screen.dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../../core/models/package_model.dart';
import '../../core/providers/package_provider.dart';
import '../../widgets/custom_text_field.dart';
import '../../widgets/package_card.dart';

class TrackingScreen extends StatefulWidget {
  const TrackingScreen({super.key});

  @override
  State<TrackingScreen> createState() => _TrackingScreenState();
}

class _TrackingScreenState extends State<TrackingScreen> {
  final _trackingController = TextEditingController();
  PackageModel? _package;
  bool _loading = false;
  String? _error;

  Future<void> _search() async {
    if (_trackingController.text.isEmpty) return;

    setState(() {
      _loading = true;
      _error = null;
    });

    try {
      final provider = context.read<PackageProvider>();
      final package = await provider.trackPackage(_trackingController.text);
      setState(() {
        _package = package;
        _loading = false;
      });
    } catch (e) {
      setState(() {
        _error = 'Paquete no encontrado';
        _package = null;
        _loading = false;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Rastreo de Paquete')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            CustomTextField(
              controller: _trackingController,
              label: 'Codigo de rastreo',
              hint: 'SV20260108SS...',
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _loading ? null : _search,
              child: _loading
                  ? const CircularProgressIndicator()
                  : const Text('Buscar'),
            ),
            const SizedBox(height: 24),
            if (_error != null)
              Text(_error!, style: const TextStyle(color: Colors.red)),
            if (_package != null) PackageCard(package: _package!),
          ],
        ),
      ),
    );
  }
}
```

---

## Variables de Entorno

```env
# .env.example

# App
NODE_ENV=development
PORT=3000
API_URL=http://localhost:3000

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/sigl

# JWT
JWT_SECRET=tu-secreto-super-seguro
JWT_EXPIRES_IN=30m
JWT_REFRESH_EXPIRES_IN=7d

# MinIO
MINIO_ENDPOINT=localhost
MINIO_PORT=9000
MINIO_USE_SSL=false
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin
MINIO_BUCKET=sigl-storage

# Google Cloud Vision
GOOGLE_APPLICATION_CREDENTIALS=/path/to/credentials.json

# Twilio
TWILIO_SID=ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_PHONE=+15551234567

# SendGrid
SENDGRID_API_KEY=SG.xxxxxxxxxxxxxxxxxxxxxxxx

# Redis (Fase 2)
REDIS_URL=redis://localhost:6379
```

---

## Docker Compose

```yaml
version: '3.8'

services:
  api:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://sigl:sigl123@postgres:5432/sigl
      - MINIO_ENDPOINT=minio
      - MINIO_PORT=9000
    depends_on:
      - postgres
      - minio
      - redis

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

  minio:
    image: minio/minio
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    volumes:
      - minio_data:/data
    ports:
      - "9000:9000"
      - "9001:9001"

  redis:
    image: redis:alpine
    volumes:
      - redis_data:/data
    ports:
      - "6379:6379"

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - api

volumes:
  postgres_data:
  minio_data:
  redis_data:
```

---

## Comandos de Desarrollo

```bash
# Backend (NestJS)
npm run start:dev      # Iniciar con hot reload
npm run build          # Compilar TypeScript
npm run start:prod     # Iniciar produccion
npm run test           # Ejecutar tests unitarios
npm run test:e2e       # Ejecutar tests e2e
npm run lint           # Verificar estilo
npx prisma migrate dev # Ejecutar migraciones
npx prisma db seed     # Cargar datos iniciales
npx prisma studio      # UI para base de datos

# Frontend Web (Angular)
ng serve               # Iniciar dev server
ng build               # Build produccion
ng build --watch       # Build con watch
ng test                # Ejecutar tests
ng lint                # Verificar estilo
ng generate component  # Generar componente

# Frontend Movil (Flutter)
flutter run            # Ejecutar en dispositivo/emulador
flutter build apk      # Build Android
flutter build ios      # Build iOS
flutter test           # Ejecutar tests
flutter analyze        # Analizar codigo

# Docker
docker-compose up -d   # Iniciar contenedores
docker-compose down    # Detener contenedores
docker-compose logs -f # Ver logs en tiempo real
```

---

## Siguiente Paso

Ver `ARQUITECTURA.md` para el diseno detallado del sistema.

---

*Guia de desarrollo - Sistema Logistico SIGL*
*Stack: NestJS + Angular + Flutter + PostgreSQL + Prisma v7 + MinIO*
