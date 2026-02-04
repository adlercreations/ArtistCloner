# ArtistCloner - AI Art Style Training & Generation Platform

## Project Overview

ArtistCloner is a full-stack application that enables artists to train AI models on their unique art style and generate new content using that style. Artists upload 20-40 images of their work, and the platform trains a custom AI model that can then create new images, videos, and other media that match their artistic style.

---

## Implementation Phases

### Phase 1: Foundation (Build First)
1. Set up project structure and dependencies
2. Configure Tailwind CSS with custom theme
3. Create base UI components (Button, Card, Input, Modal)
4. Set up React Router with page structure
5. Create layout components (Header, Sidebar, DashboardLayout)
6. Set up Zustand stores for state management

### Phase 2: Authentication
1. Create Login and Register pages
2. Implement AuthService with JWT handling
3. Set up ProtectedRoute component
4. Create auth Zustand store
5. Build backend auth routes and controllers

### Phase 3: Image Upload & Style Management
1. Build ImageDropzone component with react-dropzone
2. Create image validation utilities
3. Implement presigned URL upload flow
4. Build StyleImage grid and preview components
5. Create art style CRUD operations

### Phase 4: Model Training
1. Integrate Replicate API for LoRA training
2. Build training status and progress components
3. Implement WebSocket for real-time updates
4. Create training job queue with Bull

### Phase 5: Content Generation
1. Build prompt input with generation settings
2. Integrate Replicate for image generation
3. Create generation preview and history
4. Implement generation queue and status tracking

### Phase 6: Gallery & Polish
1. Build gallery grid with filters
2. Add download and share functionality
3. Implement responsive design
4. Add animations with Framer Motion

### Phase 7: Future Features (Extensibility)
1. Text-to-video integration
2. Text-to-3D integration
3. Style mixing capabilities

---

## Package Dependencies

### Frontend (package.json)
```json
{
  "name": "artistcloner",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint ."
  },
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "react-router-dom": "^7.1.0",
    "zustand": "^5.0.0",
    "axios": "^1.7.0",
    "react-hook-form": "^7.54.0",
    "@hookform/resolvers": "^3.9.0",
    "zod": "^3.24.0",
    "react-dropzone": "^14.3.0",
    "framer-motion": "^11.15.0",
    "@radix-ui/react-dialog": "^1.1.0",
    "@radix-ui/react-dropdown-menu": "^2.1.0",
    "@radix-ui/react-toast": "^1.2.0",
    "@radix-ui/react-progress": "^1.1.0",
    "@radix-ui/react-tabs": "^1.1.0",
    "@radix-ui/react-slider": "^1.2.0",
    "@radix-ui/react-switch": "^1.1.0",
    "clsx": "^2.1.0",
    "tailwind-merge": "^2.6.0",
    "lucide-react": "^0.469.0",
    "date-fns": "^4.1.0",
    "socket.io-client": "^4.8.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.3.0",
    "vite": "^6.0.0",
    "tailwindcss": "^3.4.0",
    "postcss": "^8.4.0",
    "autoprefixer": "^10.4.0",
    "eslint": "^9.17.0",
    "@eslint/js": "^9.17.0",
    "globals": "^15.14.0",
    "eslint-plugin-react": "^7.37.0",
    "eslint-plugin-react-hooks": "^5.0.0"
  }
}
```

### Backend (server/package.json)
```json
{
  "name": "artistcloner-server",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "nodemon index.js",
    "start": "node index.js",
    "db:push": "prisma db push",
    "db:migrate": "prisma migrate dev",
    "db:generate": "prisma generate"
  },
  "dependencies": {
    "express": "^4.21.0",
    "cors": "^2.8.5",
    "helmet": "^8.0.0",
    "morgan": "^1.10.0",
    "dotenv": "^16.4.0",
    "@prisma/client": "^6.2.0",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.0",
    "express-validator": "^7.2.0",
    "multer": "^1.4.5-lts.1",
    "@aws-sdk/client-s3": "^3.700.0",
    "@aws-sdk/s3-request-presigner": "^3.700.0",
    "bullmq": "^5.30.0",
    "ioredis": "^5.4.0",
    "socket.io": "^4.8.0",
    "replicate": "^1.0.0",
    "sharp": "^0.33.0",
    "uuid": "^11.0.0",
    "express-rate-limit": "^7.5.0"
  },
  "devDependencies": {
    "prisma": "^6.2.0",
    "nodemon": "^3.1.0"
  }
}
```

---

## Configuration Files

