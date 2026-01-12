# ТЕХНИЧЕСКАЯ ДОКУМЕНТАЦИЯ ДЛЯ РАЗРАБОТЧИКОВ

## 1. ТЕХНОЛОГИЧЕСКИЙ СТЕК (РЕКОМЕНДАЦИИ)

### Frontend
- **Framework:** React 18+ или Next.js 14+
- **Styling:** Tailwind CSS + Radix UI
- **Animations:** Framer Motion
- **Charts:** Recharts или D3.js
- **State Management:** Zustand или React Context
- **Forms:** React Hook Form + Zod

### Backend
- **Runtime:** Node.js 20+
- **Framework:** Next.js API Routes или Express
- **Database:** PostgreSQL + Prisma ORM
- **Auth:** NextAuth.js или Supabase Auth
- **File Storage:** Supabase Storage или AWS S3

### Infrastructure
- **Hosting:** Vercel или Netlify
- **Database:** Supabase или Railway
- **Analytics:** Plausible или PostHog
- **Monitoring:** Sentry

---

## 2. СТРУКТУРА ПРОЕКТА

```
happiness-decathlon/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (marketing)/       # Публичные страницы
│   │   │   ├── page.tsx       # Главная
│   │   │   ├── about/
│   │   │   ├── decathlon/
│   │   │   └── roadmap/
│   │   ├── (dashboard)/       # Личный кабинет
│   │   │   ├── dashboard/
│   │   │   └── tools/
│   │   └── api/               # API routes
│   │
│   ├── components/
│   │   ├── ui/                # Базовые компоненты
│   │   ├── interactive/       # Интерактивные элементы
│   │   │   ├── WheelOfDecathlon.tsx
│   │   │   ├── ProgressTracker.tsx
│   │   │   ├── MoodTracker.tsx
│   │   │   └── RelationshipMatrix.tsx
│   │   ├── sections/          # Секции страниц
│   │   └── layout/            # Лейаут компоненты
│   │
│   ├── lib/
│   │   ├── db/               # Database utilities
│   │   ├── auth/             # Auth utilities
│   │   └── utils/            # Helper functions
│   │
│   ├── styles/
│   │   └── globals.css
│   │
│   └── types/
│       └── index.ts
│
├── public/
│   ├── images/
│   ├── icons/
│   └── fonts/
│
└── prisma/
    └── schema.prisma
```

---

## 3. БАЗА ДАННЫХ (SCHEMA)

### Users Table
```prisma
model User {
  id            String    @id @default(cuid())
  email         String    @unique
  name          String?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
  
  profile       Profile?
  progress      Progress[]
  moodEntries   MoodEntry[]
  relationships Relationship[]
  goals         Goal[]
}
```

### Profile Table
```prisma
model Profile {
  id           String   @id @default(cuid())
  userId       String   @unique
  user         User     @relation(fields: [userId], references: [id])
  
  age          Int?
  bio          String?
  avatar       String?
  
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
}
```

### Progress Table
```prisma
model Progress {
  id          String   @id @default(cuid())
  userId      String
  user        User     @relation(fields: [userId], references: [id])
  
  category    String   // foundation, heart, mind, purpose
  subcategory String   // sleep, nutrition, movement, etc.
  value       Int      // 0-100
  
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  @@index([userId, category])
}
```

### MoodEntry Table
```prisma
model MoodEntry {
  id        String   @id @default(cuid())
  userId    String
  user      User     @relation(fields: [userId], references: [id])
  
  mood      Int      // 1-5
  note      String?
  date      DateTime @default(now())
  
  createdAt DateTime @default(now())
  
  @@index([userId, date])
}
```

### Relationship Table
```prisma
model Relationship {
  id          String   @id @default(cuid())
  userId      String
  user        User     @relation(fields: [userId], references: [id])
  
  name        String
  type        String   // partner, family, friend, colleague
  energy      Int      // -5 to +5
  frequency   Int      // 1-5
  notes       String?
  
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  @@index([userId])
}
```

---

## 4. API ENDPOINTS

### Authentication
```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/auth/session
```

### User & Profile
```
GET    /api/user/profile
PUT    /api/user/profile
DELETE /api/user/account
```

### Progress Tracking
```
GET    /api/progress              # Get all progress
GET    /api/progress/:category    # Get category progress
POST   /api/progress              # Update progress
GET    /api/progress/stats        # Get statistics
```

### Mood Tracking
```
GET    /api/mood                  # Get mood history
POST   /api/mood                  # Add mood entry
GET    /api/mood/stats            # Get mood statistics
```

### Relationships
```
GET    /api/relationships         # Get all relationships
POST   /api/relationships         # Add relationship
PUT    /api/relationships/:id     # Update relationship
DELETE /api/relationships/:id     # Delete relationship
```

### Tools & Checklists
```
GET    /api/checklists/:type      # Get checklist
POST   /api/checklists/:type      # Save checklist progress
```

---

## 5. КОМПОНЕНТЫ (ПРИМЕРЫ КОДА)

### WheelOfDecathlon Component

```tsx
// components/interactive/WheelOfDecathlon.tsx
'use client'

import { useState } from 'react'
import { motion } from 'framer-motion'

interface Segment {
  id: string
  name: string
  category: 'foundation' | 'heart' | 'mind' | 'purpose'
  progress: number
  angle: number
}

export function WheelOfDecathlon({ userProgress }) {
  const [hoveredSegment, setHoveredSegment] = useState<string | null>(null)
  
  const segments: Segment[] = [
    { id: 'sleep', name: 'Сон', category: 'foundation', progress: 75, angle: 0 },
    { id: 'nutrition', name: 'Питание', category: 'foundation', progress: 60, angle: 36 },
    // ... остальные сегменты
  ]
  
  return (
    <div className="relative w-[600px] h-[600px]">
      <svg viewBox="0 0 200 200" className="w-full h-full">
        {segments.map((segment) => (
          <SegmentPath
            key={segment.id}
            segment={segment}
            isHovered={hoveredSegment === segment.id}
            onHover={() => setHoveredSegment(segment.id)}
            onLeave={() => setHoveredSegment(null)}
          />
        ))}
      </svg>
      
      {hoveredSegment && (
        <SegmentTooltip segment={segments.find(s => s.id === hoveredSegment)} />
      )}
    </div>
  )
}
```

---

