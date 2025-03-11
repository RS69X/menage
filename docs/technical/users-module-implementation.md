# Spécifications d'implémentation du module Users

## Table des matières
1. [Vue d'ensemble](#vue-densemble)
2. [Objectifs](#objectifs)
3. [Architecture du module](#architecture-du-module)
4. [Dépendances](#dépendances)
5. [Modèles de données](#modèles-de-données)
   - [Entité de base User](#entité-de-base-user)
   - [Entité Host (Hébergeur)](#entité-host-hébergeur)
   - [Entité Cleaner (Professionnel de ménage)](#entité-cleaner-professionnel-de-ménage)
   - [Relations entre entités](#relations-entre-entités)
6. [Composants principaux](#composants-principaux)
   - [DTOs](#dtos)
   - [Service utilisateurs](#service-utilisateurs)
   - [Contrôleur](#contrôleur)
   - [Repository](#repository)
7. [Fonctionnalités principales](#fonctionnalités-principales)
8. [API Endpoints](#api-endpoints)
9. [Sécurité et RGPD](#sécurité-et-rgpd)
10. [Tests](#tests)
11. [Intégration avec autres modules](#intégration-avec-autres-modules)

## Vue d'ensemble

Le module Users est la pierre angulaire de l'application Menage Connect. Il gère les données des deux types d'utilisateurs de la plateforme : les hébergeurs (hôtels, propriétaires Airbnb, etc.) et les professionnels de ménage. Ce module définit les entités de base, fournit les services de gestion des utilisateurs et expose les API pour manipuler ces données.

## Objectifs

1. Modéliser et stocker les données des deux types d'utilisateurs avec leurs spécificités
2. Fournir des mécanismes de création, récupération, mise à jour et suppression des profils
3. Gérer de manière sécurisée les données sensibles (bancaires, personnelles, professionnelles)
4. Supporter la validation des informations spécifiques (SIRET, coordonnées bancaires)
5. Fournir des services d'accès aux données utilisateurs pour les autres modules
6. Assurer la conformité RGPD pour le traitement des données personnelles

## Architecture du module

```
modules/users/
├── users.module.ts             # Définition du module et dépendances
├── users.controller.ts         # Points d'entrée de l'API
├── users.service.ts            # Logique métier utilisateurs
├── dto/                        # Objets de transfert de données
│   ├── create-user.dto.ts      # DTO base pour la création d'utilisateur
│   ├── create-host.dto.ts      # DTO pour la création d'hébergeur
│   ├── create-cleaner.dto.ts   # DTO pour la création de professionnel
│   ├── update-user.dto.ts      # DTO base pour la mise à jour d'utilisateur
│   ├── update-host.dto.ts      # DTO pour la mise à jour d'hébergeur
│   ├── update-cleaner.dto.ts   # DTO pour la mise à jour de professionnel
│   └── user-response.dto.ts    # DTO pour les réponses utilisateur
├── entities/                   # Entités de base de données
│   ├── user.entity.ts          # Entité de base utilisateur
│   ├── host.entity.ts          # Entité spécifique hébergeur
│   └── cleaner.entity.ts       # Entité spécifique professionnel
├── repositories/               # Repositories personnalisés (si nécessaire)
│   ├── host.repository.ts
│   └── cleaner.repository.ts
├── interfaces/                 # Interfaces et types
│   ├── user-type.enum.ts       # Enum des types d'utilisateur
│   └── user.interface.ts       # Interface utilisateur
└── tests/                      # Tests unitaires et d'intégration
    ├── users.controller.spec.ts
    └── users.service.spec.ts
```

## Dépendances

Le module Users dépend des packages suivants :

```typescript
// Dépendances NestJS
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

// Cryptographie pour les données sensibles
import { EncryptionModule } from '../common/encryption/encryption.module';

// Validation
import { validate } from 'class-validator';

// Entités
import { User } from './entities/user.entity';
import { Host } from './entities/host.entity';
import { Cleaner } from './entities/cleaner.entity';

// Modules externes pour validation spécifique (SIRET, IBAN)
import { ExternalValidationModule } from '../external-validation/external-validation.module';
```

## Modèles de données

### Entité de base User

L'entité `User` sert de base pour les deux types d'utilisateurs avec les attributs communs.

```typescript
@Entity('users')
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  email: string;

  @Column()
  password: string; // Stocké haché

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column()
  phoneNumber: string;

  @Column({
    type: 'enum',
    enum: UserType,
  })
  userType: UserType; // 'host' ou 'cleaner'

  // Adresse
  @Column()
  address: string;

  @Column()
  city: string;

  @Column()
  postalCode: string;

  // Statut du compte
  @Column({ default: false })
  isEmailVerified: boolean;

  @Column({ default: false })
  isIdentityVerified: boolean;

  @Column({ default: true })
  isActive: boolean;

  // Sécurité
  @Column({ nullable: true })
  refreshToken: string;

  // Timestamps
  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}
```

### Entité Host (Hébergeur)

L'entité `Host` étend l'entité `User` avec les attributs spécifiques aux hébergeurs.

```typescript
@Entity('hosts')
export class Host {
  @PrimaryColumn()
  userId: string;

  @OneToOne(() => User, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'userId' })
  user: User;

  @Column({ nullable: true })
  companyName?: string;

  @Column({ nullable: true })
  siret?: string;

  @Column({ default: 0 })
  averageRating: number;

  @Column({ default: 0 })
  ratingCount: number;

  // Relations avec autres modules
  // @OneToMany(() => Listing, listing => listing.host)
  // listings: Listing[];
}
```

### Entité Cleaner (Professionnel de ménage)

L'entité `Cleaner` étend l'entité `User` avec les attributs spécifiques aux professionnels de ménage.

```typescript
@Entity('cleaners')
export class Cleaner {
  @PrimaryColumn()
  userId: string;

  @OneToOne(() => User, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'userId' })
  user: User;

  // Informations professionnelles
  @Column()
  siret: string;

  @Column()
  isCompany: boolean;

  @Column({ nullable: true })
  companyName?: string;

  // Informations bancaires (chiffrées)
  @Column({ type: 'text' })
  encryptedIban: string;

  @Column({ type: 'text' })
  encryptedBic: string;

  // Préférences de travail
  @Column()
  workRadius: number; // Rayon de travail en km

  @Column({ default: 0 })
  averageRating: number;

  @Column({ default: 0 })
  ratingCount: number;

  // Relations avec autres modules
  // @OneToMany(() => Booking, booking => booking.cleaner)
  // bookings: Booking[];
}
```

### Relations entre entités

- **User ↔ Host/Cleaner**: Relation one-to-one avec cascade de suppression
- **Host ↔ Listings**: Relation one-to-many (un hébergeur peut avoir plusieurs annonces)
- **Cleaner ↔ Bookings**: Relation one-to-many (un professionnel peut avoir plusieurs réservations)

## Composants principaux

### DTOs

#### CreateHostDto
```typescript
export class CreateHostDto {
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsString()
  @IsNotEmpty()
  firstName: string;

  @IsString()
  @IsNotEmpty()
  lastName: string;

  @IsString()
  @IsNotEmpty()
  phoneNumber: string;

  @IsOptional()
  @IsString()
  companyName?: string;

  @IsOptional()
  @IsString()
  @IsSiret() // Validateur personnalisé
  siret?: string;

  // Adresse
  @IsString()
  @IsNotEmpty()
  address: string;

  @IsString()
  @IsNotEmpty()
  city: string;

  @IsString()
  @IsNotEmpty()
  postalCode: string;
}
```

#### CreateCleanerDto
```typescript
export class CreateCleanerDto {
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsString()
  @IsNotEmpty()
  firstName: string;

  @IsString()
  @IsNotEmpty()
  lastName: string;

  @IsString()
  @IsNotEmpty()
  phoneNumber: string;

  // Informations professionnelles
  @IsString()
  @IsNotEmpty()
  @IsSiret() // Validateur personnalisé
  siret: string;

  @IsBoolean()
  isCompany: boolean;

  @IsOptional()
  @IsString()
  companyName?: string;

  // Adresse
  @IsString()
  @IsNotEmpty()
  address: string;

  @IsString()
  @IsNotEmpty()
  city: string;

  @IsString()
  @IsNotEmpty()
  postalCode: string;

  // Informations bancaires
  @IsString()
  @IsNotEmpty()
  @IsIban() // Validateur personnalisé
  iban: string;

  @IsString()
  @IsNotEmpty()
  @IsBic() // Validateur personnalisé
  bic: string;

  // Préférences de travail
  @IsNumber()
  @Min(1)
  @Max(100)
  workRadius: number; // Rayon de travail en km
}
```

#### UpdateHostDto
```typescript
export class UpdateHostDto {
  @IsOptional()
  @IsString()
  firstName?: string;

  @IsOptional()
  @IsString()
  lastName?: string;

  @IsOptional()
  @IsString()
  phoneNumber?: string;

  @IsOptional()
  @IsString()
  companyName?: string;

  @IsOptional()
  @IsString()
  @IsSiret()
  siret?: string;

  // Adresse
  @IsOptional()
  @IsString()
  address?: string;

  @IsOptional()
  @IsString()
  city?: string;

  @IsOptional()
  @IsString()
  postalCode?: string;
}
```

#### UpdateCleanerDto
```typescript
export class UpdateCleanerDto {
  @IsOptional()
  @IsString()
  firstName?: string;

  @IsOptional()
  @IsString()
  lastName?: string;

  @IsOptional()
  @IsString()
  phoneNumber?: string;

  @IsOptional()
  @IsString()
  companyName?: string;

  // Adresse
  @IsOptional()
  @IsString()
  address?: string;

  @IsOptional()
  @IsString()
  city?: string;

  @IsOptional()
  @IsString()
  postalCode?: string;

  // Informations bancaires
  @IsOptional()
  @IsString()
  @IsIban()
  iban?: string;

  @IsOptional()
  @IsString()
  @IsBic()
  bic?: string;

  // Préférences de travail
  @IsOptional()
  @IsNumber()
  @Min(1)
  @Max(100)
  workRadius?: number;
}
```

#### UserResponseDto
```typescript
export class UserResponseDto {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  phoneNumber: string;
  userType: string;
  address: string;
  city: string;
  postalCode: string;
  isEmailVerified: boolean;
  isIdentityVerified: boolean;
  createdAt: Date;
  updatedAt: Date;

  // Champs spécifiques hébergeur
  companyName?: string;
  siret?: string;
  averageRating?: number;

  // Champs spécifiques professionnel
  isCompany?: boolean;
  workRadius?: number;

  // Note: pas d'informations bancaires dans la réponse
}
```

### Service utilisateurs

Le service utilisateurs (`users.service.ts`) implémentera les méthodes suivantes :

```typescript
// Méthodes principales
createHost(createHostDto: CreateHostDto): Promise<UserResponseDto>
createCleaner(createCleanerDto: CreateCleanerDto): Promise<UserResponseDto>
findById(id: string): Promise<UserResponseDto>
findByEmail(email: string): Promise<UserResponseDto>
findAllHosts(filters?: any): Promise<UserResponseDto[]>
findAllCleaners(filters?: any): Promise<UserResponseDto[]>
updateHost(id: string, updateHostDto: UpdateHostDto): Promise<UserResponseDto>
updateCleaner(id: string, updateCleanerDto: UpdateCleanerDto): Promise<UserResponseDto>
updateRefreshToken(userId: string, refreshToken: string): Promise<void>
verifyEmail(userId: string): Promise<void>
verifyIdentity(userId: string): Promise<void>
deactivateUser(userId: string): Promise<void>
deleteUser(userId: string): Promise<void>

// Méthodes internes
private encryptSensitiveData(data: string): Promise<string>
private decryptSensitiveData(encryptedData: string): Promise<string>
private validateSiret(siret: string): Promise<boolean>
private validateBankDetails(iban: string, bic: string): Promise<boolean>
```

### Contrôleur

Le contrôleur (`users.controller.ts`) exposera les endpoints suivants :

```typescript
@Get(':id')
@UseGuards(JwtAuthGuard)
findById(@Param('id') id: string): Promise<UserResponseDto>

@Get()
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin') // Seuls les administrateurs peuvent lister tous les utilisateurs
findAll(@Query() filters: any): Promise<UserResponseDto[]>

@Get('hosts')
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin')
findAllHosts(@Query() filters: any): Promise<UserResponseDto[]>

@Get('cleaners')
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin')
findAllCleaners(@Query() filters: any): Promise<UserResponseDto[]>

@Patch('hosts/:id')
@UseGuards(JwtAuthGuard)
updateHost(@Param('id') id: string, @Body() updateHostDto: UpdateHostDto): Promise<UserResponseDto>

@Patch('cleaners/:id')
@UseGuards(JwtAuthGuard)
updateCleaner(@Param('id') id: string, @Body() updateCleanerDto: UpdateCleanerDto): Promise<UserResponseDto>

@Delete(':id')
@UseGuards(JwtAuthGuard)
deleteUser(@Param('id') id: string): Promise<{ message: string }>
```

### Repository

Si des requêtes personnalisées complexes sont nécessaires au-delà de ce que fournit TypeORM, des repositories personnalisés seront implémentés :

```typescript
@EntityRepository(Host)
export class HostRepository extends Repository<Host> {
  async findHostsWithListings(filters: any): Promise<Host[]> {
    // Requête personnalisée pour trouver des hébergeurs avec leurs annonces
  }
}

@EntityRepository(Cleaner)
export class CleanerRepository extends Repository<Cleaner> {
  async findCleanersWithinRadius(lat: number, lng: number, radius: number): Promise<Cleaner[]> {
    // Requête personnalisée pour trouver des professionnels dans un rayon donné
  }
}
```

## Fonctionnalités principales

### Création d'utilisateurs
Le module prend en charge deux flux de création différents :
1. **Hébergeurs** : Avec validation optionnelle du SIRET
2. **Professionnels** : Avec validation obligatoire du SIRET et des coordonnées bancaires

### Gestion de profil
Les utilisateurs peuvent mettre à jour leurs informations de profil via des endpoints dédiés :
- Mise à jour des informations personnelles
- Modification de l'adresse
- Mise à jour des préférences de travail (professionnels)
- Modification des informations bancaires (professionnels)

### Validation et vérification
Le module implémente plusieurs mécanismes de validation :
- Vérification d'email (flag `isEmailVerified`)
- Vérification d'identité (flag `isIdentityVerified`)
- Validation du SIRET via une API externe
- Validation des coordonnées bancaires (IBAN/BIC)

### Chiffrement des données sensibles
Les données bancaires sont chiffrées avant stockage et déchiffrées lors de la récupération :
- L'IBAN et le BIC sont stockés sous forme chiffrée dans la base de données
- Une clé de chiffrement distincte est utilisée pour sécuriser ces données

## API Endpoints

| Méthode | Endpoint                    | Description                                    | Auth requise | Rôles autorisés   |
|---------|-----------------------------|-------------------------------------------------|-------------|-------------------|
| GET     | /users/:id                  | Récupération d'un utilisateur par ID            | Oui         | Utilisateur ou Admin |
| GET     | /users                      | Liste de tous les utilisateurs                  | Oui         | Admin             |
| GET     | /users/hosts                | Liste de tous les hébergeurs                    | Oui         | Admin             |
| GET     | /users/cleaners             | Liste de tous les professionnels                | Oui         | Admin             |
| PATCH   | /users/hosts/:id            | Mise à jour d'un profil hébergeur               | Oui         | Utilisateur ou Admin |
| PATCH   | /users/cleaners/:id         | Mise à jour d'un profil professionnel           | Oui         | Utilisateur ou Admin |
| DELETE  | /users/:id                  | Suppression d'un utilisateur                    | Oui         | Utilisateur ou Admin |

*Note: La création d'utilisateurs est gérée par le module Auth via les endpoints d'inscription.*

## Sécurité et RGPD

### Chiffrement des données sensibles
```typescript
// Utilisation d'une librairie de chiffrement sécurisée
import { EncryptionService } from '../common/encryption/encryption.service';

// Dans le service
async encryptIban(iban: string): Promise<string> {
  return this.encryptionService.encrypt(iban);
}

async decryptIban(encryptedIban: string): Promise<string> {
  return this.encryptionService.decrypt(encryptedIban);
}
```

### Conformité RGPD
- **Droit à l'oubli** : Implémentation d'une suppression complète des données utilisateur
- **Droit d'accès** : Endpoints permettant aux utilisateurs d'accéder à toutes leurs données
- **Limitation de la collecte** : Collecte uniquement des données nécessaires au service
- **Sécurité des données** : Chiffrement des données sensibles et authentification forte

### Journalisation des accès
```typescript
// Intercepteur pour journaliser les accès aux données sensibles
@Injectable()
export class DataAccessLoggerInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const { user, method, path } = request;
    
    // Journaliser l'accès
    console.log(`User ${user.id} accessed ${method} ${path} at ${new Date()}`);
    
    return next.handle();
  }
}
```

## Tests

### Tests unitaires
Tests pour chaque composant isolé :
- users.service.spec.ts
- repositories/host.repository.spec.ts
- repositories/cleaner.repository.spec.ts

### Tests d'intégration
- users.controller.spec.ts : Tests des endpoints avec mocks des services
- users.e2e-spec.ts : Tests des flux complets de manipulation des utilisateurs

## Intégration avec autres modules

### Module Auth
Le module Users est étroitement lié au module Auth :
- L'Auth utilise les services Users pour créer des utilisateurs lors de l'inscription
- L'Auth stocke les refresh tokens dans l'entité User
- Les guards d'authentification et d'autorisation accèdent aux données utilisateur

### Module Listings
Les hébergeurs ont une relation avec les annonces qu'ils publient.

### Module Bookings
Les professionnels ont une relation avec les réservations qu'ils acceptent.

### Module Ratings
Les deux types d'utilisateurs sont concernés par les notations et avis.

---

## Points d'attention pour l'implémentation

1. **Sécurité des données sensibles** : Implémenter un chiffrement robuste pour les coordonnées bancaires
2. **Validation des identités professionnelles** : Intégrer une API de vérification de SIRET
3. **Performance** : Optimiser les requêtes pour les listes d'utilisateurs avec filtrage
4. **Évolutivité** : Concevoir les entités pour faciliter l'ajout de nouveaux attributs sans casser la compatibilité
5. **Tests rigoureux** : Vérifier en particulier les flux de mise à jour des données sensibles
6. **Conformité RGPD** : S'assurer que tous les traitements respectent les principes de protection des données

Ce document servira de guide pour l'implémentation du module Users et pourra être enrichi au fur et à mesure du développement.