### vite.config.js
```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@pages': path.resolve(__dirname, './src/pages'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
      '@stores': path.resolve(__dirname, './src/stores'),
      '@services': path.resolve(__dirname, './src/services'),
      '@utils': path.resolve(__dirname, './src/utils'),
    },
  },
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
      },
      '/socket.io': {
        target: 'http://localhost:3000',
        ws: true,
      },
    },
  },
})
```

### tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        background: {
          primary: '#0a0a0b',
          secondary: '#141416',
          tertiary: '#1c1c1f',
        },
        foreground: {
          primary: '#ffffff',
          secondary: '#a1a1aa',
          muted: '#71717a',
        },
        accent: {
          primary: '#8b5cf6',
          'primary-hover': '#7c3aed',
          secondary: '#06b6d4',
          'secondary-hover': '#0891b2',
        },
        success: '#10b981',
        warning: '#f59e0b',
        error: '#ef4444',
        border: '#27272a',
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        heading: ['Space Grotesk', 'Inter', 'system-ui', 'sans-serif'],
        mono: ['JetBrains Mono', 'monospace'],
      },
      animation: {
        'fade-in': 'fadeIn 0.3s ease-out',
        'slide-up': 'slideUp 0.3s ease-out',
        'pulse-slow': 'pulse 3s infinite',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { opacity: '0', transform: 'translateY(10px)' },
          '100%': { opacity: '1', transform: 'translateY(0)' },
        },
      },
    },
  },
  plugins: [],
}
```

### postcss.config.js
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### src/styles/globals.css
```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Space+Grotesk:wght@500;600;700&family=JetBrains+Mono:wght@400;500&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  * {
    @apply border-border;
  }

  body {
    @apply bg-background-primary text-foreground-primary font-sans antialiased;
  }

  h1, h2, h3, h4, h5, h6 {
    @apply font-heading;
  }
}

@layer components {
  .btn-primary {
    @apply bg-accent-primary hover:bg-accent-primary-hover text-white font-medium py-2 px-4 rounded-lg transition-colors duration-200;
  }

  .btn-secondary {
    @apply bg-background-tertiary hover:bg-border text-foreground-primary font-medium py-2 px-4 rounded-lg transition-colors duration-200 border border-border;
  }

  .card {
    @apply bg-background-secondary border border-border rounded-xl p-6;
  }

  .input {
    @apply bg-background-tertiary border border-border rounded-lg px-4 py-2 text-foreground-primary placeholder:text-foreground-muted focus:outline-none focus:ring-2 focus:ring-accent-primary focus:border-transparent transition-all duration-200;
  }
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  @apply bg-background-secondary;
}

