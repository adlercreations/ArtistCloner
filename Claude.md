# ArtistCloner - AI Art Style Training & Generation Platform

## Project Overview

ArtistCloner is a full-stack application that enables artists to train AI models on their unique art style and generate new content using that style. Artists upload 20-40 images of their work, and the platform trains a custom AI model that can then create new images, videos, and other media that match their artistic style.

## Tech Stack

### Frontend
- **Framework**: React 19 with Vite 7
- **Styling**: Tailwind CSS with custom design system
- **State Management**: Zustand for global state
- **Routing**: React Router v7
- **UI Components**: Radix UI primitives + custom components
- **Forms**: React Hook Form + Zod validation
- **File Upload**: react-dropzone for drag-and-drop uploads
- **Animations**: Framer Motion

### Backend
- **Runtime**: Node.js with Express.js
- **API**: RESTful API + WebSocket for real-time training progress
- **Authentication**: JWT + OAuth 2.0 (Google, Discord)
- **File Storage**: AWS S3 or Cloudflare R2
- **Job Queue**: Bull (Redis-based) for training job management

### Database
- **Primary**: PostgreSQL for relational data
- **ORM**: Prisma
- **Caching**: Redis for sessions and job queues

### AI/ML Services
- **Image Generation**: Stable Diffusion API (via Replicate or self-hosted)
- **Model Training**: LoRA/DreamBooth fine-tuning
- **Future**: Integration points for text-to-video and text-to-3D services

---

## Database Schema

```prisma
// schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id            String    @id @default(cuid())
  email         String    @unique
  name          String?
  avatarUrl     String?
  password      String?   // null for OAuth users
  provider      String?   // google, discord, local
  providerId    String?

  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt

  artStyles     ArtStyle[]
  generations   Generation[]
  subscription  Subscription?
}

model ArtStyle {
  id            String    @id @default(cuid())
  name          String
  description   String?

  userId        String
  user          User      @relation(fields: [userId], references: [id], onDelete: Cascade)

  images        StyleImage[]
  trainedModel  TrainedModel?
  generations   Generation[]

  status        StyleStatus @default(DRAFT)

  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum StyleStatus {
  DRAFT           // Still uploading images
  READY_TO_TRAIN  // Has 20-40 images, ready for training
  TRAINING        // Model is being trained
  TRAINED         // Model ready for generation
  FAILED          // Training failed
}

model StyleImage {
  id            String    @id @default(cuid())

  artStyleId    String
  artStyle      ArtStyle  @relation(fields: [artStyleId], references: [id], onDelete: Cascade)

  filename      String
  originalUrl   String    // Original uploaded image
  processedUrl  String?   // Processed/resized for training
  thumbnailUrl  String?

  width         Int?
  height        Int?
  sizeBytes     Int?
  mimeType      String?

  caption       String?   // Optional description for training

  createdAt     DateTime  @default(now())
}

model TrainedModel {
  id            String    @id @default(cuid())

  artStyleId    String    @unique
  artStyle      ArtStyle  @relation(fields: [artStyleId], references: [id], onDelete: Cascade)

  modelUrl      String?   // URL to trained model weights
  modelType     ModelType @default(LORA)

  trainingSteps Int?
  trainingLoss  Float?

  startedAt     DateTime?
  completedAt   DateTime?

  status        TrainingStatus @default(PENDING)
  errorMessage  String?

  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum ModelType {
  LORA
  DREAMBOOTH
  TEXTUAL_INVERSION
}

enum TrainingStatus {
  PENDING
  QUEUED
  PROCESSING
  COMPLETED
  FAILED
}

model Generation {
  id            String    @id @default(cuid())

  userId        String
  user          User      @relation(fields: [userId], references: [id], onDelete: Cascade)

  artStyleId    String
  artStyle      ArtStyle  @relation(fields: [artStyleId], references: [id], onDelete: Cascade)

  prompt        String
  negativePrompt String?

  outputType    OutputType @default(IMAGE)
  outputUrl     String?
  thumbnailUrl  String?

  // Generation parameters
  width         Int       @default(512)
  height        Int       @default(512)
  steps         Int       @default(30)
  guidance      Float     @default(7.5)
  seed          Int?

  status        GenerationStatus @default(PENDING)
  errorMessage  String?

  createdAt     DateTime  @default(now())
  completedAt   DateTime?
}

enum OutputType {
  IMAGE
  VIDEO         // Future: text-to-video
  MODEL_3D      // Future: text-to-3D
}

enum GenerationStatus {
  PENDING
  PROCESSING
  COMPLETED
  FAILED
}

model Subscription {
  id            String    @id @default(cuid())

  userId        String    @unique
  user          User      @relation(fields: [userId], references: [id], onDelete: Cascade)

  plan          PlanType  @default(FREE)

  stripeCustomerId     String?
  stripeSubscriptionId String?

  trainingCredits      Int @default(1)  // Number of models that can be trained
  generationCredits    Int @default(50) // Number of generations per month

  currentPeriodStart   DateTime?
  currentPeriodEnd     DateTime?

  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum PlanType {
  FREE
  BASIC
  PRO
  ENTERPRISE
}
```

