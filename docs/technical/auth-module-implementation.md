# Spécifications d'implémentation du module Auth

## Table des matières
1. [Vue d'ensemble](#vue-densemble)
2. [Objectifs](#objectifs)
3. [Architecture du module](#architecture-du-module)
4. [Dépendances](#dépendances)
5. [Modèles de données](#modèles-de-données)
6. [Composants principaux](#composants-principaux)
   - [DTOs](#dtos)
   - [Service d'authentification](#service-dauthentification)
   - [Contrôleur](#contrôleur)
   - [Stratégies d'authentification](#stratégies-dauthentification)
   - [Guards](#guards)
7. [Flux d'authentification](#flux-dauthentification)
8. [API Endpoints](#api-endpoints)
9. [Sécurité](#sécurité)
10. [Tests](#tests)
11. [Intégration avec autres modules](#intégration-avec-autres-modules)

## Vue d'ensemble

Le module Auth est responsable de l'authentification et de l'autorisation des utilisateurs dans l'application Menage Connect. Il gère l'inscription, la connexion, la génération et la validation des tokens JWT, ainsi que la protection des routes nécessitant une authentification.

Ce module est conçu pour prendre en charge deux types d'utilisateurs avec des processus d'inscription distincts :
- Les hébergeurs (propriétaires d'hébergements)
- Les professionnels de ménage

## Objectifs

1. Fournir un système d'inscription sécurisé avec validation des données
2. Implémenter l'authentification JWT avec tokens d'accès et de rafraîchissement
3. Protéger les routes de l'API nécessitant une authentification
4. Gérer les autorisations basées sur les rôles (hébergeur vs professionnel)
5. Intégrer des mécanismes de vérification (email et identité)
6. Assurer la sécurité des données sensibles

## Architecture du module

```
modules/auth/
├── auth.module.ts             # Définition du module et dépendances
├── auth.controller.ts         # Points d'entrée de l'API
├── auth.service.ts            # Logique métier d'authentification
├── dto/                       # Objets de transfert de données
│   ├── login.dto.ts           # DTO pour la connexion
│   ├── register-host.dto.ts   # DTO pour l'inscription des hébergeurs
│   ├── register-cleaner.dto.ts # DTO pour l'inscription des professionnels
│   ├── reset-password.dto.ts  # DTO pour réinitialisation de mot de passe
│   └── auth-response.dto.ts   # DTO pour les réponses d'authentification
├── strategies/                # Stratégies Passport
│   ├── jwt.strategy.ts        # Stratégie JWT
│   ├── local.strategy.ts      # Stratégie d'authentification locale
│   └── refresh-token.strategy.ts # Stratégie pour refresh token
├── guards/                    # Guards pour la protection des routes
│   ├── jwt-auth.guard.ts      # Guard pour authentification JWT
│   ├── local-auth.guard.ts    # Guard pour authentification locale
│   ├── roles.guard.ts         # Guard pour vérification des rôles
│   └── refresh-token.guard.ts # Guard pour refresh token
└── tests/                     # Tests unitaires et d'intégration
    ├── auth.controller.spec.ts
    └── auth.service.spec.ts
```

## Dépendances

Le module Auth dépend des packages suivants :

```typescript
// Dépendances NestJS principales
import { Module } from '@nestjs/common';
import { ConfigModule, ConfigType } from '@nestjs/config';

// JWT et Passport pour l'authentification
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';

// Configuration JWT personnalisée
import jwtConfig from '../../config/jwt.config';

// Module des utilisateurs (pour la gestion des utilisateurs)
import { UsersModule } from '../users/users.module';

// Utilitaires de cryptographie
import * as bcrypt from 'bcrypt';
```

## Modèles de données

### Types d'utilisateurs
Le système prend en charge deux types d'utilisateurs distincts :

1. **Hébergeurs** : Propriétaires de logements proposant des missions de ménage
2. **Professionnels de ménage** : Prestataires de services cherchant des missions

### Jetons d'authentification
Deux types de jetons sont utilisés :

1. **Access Token** : JWT de courte durée (1 jour) pour l'accès aux ressources
2. **Refresh Token** : JWT de longue durée (7 jours) pour obtenir de nouveaux access tokens

## Composants principaux

### DTOs

#### LoginDto
```typescript
export class LoginDto {
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsString()
  @IsNotEmpty()
  @MinLength(8)
  password: string;
}
```

#### RegisterHostDto
```typescript
export class RegisterHostDto {
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsString()
  @IsNotEmpty()
  @MinLength(8)
  password: string;

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

  // Termes et conditions
  @IsBoolean()
  @IsTrue()
  acceptTerms: boolean;
}
```

#### RegisterCleanerDto
```typescript
export class RegisterCleanerDto {
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsString()
  @IsNotEmpty()
  @MinLength(8)
  password: string;

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

  // Informations bancaires (à chiffrer)
  @IsString()
  @IsNotEmpty()
  iban: string;

  @IsString()
  @IsNotEmpty()
  bic: string;

  // Préférences de travail
  @IsNumber()
  @Min(1)
  @Max(100)
  workRadius: number; // Rayon de travail en km

  // Termes et conditions
  @IsBoolean()
  @IsTrue()
  acceptTerms: boolean;
}
```

#### AuthResponseDto
```typescript
export class AuthResponseDto {
  @IsString()
  accessToken: string;

  @IsString()
  refreshToken: string;

  @IsString()
  tokenType: string = 'Bearer';

  @IsNumber()
  expiresIn: number;

  @IsObject()
  user: {
    id: string;
    email: string;
    firstName: string;
    lastName: string;
    role: string;
  };
}
```

### Service d'authentification

Le service d'authentification (`auth.service.ts`) implémentera les méthodes suivantes :

```typescript
// Méthodes publiques principales
register(registerDto: RegisterHostDto | RegisterCleanerDto): Promise<AuthResponseDto>
login(loginDto: LoginDto): Promise<AuthResponseDto>
refreshToken(userId: string, refreshToken: string): Promise<AuthResponseDto>
validateUser(email: string, password: string): Promise<any>
verifyEmail(token: string): Promise<void>
resetPasswordRequest(email: string): Promise<void>
resetPassword(resetPasswordDto: ResetPasswordDto): Promise<void>

// Méthodes privées et utilitaires
private hashPassword(password: string): Promise<string>
private comparePasswords(password: string, hashedPassword: string): Promise<boolean>
private generateTokens(user: User): Promise<{ accessToken: string; refreshToken: string }>
private verifyRefreshToken(userId: string, refreshToken: string): Promise<boolean>
```

### Contrôleur

Le contrôleur (`auth.controller.ts`) exposera les endpoints suivants :

```typescript
@Post('register/host')
registerHost(@Body() registerHostDto: RegisterHostDto): Promise<AuthResponseDto>

@Post('register/cleaner')
registerCleaner(@Body() registerCleanerDto: RegisterCleanerDto): Promise<AuthResponseDto>

@Post('login')
login(@Body() loginDto: LoginDto): Promise<AuthResponseDto>

@Post('refresh-token')
@UseGuards(RefreshTokenGuard)
refreshToken(@Request() req): Promise<AuthResponseDto>

@Get('verify-email/:token')
verifyEmail(@Param('token') token: string): Promise<{ message: string }>

@Post('reset-password-request')
resetPasswordRequest(@Body() { email }: { email: string }): Promise<{ message: string }>

@Post('reset-password')
resetPassword(@Body() resetPasswordDto: ResetPasswordDto): Promise<{ message: string }>

@Get('profile')
@UseGuards(JwtAuthGuard)
getProfile(@Request() req): Promise<any>
```

### Stratégies d'authentification

#### JWT Strategy
```typescript
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(
    @Inject(jwtConfig.KEY)
    private readonly jwtConfiguration: ConfigType<typeof jwtConfig>,
  ) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: jwtConfiguration.secret,
    });
  }

  async validate(payload: any) {
    return { 
      userId: payload.sub, 
      email: payload.email,
      role: payload.role,
    };
  }
}
```

#### Local Strategy
```typescript
@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super({
      usernameField: 'email',
      passwordField: 'password',
    });
  }

  async validate(email: string, password: string): Promise<any> {
    const user = await this.authService.validateUser(email, password);
    if (!user) {
      throw new UnauthorizedException('Identifiants invalides');
    }
    return user;
  }
}
```

#### Refresh Token Strategy
```typescript
@Injectable()
export class RefreshTokenStrategy extends PassportStrategy(Strategy, 'refresh-token') {
  constructor(
    @Inject(jwtConfig.KEY)
    private readonly jwtConfiguration: ConfigType<typeof jwtConfig>,
    private readonly authService: AuthService,
  ) {
    super({
      jwtFromRequest: ExtractJwt.fromBodyField('refreshToken'),
      ignoreExpiration: false,
      secretOrKey: jwtConfiguration.refreshSecret,
      passReqToCallback: true,
    });
  }

  async validate(req: Request, payload: any) {
    const refreshToken = req.body.refreshToken;
    const isValid = await this.authService.verifyRefreshToken(payload.sub, refreshToken);
    
    if (!isValid) {
      throw new UnauthorizedException('Refresh token invalide');
    }

    return { 
      userId: payload.sub, 
      email: payload.email,
      role: payload.role,
      refreshToken,
    };
  }
}
```

### Guards

#### JWT Auth Guard
```typescript
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

#### Refresh Token Guard
```typescript
@Injectable()
export class RefreshTokenGuard extends AuthGuard('refresh-token') {}
```

#### Roles Guard
```typescript
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<string[]>('roles', [
      context.getHandler(),
      context.getClass(),
    ]);
    
    if (!requiredRoles) {
      return true;
    }
    
    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.includes(user.role);
  }
}
```

## Flux d'authentification

### Inscription
1. L'utilisateur remplit le formulaire d'inscription correspondant à son profil
2. L'API valide les données et vérifie l'unicité de l'email
3. Le mot de passe est haché avec bcrypt
4. Un nouvel utilisateur est créé dans la base de données
5. Un email de vérification est envoyé
6. Des tokens d'accès et de rafraîchissement sont générés et renvoyés

### Connexion
1. L'utilisateur soumet ses identifiants (email/mot de passe)
2. L'API valide les identifiants
3. Des tokens d'accès et de rafraîchissement sont générés et renvoyés

### Accès aux ressources protégées
1. Le client inclut le token d'accès dans l'en-tête Authorization
2. Le JwtAuthGuard valide le token
3. Le RolesGuard vérifie les autorisations si nécessaire
4. La ressource est renvoyée si l'utilisateur est autorisé

### Rafraîchissement du token
1. Le client envoie le refresh token
2. L'API valide le refresh token
3. De nouveaux tokens d'accès et de rafraîchissement sont générés et renvoyés

## API Endpoints

| Méthode | Endpoint                    | Description                                    | Auth requise | Rôles autorisés   |
|---------|-----------------------------|-------------------------------------------------|-------------|-------------------|
| POST    | /auth/register/host         | Inscription d'un hébergeur                      | Non         | -                 |
| POST    | /auth/register/cleaner      | Inscription d'un professionnel de ménage        | Non         | -                 |
| POST    | /auth/login                 | Connexion d'un utilisateur                      | Non         | -                 |
| POST    | /auth/refresh-token         | Rafraîchissement du token d'accès               | Oui         | Tous              |
| GET     | /auth/verify-email/:token   | Vérification de l'email                         | Non         | -                 |
| POST    | /auth/reset-password-request | Demande de réinitialisation du mot de passe    | Non         | -                 |
| POST    | /auth/reset-password        | Réinitialisation du mot de passe                | Non         | -                 |
| GET     | /auth/profile               | Récupération du profil de l'utilisateur connecté | Oui        | Tous              |

## Sécurité

### Hachage des mots de passe
Les mots de passe sont hachés avec bcrypt avant stockage :
```typescript
const saltRounds = 10;
const hashedPassword = await bcrypt.hash(password, saltRounds);
```

### Protection contre les attaques courantes
- **Limiter les tentatives** : Implémentation d'un rate limiting pour prévenir le brute force
- **Validation stricte** : Utilisation de class-validator pour valider toutes les entrées
- **Tokens JWT sécurisés** : Signatures vérifiées, expiration courte pour les access tokens

### Stockage des tokens
- Les refresh tokens sont stockés de manière sécurisée dans la base de données
- Les tokens sont invalidés lors de la déconnexion ou du changement de mot de passe

## Tests

### Tests unitaires
Tests pour chaque composant isolé :
- auth.service.spec.ts
- strategies/jwt.strategy.spec.ts
- guards/roles.guard.spec.ts

### Tests d'intégration
- auth.controller.spec.ts : Tests des endpoints avec mocks des services
- auth.e2e-spec.ts : Tests des flux complets d'authentification

## Intégration avec autres modules

### Module Users
Le module Auth est étroitement lié au module Users :
- Dépendance pour la création et la vérification des utilisateurs
- Partage des entités et des repositories

### Module Notifications
Pour les emails de vérification et de réinitialisation de mot de passe.

### Autres modules
Tous les modules nécessitant une authentification utiliseront les guards fournis par ce module.

---

## Points d'attention pour l'implémentation

1. **Sécurité renforcée** : Porter une attention particulière à la sécurisation des données sensibles
2. **Validation robuste** : S'assurer que toutes les entrées sont strictement validées
3. **Tests exhaustifs** : Couvrir tous les scénarios, y compris les cas d'erreur
4. **Documentation API** : Documenter clairement tous les endpoints avec Swagger
5. **Gestion des erreurs** : Implémenter une gestion d'erreurs cohérente et informative

Ce document servira de guide pour l'implémentation du module Auth et pourra être enrichi au fur et à mesure du développement.