::-webkit-scrollbar-thumb {
  @apply bg-border rounded-full hover:bg-foreground-muted;
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
├── tailwind.config.js
├── postcss.config.js
├── index.html
│
├── public/
│   └── assets/
│       └── logo.svg
│
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   │
│   ├── components/
│   │   ├── ui/
│   │   │   ├── Button.jsx
│   │   │   ├── Card.jsx
│   │   │   ├── Input.jsx
│   │   │   ├── Modal.jsx
│   │   │   ├── Dropdown.jsx
│   │   │   ├── Progress.jsx
│   │   │   ├── Toast.jsx
│   │   │   ├── Spinner.jsx
│   │   │   ├── Badge.jsx
│   │   │   ├── Tabs.jsx
│   │   │   ├── Slider.jsx
│   │   │   └── index.js
│   │   │
│   │   ├── layout/
│   │   │   ├── Header.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   ├── Footer.jsx
│   │   │   └── DashboardLayout.jsx
│   │   │
│   │   ├── upload/
│   │   │   ├── ImageDropzone.jsx
│   │   │   ├── ImagePreview.jsx
│   │   │   ├── ImageGrid.jsx
│   │   │   └── UploadProgress.jsx
│   │   │
│   │   ├── training/
│   │   │   ├── TrainingStatus.jsx
│   │   │   ├── TrainingProgress.jsx
│   │   │   └── TrainingConfig.jsx
│   │   │
│   │   ├── generation/
│   │   │   ├── PromptInput.jsx
│   │   │   ├── GenerationSettings.jsx
│   │   │   ├── GenerationPreview.jsx
│   │   │   ├── GenerationHistory.jsx
│   │   │   └── OutputTypeSelector.jsx
│   │   │
│   │   ├── gallery/
│   │   │   ├── GalleryGrid.jsx
│   │   │   ├── GalleryItem.jsx
│   │   │   └── GalleryFilters.jsx
│   │   │
│   │   └── auth/
│   │       ├── LoginForm.jsx
│   │       ├── RegisterForm.jsx
│   │       ├── OAuthButtons.jsx
│   │       └── ProtectedRoute.jsx
│   │
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── Dashboard.jsx
│   │   ├── Upload.jsx
│   │   ├── Training.jsx
│   │   ├── Generate.jsx
│   │   ├── Gallery.jsx
│   │   ├── StyleDetail.jsx
│   │   ├── Settings.jsx
│   │   ├── Pricing.jsx
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
│   ├── stores/
│   │   ├── authStore.js
│   │   ├── uploadStore.js
│   │   ├── styleStore.js
│   │   └── generationStore.js
│   │
│   ├── services/
│   │   ├── api.js
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
│   │   └── cn.js
│   │
│   └── styles/
│       └── globals.css
│
├── server/
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
│   │   ├── storageService.js
│   │   ├── trainingService.js
│   │   ├── generationService.js
│   │   └── queueService.js
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
└── .env.example
```

---

## Component Specifications

### UI Components

#### Button.jsx
```jsx
// Props:
// - variant: 'primary' | 'secondary' | 'ghost' | 'danger' (default: 'primary')
// - size: 'sm' | 'md' | 'lg' (default: 'md')
// - disabled: boolean
// - loading: boolean - shows spinner when true
// - icon: ReactNode - optional icon to display
// - iconPosition: 'left' | 'right' (default: 'left')
// - fullWidth: boolean
// - children: ReactNode
// - onClick: function
// - type: 'button' | 'submit' | 'reset'

// Example usage:
<Button variant="primary" size="lg" loading={isSubmitting}>
  Create Style
</Button>
```

#### Card.jsx
```jsx
// Props:
// - variant: 'default' | 'interactive' | 'elevated' (default: 'default')
// - padding: 'none' | 'sm' | 'md' | 'lg' (default: 'md')
// - children: ReactNode
// - className: string
// - onClick: function (for interactive variant)

// Example usage:
<Card variant="interactive" onClick={handleClick}>
  <CardHeader>Title</CardHeader>
  <CardContent>Content here</CardContent>
</Card>
```

#### Input.jsx
```jsx
// Props:
// - label: string
// - error: string
// - helperText: string
// - icon: ReactNode (left icon)
// - type: 'text' | 'email' | 'password' | 'number'
// - placeholder: string
// - disabled: boolean
// - required: boolean
// - ...rest (standard input props)

// Example usage:
<Input
  label="Email"
  type="email"
  placeholder="Enter your email"
  error={errors.email?.message}
  {...register('email')}
/>
```

#### Modal.jsx
```jsx
// Props:
// - open: boolean
// - onClose: function
// - title: string
// - description: string
// - children: ReactNode
// - size: 'sm' | 'md' | 'lg' | 'xl' (default: 'md')
// - showClose: boolean (default: true)

// Example usage:
<Modal open={isOpen} onClose={handleClose} title="Confirm Delete">
  <p>Are you sure you want to delete this style?</p>
  <div className="flex gap-3 mt-4">
    <Button variant="secondary" onClick={handleClose}>Cancel</Button>
    <Button variant="danger" onClick={handleDelete}>Delete</Button>
  </div>
</Modal>
```

#### Progress.jsx
```jsx
// Props:
// - value: number (0-100)
// - max: number (default: 100)
// - size: 'sm' | 'md' | 'lg' (default: 'md')
// - showLabel: boolean
// - color: 'primary' | 'success' | 'warning' | 'error'

// Example usage:
<Progress value={75} showLabel color="primary" />
```

### Upload Components

#### ImageDropzone.jsx
```jsx
// Props:
// - onFilesAccepted: (files: File[]) => void
// - maxFiles: number (default: 40)
// - minFiles: number (default: 20)
// - maxSizeBytes: number (default: 10MB)
// - acceptedFormats: string[] (default: ['image/jpeg', 'image/png', 'image/webp'])
// - disabled: boolean
// - currentCount: number

// Features:
// - Drag and drop support
// - Click to browse
// - File validation with error messages
// - Shows accepted file count
// - Visual feedback on drag over

// Example usage:
<ImageDropzone
  onFilesAccepted={handleFilesAccepted}
  maxFiles={40}
  minFiles={20}
  currentCount={uploadedImages.length}
/>
```

#### ImageGrid.jsx
```jsx
// Props:
// - images: Array<{ id, url, filename, caption? }>
// - onRemove: (id: string) => void
// - onCaptionChange: (id: string, caption: string) => void
// - selectable: boolean
// - selectedIds: string[]
// - onSelect: (id: string) => void
// - columns: 2 | 3 | 4 | 5 (default: 4)

// Example usage:
<ImageGrid
  images={styleImages}
  onRemove={handleRemove}
  onCaptionChange={handleCaptionChange}
  columns={4}
/>
```

### Generation Components

#### PromptInput.jsx
```jsx
// Props:
// - value: string
// - onChange: (value: string) => void
// - onSubmit: () => void
// - disabled: boolean
// - placeholder: string
// - maxLength: number (default: 500)
// - showCharCount: boolean

// Features:
// - Multi-line textarea
// - Character counter
// - Submit with Ctrl+Enter
// - Prompt suggestions dropdown

// Example usage:
<PromptInput
  value={prompt}
  onChange={setPrompt}
  onSubmit={handleGenerate}
  placeholder="Describe the image you want to generate..."
/>
```

#### GenerationSettings.jsx
```jsx
// Props:
// - settings: { width, height, steps, guidance, seed, negativePrompt }
// - onChange: (settings) => void
// - presets: Array<{ name, settings }>

// Settings ranges:
// - width/height: 512, 768, 1024
// - steps: 20-50 (default: 30)
// - guidance: 1-20 (default: 7.5)
// - seed: number or 'random'

// Example usage:
<GenerationSettings
  settings={generationSettings}
  onChange={setGenerationSettings}
/>
```

---

## Page Layouts

### Home.jsx (Landing Page)
```
┌────────────────────────────────────────────────────────────┐
│ Header: Logo | Features | Pricing | Login | Get Started    │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Hero Section:                                             │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  "Train AI on Your Art Style"                        │ │
│  │  Subtitle: Upload your work, train a model...        │ │
│  │  [Get Started Free] [See Examples]                   │ │
│  │                                                       │ │
│  │  Hero Image: Grid of AI-generated art samples        │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  How It Works (3 steps):                                   │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐         │
│  │ 1. Upload   │ │ 2. Train    │ │ 3. Generate │         │
│  │ 20-40 imgs  │ │ AI learns   │ │ New artwork │         │
│  └─────────────┘ └─────────────┘ └─────────────┘         │
│                                                            │
│  Features Grid:                                            │
│  - Fast training | Multiple styles | High quality          │
│  - Video support (coming) | 3D models (coming)            │
│                                                            │
│  Pricing Section:                                          │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐                     │
│  │  Free   │ │  Basic  │ │   Pro   │                     │
│  │  $0/mo  │ │ $19/mo  │ │ $49/mo  │                     │
│  └─────────┘ └─────────┘ └─────────┘                     │
│                                                            │
├────────────────────────────────────────────────────────────┤
│ Footer: Links | Social | Copyright                         │
└────────────────────────────────────────────────────────────┘
```

### Dashboard.jsx
```
┌────────────────────────────────────────────────────────────┐
│ Header: Logo | Search | Notifications | Profile            │
├──────────┬─────────────────────────────────────────────────┤
│ Sidebar  │  Dashboard Content                              │
│          │                                                 │
│ Dashboard│  Welcome Back, {name}                          │
│ My Styles│                                                 │
│ Generate │  Stats Cards:                                   │
│ Gallery  │  ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│ Settings │  │ Styles  │ │ Trained │ │ Credits │          │
│          │  │    3    │ │    2    │ │   47    │          │
│          │  └─────────┘ └─────────┘ └─────────┘          │
│          │                                                 │
│          │  My Art Styles:                                │
│          │  ┌─────────────────────────────────────────┐   │
│          │  │ [+ Create New Style]                     │   │
│          │  │                                          │   │
│          │  │ ┌──────────┐ ┌──────────┐ ┌──────────┐  │   │
│          │  │ │ Style 1  │ │ Style 2  │ │ Style 3  │  │   │
│          │  │ │ Trained  │ │ Training │ │ Draft    │  │   │
│          │  │ │ 32 imgs  │ │ 28 imgs  │ │ 15 imgs  │  │   │
│          │  │ └──────────┘ └──────────┘ └──────────┘  │   │
│          │  └─────────────────────────────────────────┘   │
│          │                                                 │
│          │  Recent Generations:                           │
│          │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐         │
│          │  │ img  │ │ img  │ │ img  │ │ img  │         │
│          │  └──────┘ └──────┘ └──────┘ └──────┘         │
└──────────┴─────────────────────────────────────────────────┘
```

### Upload.jsx (Style Creation)
```
┌────────────────────────────────────────────────────────────┐
│ Header with breadcrumb: Dashboard > Create New Style       │
├──────────┬─────────────────────────────────────────────────┤
│ Sidebar  │                                                 │
│          │  Create New Art Style                          │
│          │                                                 │
│          │  ┌──────────────────────────────────────────┐  │
│          │  │ Style Name: [___________________]        │  │
│          │  │ Description: [___________________]       │  │
│          │  └──────────────────────────────────────────┘  │
│          │                                                 │
│          │  Upload Images (20-40 required):               │
│          │  ┌──────────────────────────────────────────┐  │
│          │  │                                          │  │
│          │  │    ┌─────────────────────────────┐      │  │
│          │  │    │  Drag & drop images here    │      │  │
│          │  │    │  or click to browse         │      │  │
│          │  │    │                             │      │  │
│          │  │    │  PNG, JPG, WebP up to 10MB  │      │  │
│          │  │    └─────────────────────────────┘      │  │
│          │  │                                          │  │
│          │  │  Uploaded: 24/40 images                  │  │
│          │  │  ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐    │  │
│          │  │  │ x  │ │ x  │ │ x  │ │ x  │ │ x  │    │  │
│          │  │  └────┘ └────┘ └────┘ └────┘ └────┘    │  │
│          │  │  (grid continues...)                    │  │
│          │  └──────────────────────────────────────────┘  │
│          │                                                 │
│          │  [Save as Draft]  [Start Training]             │
└──────────┴─────────────────────────────────────────────────┘
```

### Generate.jsx
```
┌────────────────────────────────────────────────────────────┐
│ Header                                                     │
├──────────┬─────────────────────────────────────────────────┤
│ Sidebar  │                                                 │
│          │  Generate New Artwork                          │
│          │                                                 │
│          │  Select Style:                                 │
│          │  ┌──────────────────────────────────────────┐  │
│          │  │ [Dropdown: My Watercolor Style     v]   │  │
│          │  └──────────────────────────────────────────┘  │
│          │                                                 │
│          │  ┌─────────────────────┬────────────────────┐  │
│          │  │ Prompt Input        │ Settings Panel     │  │
│          │  │                     │                    │  │
│          │  │ [                 ] │ Size: 512x512  v   │  │
│          │  │ [                 ] │ Steps: [30]        │  │
│          │  │ [                 ] │ Guidance: [7.5]    │  │
│          │  │                     │ Seed: [random]     │  │
│          │  │ Negative prompt:    │                    │  │
│          │  │ [                 ] │ [Advanced v]       │  │
│          │  │                     │                    │  │
│          │  │ [Generate]          │                    │  │
│          │  └─────────────────────┴────────────────────┘  │
│          │                                                 │
│          │  Preview / Result:                             │
│          │  ┌──────────────────────────────────────────┐  │
│          │  │                                          │  │
│          │  │         [Generated Image]                │  │
│          │  │                                          │  │
│          │  │  [Download] [Save to Gallery] [Regenerate]│ │
│          │  └──────────────────────────────────────────┘  │
└──────────┴─────────────────────────────────────────────────┘
```

---

## Database Schema

```prisma
// server/prisma/schema.prisma

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
  password      String?
  provider      String?
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

  @@index([userId])
}