---

## Project Structure

```
ArtistCloner/
├── Claude.md
├── README.md
├── package.json
├── vite.config.js
│
├── public/
│   └── assets/
│
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   │
│   ├── components/
│   │   ├── ui/                    # Reusable UI components
│   │   │   ├── Button.jsx
│   │   │   ├── Card.jsx
│   │   │   ├── Input.jsx
│   │   │   ├── Modal.jsx
│   │   │   ├── Dropdown.jsx
│   │   │   ├── Progress.jsx
│   │   │   ├── Toast.jsx
│   │   │   └── index.js
│   │   │
│   │   ├── layout/                # Layout components
│   │   │   ├── Header.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   ├── Footer.jsx
│   │   │   └── DashboardLayout.jsx
│   │   │
│   │   ├── upload/                # Image upload components
│   │   │   ├── ImageDropzone.jsx
│   │   │   ├── ImagePreview.jsx
│   │   │   ├── ImageGrid.jsx
│   │   │   └── UploadProgress.jsx
│   │   │
│   │   ├── training/              # Model training components
│   │   │   ├── TrainingStatus.jsx
│   │   │   ├── TrainingProgress.jsx
│   │   │   └── TrainingConfig.jsx
│   │   │
│   │   ├── generation/            # Content generation components
│   │   │   ├── PromptInput.jsx
│   │   │   ├── GenerationSettings.jsx
│   │   │   ├── GenerationPreview.jsx
│   │   │   ├── GenerationHistory.jsx
│   │   │   └── OutputTypeSelector.jsx
│   │   │
│   │   ├── gallery/               # Gallery/portfolio components
│   │   │   ├── GalleryGrid.jsx
│   │   │   ├── GalleryItem.jsx
│   │   │   └── GalleryFilters.jsx
│   │   │
│   │   └── auth/                  # Authentication components
│   │       ├── LoginForm.jsx
│   │       ├── RegisterForm.jsx
│   │       ├── OAuthButtons.jsx
│   │       └── ProtectedRoute.jsx
│   │
│   ├── pages/
│   │   ├── Home.jsx               # Landing page
│   │   ├── Dashboard.jsx          # User dashboard
│   │   ├── Upload.jsx             # Image upload page
│   │   ├── Training.jsx           # Training management page
│   │   ├── Generate.jsx           # Content generation page
│   │   ├── Gallery.jsx            # User's generated content
│   │   ├── StyleDetail.jsx        # Individual art style view
│   │   ├── Settings.jsx           # User settings
│   │   ├── Pricing.jsx            # Subscription plans
│   │   ├── Login.jsx
│   │   └── Register.jsx
│   │
│   ├── hooks/
│   │   ├── useAuth.js
│   │   ├── useUpload.js
│   │   ├── useTraining.js
│   │   ├── useGeneration.js
│   │   ├── useArtStyles.js
│   │   └── useWebSocket.js
│   │
│   ├── stores/                    # Zustand stores
│   │   ├── authStore.js
│   │   ├── uploadStore.js
│   │   ├── styleStore.js
│   │   └── generationStore.js
│   │
│   ├── services/                  # API services
│   │   ├── api.js                 # Axios instance
│   │   ├── authService.js
│   │   ├── uploadService.js
│   │   ├── styleService.js
│   │   ├── trainingService.js
│   │   └── generationService.js
│   │
│   ├── utils/
│   │   ├── constants.js
│   │   ├── helpers.js
│   │   ├── validators.js
│   │   └── imageProcessing.js
│   │
│   └── styles/
│       ├── globals.css
│       └── tailwind.css
│
├── server/                        # Backend (can be separate repo)
│   ├── package.json
│   ├── index.js
│   │
│   ├── routes/
│   │   ├── auth.js
│   │   ├── users.js
│   │   ├── styles.js
│   │   ├── upload.js
│   │   ├── training.js
│   │   ├── generation.js
│   │   └── webhooks.js
│   │
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── styleController.js
│   │   ├── uploadController.js
│   │   ├── trainingController.js
│   │   └── generationController.js
│   │
│   ├── services/
│   │   ├── storageService.js      # S3/R2 file operations
│   │   ├── trainingService.js     # AI model training
│   │   ├── generationService.js   # Content generation
│   │   └── queueService.js        # Job queue management
│   │
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── rateLimit.js
│   │   ├── upload.js
│   │   └── errorHandler.js
│   │
│   ├── jobs/
│   │   ├── trainingJob.js
│   │   └── generationJob.js
│   │
│   ├── prisma/
│   │   ├── schema.prisma
│   │   └── migrations/
│   │
│   └── utils/
│       ├── logger.js
│       └── helpers.js
│
└── tests/
    ├── unit/
    └── integration/
```