enum StyleStatus {
  DRAFT
  READY_TO_TRAIN
  TRAINING
  TRAINED
  FAILED
}

model StyleImage {
  id            String    @id @default(cuid())
  artStyleId    String
  artStyle      ArtStyle  @relation(fields: [artStyleId], references: [id], onDelete: Cascade)

  filename      String
  originalUrl   String
  processedUrl  String?
  thumbnailUrl  String?

  width         Int?
  height        Int?
  sizeBytes     Int?
  mimeType      String?
  caption       String?

  createdAt     DateTime  @default(now())

  @@index([artStyleId])
}

model TrainedModel {
  id            String    @id @default(cuid())
  artStyleId    String    @unique
  artStyle      ArtStyle  @relation(fields: [artStyleId], references: [id], onDelete: Cascade)

  modelUrl      String?
  modelVersion  String?
  modelType     ModelType @default(LORA)

  trainingSteps Int?
  trainingLoss  Float?

  replicateTrainingId String?

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
  CANCELED
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

  width         Int       @default(512)
  height        Int       @default(512)
  steps         Int       @default(30)
  guidance      Float     @default(7.5)
  seed          Int?

  replicatePredictionId String?

  status        GenerationStatus @default(PENDING)
  errorMessage  String?

  createdAt     DateTime  @default(now())
  completedAt   DateTime?

  @@index([userId])
  @@index([artStyleId])
}