---

## Core Features

### 1. User Authentication
- Email/password registration and login
- OAuth integration (Google, Discord)
- JWT-based session management
- Password reset flow

### 2. Art Style Management
- Create named art styles/portfolios
- Upload 20-40 reference images per style
- Drag-and-drop image upload with preview
- Image validation (format, size, dimensions)
- Automatic image preprocessing for training
- Optional captions for training images

### 3. Model Training
- LoRA fine-tuning on Stable Diffusion
- Real-time training progress via WebSocket
- Training configuration options (steps, learning rate)
- Training status notifications
- Model versioning and management

### 4. Content Generation
- Text-to-image generation using trained style
- Prompt input with suggestions
- Negative prompt support
- Generation parameters (size, steps, guidance, seed)
- Batch generation support
- Generation history and favorites

### 5. Gallery & Portfolio
- View all generated content
- Filter by art style, date, output type
- Download generated content
- Share functionality
- Public portfolio option

### 6. Subscription & Credits
- Free tier with limited generations
- Paid tiers for more training/generation credits
- Stripe integration for payments
- Usage tracking and limits

---

## API Endpoints

### Authentication
```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
POST   /api/auth/refresh
POST   /api/auth/forgot-password
POST   /api/auth/reset-password
GET    /api/auth/me
POST   /api/auth/oauth/google
POST   /api/auth/oauth/discord
```

### Art Styles
```
GET    /api/styles                 # List user's art styles
POST   /api/styles                 # Create new art style
GET    /api/styles/:id             # Get art style details
PUT    /api/styles/:id             # Update art style
DELETE /api/styles/:id             # Delete art style
```

### Image Upload
```
POST   /api/styles/:id/images      # Upload images to style
GET    /api/styles/:id/images      # List style images
DELETE /api/styles/:id/images/:imageId  # Remove image
POST   /api/upload/presigned       # Get presigned upload URL
```

### Training
```
POST   /api/styles/:id/train       # Start training
GET    /api/styles/:id/training    # Get training status
DELETE /api/styles/:id/training    # Cancel training
```

### Generation
```
POST   /api/generate               # Create generation
GET    /api/generations            # List user's generations
GET    /api/generations/:id        # Get generation details
DELETE /api/generations/:id        # Delete generation
POST   /api/generations/:id/retry  # Retry failed generation
```

### User
```
GET    /api/users/profile
PUT    /api/users/profile
GET    /api/users/subscription
POST   /api/users/subscription
```

---

## Environment Variables

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/artistcloner

# Redis
REDIS_URL=redis://localhost:6379

# JWT
JWT_SECRET=your-super-secret-key
JWT_EXPIRY=7d

# OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
DISCORD_CLIENT_ID=
DISCORD_CLIENT_SECRET=

# File Storage (S3/R2)
STORAGE_ENDPOINT=
STORAGE_ACCESS_KEY=
STORAGE_SECRET_KEY=
STORAGE_BUCKET=artistcloner-uploads
STORAGE_PUBLIC_URL=

# AI Services
REPLICATE_API_TOKEN=
# Or self-hosted SD
SD_API_URL=

# Stripe
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
STRIPE_PRICE_BASIC=
STRIPE_PRICE_PRO=

# App
APP_URL=http://localhost:5173
API_URL=http://localhost:3000
NODE_ENV=development
```

---

## Future Extensibility

### Text-to-Video (Planned)
- Integration with video generation models (Runway, Pika, Sora API)
- Frame-by-frame style application
- Video length and format options
- Video generation queue with progress tracking

### Text-to-3D (Planned)
- Integration with 3D generation models (Point-E, Shap-E, Meshy)
- Multiple output formats (OBJ, GLB, FBX)
- 3D viewer integration in gallery
- Texture application using trained style

### Additional Future Features
- Style mixing (combine multiple trained styles)
- Inpainting with style preservation
- Outpainting/image extension
- Real-time collaborative generation
- API access for developers
- Mobile app
- Browser extension for quick generation
- Community marketplace for styles

---

## UI/UX Guidelines

### Design Principles
- Clean, modern interface with focus on artwork
- Dark mode by default (light mode option)
- Generous whitespace for visual breathing room
- Large image previews and galleries
- Smooth animations and transitions
- Mobile-responsive design

### Color Palette
```css
:root {
  /* Dark theme (default) */
  --bg-primary: #0a0a0b;
  --bg-secondary: #141416;
  --bg-tertiary: #1c1c1f;
  --text-primary: #ffffff;
  --text-secondary: #a1a1aa;
  --accent-primary: #8b5cf6;    /* Purple */
  --accent-secondary: #06b6d4;  /* Cyan */
  --success: #10b981;
  --warning: #f59e0b;
  --error: #ef4444;
}
```

### Typography
- Headings: Inter or Space Grotesk
- Body: Inter
- Monospace: JetBrains Mono (for prompts/code)

---

## Development Guidelines

### Code Style
- Use ES modules
- Functional components with hooks
- Descriptive variable and function names
- JSDoc comments for complex functions
- Keep components small and focused

### Git Workflow
- Feature branches from `main`
- Conventional commits (feat:, fix:, docs:, etc.)
- PR reviews before merging
- Squash merge to main

### Testing
- Unit tests for utilities and hooks
- Integration tests for API endpoints
- E2E tests for critical user flows

---

## Getting Started

```bash
# Install dependencies
npm install

# Set up environment variables
cp .env.example .env

# Start development server
npm run dev

# In separate terminal, start backend
cd server && npm install && npm run dev
```

---

## Key Implementation Notes

1. **Image Upload Flow**: Use presigned URLs for direct-to-S3 uploads to avoid server bottlenecks
2. **Training Queue**: Use Bull/Redis to manage training jobs with proper retries and timeouts
3. **WebSocket**: Implement real-time updates for training progress and generation status
4. **Rate Limiting**: Apply per-user rate limits on generation endpoints
5. **Image Validation**: Validate on both client and server (format, size, dimensions)
6. **Error Handling**: Comprehensive error handling with user-friendly messages
7. **Caching**: Cache user data and frequently accessed resources
8. **Security**: Sanitize inputs, validate file types, use parameterized queries