enum OutputType {
  IMAGE
  VIDEO
  MODEL_3D
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

  trainingCredits      Int @default(1)
  generationCredits    Int @default(50)

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

## API Specifications

### Authentication

#### POST /api/auth/register
```json
// Request
{
  "email": "artist@example.com",
  "password": "securePassword123",
  "name": "Artist Name"
}

// Response 201
{
  "user": {
    "id": "clx1234567",
    "email": "artist@example.com",
    "name": "Artist Name",
    "avatarUrl": null,
    "createdAt": "2024-01-15T10:30:00Z"
  },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}

// Response 400
{
  "error": "Email already exists"
}
```

#### POST /api/auth/login
```json
// Request
{
  "email": "artist@example.com",
  "password": "securePassword123"
}

// Response 200
{
  "user": { ... },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}

// Response 401
{
  "error": "Invalid credentials"
}
```

### Art Styles

#### GET /api/styles
```json
// Response 200
{
  "styles": [
    {
      "id": "clx1234567",
      "name": "Watercolor Landscapes",
      "description": "My watercolor style",
      "status": "TRAINED",
      "imageCount": 35,
      "thumbnailUrl": "https://...",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ]
}
```

#### POST /api/styles
```json
// Request
{
  "name": "My New Style",
  "description": "Description of my art style"
}

// Response 201
{
  "id": "clx1234567",
  "name": "My New Style",
  "description": "Description of my art style",
  "status": "DRAFT",
  "imageCount": 0,
  "createdAt": "2024-01-15T10:30:00Z"
}
```

### Image Upload

#### POST /api/upload/presigned
```json
// Request
{
  "styleId": "clx1234567",
  "filename": "artwork1.jpg",
  "contentType": "image/jpeg"
}

// Response 200
{
  "uploadUrl": "https://s3.amazonaws.com/bucket/...",
  "imageId": "clx7654321",
  "key": "styles/clx1234567/images/clx7654321.jpg"
}
```

#### POST /api/styles/:id/images/confirm
```json
// Request (after successful S3 upload)
{
  "imageId": "clx7654321",
  "width": 1024,
  "height": 768,
  "sizeBytes": 245760
}

// Response 200
{
  "id": "clx7654321",
  "filename": "artwork1.jpg",
  "originalUrl": "https://...",
  "thumbnailUrl": "https://...",
  "width": 1024,
  "height": 768
}
```

### Training

#### POST /api/styles/:id/train
```json
// Request
{
  "config": {
    "steps": 1000,
    "learningRate": 0.0001,
    "modelType": "LORA"
  }
}

// Response 202
{
  "trainingId": "clx8888888",
  "status": "QUEUED",
  "estimatedTime": 1800,
  "message": "Training job queued"
}
```

#### GET /api/styles/:id/training
```json
// Response 200
{
  "status": "PROCESSING",
  "progress": 45,
  "currentStep": 450,
  "totalSteps": 1000,
  "estimatedTimeRemaining": 900,
  "startedAt": "2024-01-15T10:30:00Z"
}
```

### Generation

#### POST /api/generate
```json
// Request
{
  "styleId": "clx1234567",
  "prompt": "A serene mountain landscape at sunset",
  "negativePrompt": "blurry, low quality",
  "width": 768,
  "height": 768,
  "steps": 30,
  "guidance": 7.5,
  "seed": null
}

// Response 202
{
  "generationId": "clx9999999",
  "status": "PROCESSING",
  "estimatedTime": 30
}
```

#### GET /api/generations/:id
```json
// Response 200 (completed)
{
  "id": "clx9999999",
  "status": "COMPLETED",
  "prompt": "A serene mountain landscape at sunset",
  "outputUrl": "https://...",
  "thumbnailUrl": "https://...",
  "width": 768,
  "height": 768,
  "seed": 12345678,
  "createdAt": "2024-01-15T10:30:00Z",
  "completedAt": "2024-01-15T10:30:30Z"
}
```

---

## Replicate API Integration

### Training a LoRA Model
```javascript
// server/services/trainingService.js
import Replicate from 'replicate'

const replicate = new Replicate({
  auth: process.env.REPLICATE_API_TOKEN,
})

export async function startTraining(styleId, imageUrls, config) {
  // Create a zip file of training images and upload to accessible URL
  const trainingDataUrl = await createTrainingDataUrl(imageUrls)

  const training = await replicate.trainings.create(
    "ostris",
    "flux-dev-lora-trainer",
    "e440909d3512c31646ee2e0c7d6f6f4923224863a6a10c494606e79fb5844571",
    {
      destination: `${process.env.REPLICATE_USERNAME}/${styleId}`,
      input: {
        input_images: trainingDataUrl,
        steps: config.steps || 1000,
        lora_rank: 16,
        optimizer: "adamw8bit",
        batch_size: 1,
        resolution: "512,768,1024",
        autocaption: true,
        trigger_word: `style_${styleId}`,
        learning_rate: config.learningRate || 0.0004,
      },
      webhook: `${process.env.API_URL}/api/webhooks/training`,
      webhook_events_filter: ["completed", "failed"],
    }
  )

  return {
    trainingId: training.id,
    status: training.status,
  }
}
```

### Generating Images
```javascript
// server/services/generationService.js
export async function generateImage(trainedModel, options) {
  const output = await replicate.run(
    trainedModel.modelVersion, // e.g., "username/style-id:version"
    {
      input: {
        prompt: `${options.prompt} in the style of style_${trainedModel.artStyleId}`,
        negative_prompt: options.negativePrompt || "",
        width: options.width || 512,
        height: options.height || 512,
        num_inference_steps: options.steps || 30,
        guidance_scale: options.guidance || 7.5,
        seed: options.seed || Math.floor(Math.random() * 2147483647),
      }
    }
  )

  return {
    imageUrl: output[0],
    seed: options.seed,
  }
}
```

### Webhook Handler
```javascript
// server/routes/webhooks.js
router.post('/training', async (req, res) => {
  const { id, status, output, error } = req.body

  if (status === 'succeeded') {
    await prisma.trainedModel.update({
      where: { replicateTrainingId: id },
      data: {
        status: 'COMPLETED',
        modelVersion: output.version,
        completedAt: new Date(),
      }
    })

    // Update art style status
    const model = await prisma.trainedModel.findUnique({
      where: { replicateTrainingId: id }
    })
    await prisma.artStyle.update({
      where: { id: model.artStyleId },
      data: { status: 'TRAINED' }
    })

    // Notify user via WebSocket
    io.to(`user:${model.userId}`).emit('training:completed', {
      styleId: model.artStyleId
    })
  }

  if (status === 'failed') {
    await prisma.trainedModel.update({
      where: { replicateTrainingId: id },
      data: {
        status: 'FAILED',
        errorMessage: error,
      }
    })
  }

  res.sendStatus(200)
})
```

---

## Zustand Store Examples

### authStore.js
```javascript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'
import { authService } from '@/services/authService'

export const useAuthStore = create(
  persist(
    (set, get) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      isLoading: false,

      login: async (email, password) => {
        set({ isLoading: true })
        try {
          const { user, token } = await authService.login(email, password)
          set({ user, token, isAuthenticated: true, isLoading: false })
          return { success: true }
        } catch (error) {
          set({ isLoading: false })
          return { success: false, error: error.message }
        }
      },

      register: async (data) => {
        set({ isLoading: true })
        try {
          const { user, token } = await authService.register(data)
          set({ user, token, isAuthenticated: true, isLoading: false })
          return { success: true }
        } catch (error) {
          set({ isLoading: false })
          return { success: false, error: error.message }
        }
      },

      logout: () => {
        set({ user: null, token: null, isAuthenticated: false })
      },

      updateUser: (updates) => {
        set({ user: { ...get().user, ...updates } })
      },
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({
        user: state.user,
        token: state.token,
        isAuthenticated: state.isAuthenticated,
      }),
    }
  )
)
```

### styleStore.js
```javascript
import { create } from 'zustand'
import { styleService } from '@/services/styleService'

export const useStyleStore = create((set, get) => ({
  styles: [],
  currentStyle: null,
  isLoading: false,
  error: null,

  fetchStyles: async () => {
    set({ isLoading: true, error: null })
    try {
      const styles = await styleService.getAll()
      set({ styles, isLoading: false })
    } catch (error) {
      set({ error: error.message, isLoading: false })
    }
  },

  fetchStyle: async (id) => {
    set({ isLoading: true, error: null })
    try {
      const style = await styleService.getById(id)
      set({ currentStyle: style, isLoading: false })
    } catch (error) {
      set({ error: error.message, isLoading: false })
    }
  },

  createStyle: async (data) => {
    set({ isLoading: true })
    try {
      const newStyle = await styleService.create(data)
      set((state) => ({
        styles: [...state.styles, newStyle],
        isLoading: false,
      }))
      return newStyle
    } catch (error) {
      set({ error: error.message, isLoading: false })
      throw error
    }
  },

  updateStyleStatus: (styleId, status) => {
    set((state) => ({
      styles: state.styles.map((s) =>
        s.id === styleId ? { ...s, status } : s
      ),
      currentStyle:
        state.currentStyle?.id === styleId
          ? { ...state.currentStyle, status }
          : state.currentStyle,
    }))
  },

  addImageToStyle: (styleId, image) => {
    set((state) => ({
      currentStyle:
        state.currentStyle?.id === styleId
          ? {
              ...state.currentStyle,
              images: [...state.currentStyle.images, image],
              imageCount: state.currentStyle.imageCount + 1,
            }
          : state.currentStyle,
    }))
  },

  removeImageFromStyle: (styleId, imageId) => {
    set((state) => ({
      currentStyle:
        state.currentStyle?.id === styleId
          ? {
              ...state.currentStyle,
              images: state.currentStyle.images.filter((i) => i.id !== imageId),
              imageCount: state.currentStyle.imageCount - 1,
            }
          : state.currentStyle,
    }))
  },
}))
```

---

## Environment Variables

```env
# .env.example

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/artistcloner

# Redis
REDIS_URL=redis://localhost:6379

# JWT
JWT_SECRET=your-super-secret-key-change-in-production
JWT_EXPIRY=7d

# OAuth (optional)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
DISCORD_CLIENT_ID=
DISCORD_CLIENT_SECRET=

# File Storage - Cloudflare R2 (S3-compatible)
STORAGE_ENDPOINT=https://account-id.r2.cloudflarestorage.com
STORAGE_ACCESS_KEY=your-access-key
STORAGE_SECRET_KEY=your-secret-key
STORAGE_BUCKET=artistcloner-uploads
STORAGE_PUBLIC_URL=https://pub-xxx.r2.dev

# Replicate API
REPLICATE_API_TOKEN=r8_your_token_here
REPLICATE_USERNAME=your-replicate-username

# Stripe (optional, for subscriptions)
STRIPE_SECRET_KEY=sk_test_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx
STRIPE_PRICE_BASIC=price_xxx
STRIPE_PRICE_PRO=price_xxx

# App URLs
APP_URL=http://localhost:5173
API_URL=http://localhost:3000
NODE_ENV=development

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
```

---

## Future Extensibility

### Text-to-Video (Planned)
- Integration with video generation models (Runway Gen-3, Luma Dream Machine, Kling)
- New OutputType.VIDEO in generation model
- Video player component in gallery
- Video-specific generation settings (duration, fps)

### Text-to-3D (Planned)
- Integration with 3D generation models (Meshy, Tripo AI, Rodin)
- New OutputType.MODEL_3D in generation model
- 3D viewer component using Three.js
- Export formats: GLB, OBJ, FBX
- Texture application using trained style

### Additional Planned Features
- Style mixing (blend multiple trained styles)
- Inpainting with style preservation
- Outpainting/image extension
- Real-time collaborative generation
- Public API access for developers
- Mobile app (React Native)
- Community marketplace for styles

---

## Getting Started

```bash
# 1. Install frontend dependencies
npm install

# 2. Set up environment variables
cp .env.example .env
# Edit .env with your values

# 3. Set up backend
cd server
npm install
cp .env.example .env
# Edit server/.env with your values

# 4. Initialize database
npx prisma generate
npx prisma db push

# 5. Start development servers
# Terminal 1 - Frontend
npm run dev

# Terminal 2 - Backend
cd server && npm run dev

# 6. Open http://localhost:5173
```

---

## Key Implementation Notes

1. **Image Upload Flow**: Use presigned URLs for direct browser-to-S3 uploads
2. **Training Queue**: Use BullMQ with Redis for reliable job processing
3. **WebSocket**: Use Socket.IO for real-time training/generation status
4. **Rate Limiting**: Apply per-user limits (100 requests/15 min)
5. **Image Validation**: Max 10MB, formats: JPEG, PNG, WebP
6. **Caching**: Redis for sessions, frequently accessed data
7. **Error Handling**: Global error boundary + toast notifications
8. **Security**: Helmet, CORS, parameterized queries, input sanitization
