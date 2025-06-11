# Next.js: Comprehensive Guide for Modern Web Applications

## Overview

Next.js is a React framework that enables server-side rendering, static site generation, and full-stack web development capabilities. It's designed to optimize performance, developer experience, and production deployment.

## Core Architecture & Concepts

### App Router vs Pages Router

**App Router (Recommended for New Projects)**
- Introduced in Next.js 13+
- Built on React Server Components (RSCs)
- File-based routing with enhanced features
- Better support for layouts, loading states, and error handling
- Streaming and Suspense integration

**Pages Router (Legacy)**
- Traditional Next.js routing system
- Still supported but not recommended for new projects
- Uses `getServerSideProps`, `getStaticProps`, etc.

### Server vs Client Components

```tsx
// Server Component (default in App Router)
export default async function ServerPage() {
  // Fetch data directly in a Server Component
  const data = await fetch('https://api.example.com/data', {
    cache: 'no-store' // Similar to getServerSideProps
  })
  const posts = await data.json()
  
  return (
    <div>
      {posts.map(post => (
        <PostCard key={post.id} post={post} />
      ))}
    </div>
  )
}

// Client Component
'use client'
import { useState, useEffect } from 'react'

export default function ClientComponent() {
  const [data, setData] = useState(null)
  
  useEffect(() => {
    fetch('/api/data').then(res => res.json()).then(setData)
  }, [])
  
  return <div>{data ? JSON.stringify(data) : 'Loading...'}</div>
}
```

## Data Fetching Strategies

### Server-Side Data Fetching

```tsx
// Static Generation (Force Cache)
async function getStaticData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'force-cache' // Default, similar to getStaticProps
  })
  return res.json()
}

// Server-Side Rendering (No Store)
async function getDynamicData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store' // Similar to getServerSideProps
  })
  return res.json()
}

// Incremental Static Regeneration
async function getRevalidatedData() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 } // Revalidate every 60 seconds
  })
  return res.json()
}
```

### Client-Side Data Fetching

```tsx
'use client'
import useSWR from 'swr'

const fetcher = (url: string) => fetch(url).then(res => res.json())

export default function ClientDataComponent() {
  const { data, error, isLoading } = useSWR('/api/user-data', fetcher)
  
  if (error) return <div>Failed to load</div>
  if (isLoading) return <div>Loading...</div>
  
  return <div>Hello {data.name}!</div>
}
```

## Authentication Integration

### Middleware-Based Auth

```tsx
// middleware.ts
import { NextRequest, NextResponse } from 'next/server'
import { decrypt } from '@/app/lib/session'
import { cookies } from 'next/headers'

const protectedRoutes = ['/dashboard', '/profile', '/admin']
const publicRoutes = ['/login', '/signup', '/']

export default async function middleware(req: NextRequest) {
  const path = req.nextUrl.pathname
  const isProtectedRoute = protectedRoutes.includes(path)
  const isPublicRoute = publicRoutes.includes(path)

  // Decrypt the session from the cookie
  const cookie = (await cookies()).get('session')?.value
  const session = await decrypt(cookie)

  // Redirect to /login if the user is not authenticated
  if (isProtectedRoute && !session?.userId) {
    return NextResponse.redirect(new URL('/login', req.nextUrl))
  }

  // Redirect to /dashboard if the user is authenticated
  if (
    isPublicRoute &&
    session?.userId &&
    !req.nextUrl.pathname.startsWith('/dashboard')
  ) {
    return NextResponse.redirect(new URL('/dashboard', req.nextUrl))
  }

  return NextResponse.next()
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|.*\\.png$).*)'],
}
```

### Session Management

```tsx
// lib/session.ts
import { cookies } from 'next/headers'
import { db } from '@/lib/db'
import { encrypt, decrypt } from '@/lib/crypto'

export async function createSession(userId: number) {
  const expiresAt = new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)

  // 1. Create a session in the database
  const data = await db.insert(sessions).values({
    userId,
    expiresAt,
  }).returning({ id: sessions.id })

  const sessionId = data[0].id

  // 2. Encrypt the session ID
  const session = await encrypt({ sessionId, expiresAt })

  // 3. Store the session in cookies
  const cookieStore = await cookies()
  cookieStore.set('session', session, {
    httpOnly: true,
    secure: true,
    expires: expiresAt,
    sameSite: 'lax',
    path: '/',
  })
}

export async function verifySession() {
  const cookie = (await cookies()).get('session')?.value
  const session = await decrypt(cookie)

  if (!session?.sessionId) {
    return null
  }

  const result = await db
    .select()
    .from(sessions)
    .where(eq(sessions.id, session.sessionId))
    .limit(1)

  if (result.length === 0) {
    return null
  }

  return { isAuth: true, sessionId: session.sessionId, userId: result[0].userId }
}
```

## API Routes & Server Actions

### Route Handlers

```tsx
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { verifySession } from '@/lib/session'

export async function GET(request: NextRequest) {
  const session = await verifySession()
  
  if (!session) {
    return new Response(null, { status: 401 })
  }

  // Fetch user data
  const users = await db.select().from(usersTable)
  
  return NextResponse.json(users)
}

export async function POST(request: NextRequest) {
  const session = await verifySession()
  
  if (!session || session.user.role !== 'admin') {
    return new Response(null, { status: 403 })
  }

  const body = await request.json()
  
  // Create user
  const newUser = await db.insert(usersTable).values(body)
  
  return NextResponse.json(newUser)
}
```

### Server Actions

```tsx
// app/actions/auth.ts
'use server'

import { db } from '@/lib/db'
import { createSession } from '@/lib/session'
import { redirect } from 'next/navigation'
import bcrypt from 'bcryptjs'

export async function signup(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string
  const password = formData.get('password') as string

  // Validate input
  if (!name || !email || !password) {
    return { error: 'Missing required fields' }
  }

  // Hash password
  const hashedPassword = await bcrypt.hash(password, 10)

  try {
    // Create user
    const user = await db.insert(users).values({
      name,
      email,
      password: hashedPassword,
    }).returning({ id: users.id })

    // Create session
    await createSession(user[0].id)
    
    redirect('/dashboard')
  } catch (error) {
    return { error: 'Failed to create account' }
  }
}

// Usage in form component
export function SignupForm() {
  return (
    <form action={signup}>
      <input name="name" placeholder="Name" required />
      <input name="email" type="email" placeholder="Email" required />
      <input name="password" type="password" placeholder="Password" required />
      <button type="submit">Sign Up</button>
    </form>
  )
}
```

## Payment Integration Architecture

### Stripe Integration Setup

```tsx
// app/api/create-payment-intent/route.ts
import { NextRequest, NextResponse } from 'next/server'
import Stripe from 'stripe'
import { verifySession } from '@/lib/session'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

export async function POST(request: NextRequest) {
  const session = await verifySession()
  
  if (!session) {
    return new Response(null, { status: 401 })
  }

  const { amount, currency = 'usd' } = await request.json()

  try {
    const paymentIntent = await stripe.paymentIntents.create({
      amount: amount * 100, // Convert to cents
      currency,
      metadata: {
        userId: session.userId.toString(),
      },
    })

    return NextResponse.json({
      clientSecret: paymentIntent.client_secret,
    })
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to create payment intent' },
      { status: 500 }
    )
  }
}
```

### Webhook Handling

```tsx
// app/api/webhooks/stripe/route.ts
import { NextRequest, NextResponse } from 'next/server'
import Stripe from 'stripe'
import { db } from '@/lib/db'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)
const webhookSecret = process.env.STRIPE_WEBHOOK_SECRET!

export async function POST(request: NextRequest) {
  const body = await request.text()
  const signature = request.headers.get('stripe-signature')!

  let event: Stripe.Event

  try {
    event = stripe.webhooks.constructEvent(body, signature, webhookSecret)
  } catch (err) {
    return new Response('Webhook signature verification failed', { status: 400 })
  }

  switch (event.type) {
    case 'payment_intent.succeeded':
      const paymentIntent = event.data.object as Stripe.PaymentIntent
      
      // Update database with successful payment
      await db.insert(payments).values({
        userId: parseInt(paymentIntent.metadata.userId),
        stripePaymentIntentId: paymentIntent.id,
        amount: paymentIntent.amount / 100,
        status: 'completed',
        createdAt: new Date(),
      })
      
      break
    
    case 'customer.subscription.updated':
      // Handle subscription updates
      break
      
    default:
      console.log(`Unhandled event type: ${event.type}`)
  }

  return new Response('OK', { status: 200 })
}
```

## Performance Optimization

### Image Optimization

```tsx
import Image from 'next/image'

export default function OptimizedImages() {
  return (
    <div>
      {/* Responsive image with priority loading */}
      <Image
        src="/hero-image.jpg"
        alt="Hero"
        width={1200}
        height={600}
        priority
        className="w-full h-auto"
      />
      
      {/* Lazy loaded image with placeholder */}
      <Image
        src="/gallery-image.jpg"
        alt="Gallery"
        width={400}
        height={300}
        placeholder="blur"
        blurDataURL="data:image/jpeg;base64,..."
      />
    </div>
  )
}
```

### Code Splitting & Dynamic Imports

```tsx
import dynamic from 'next/dynamic'
import { Suspense } from 'react'

// Dynamic import with loading state
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <p>Loading...</p>,
  ssr: false, // Disable SSR for this component
})

// Suspense boundaries for streaming
export default function Page() {
  return (
    <div>
      <h1>Main Content</h1>
      
      <Suspense fallback={<div>Loading heavy component...</div>}>
        <HeavyComponent />
      </Suspense>
    </div>
  )
}
```

## Layout & Navigation

### Root Layout

```tsx
// app/layout.tsx
import { Inter } from 'next/font/google'
import './globals.css'

const inter = Inter({ subsets: ['latin'] })

export const metadata = {
  title: 'My App',
  description: 'Next.js application with authentication',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <header>
          <Navigation />
        </header>
        <main>{children}</main>
        <footer>
          <Footer />
        </footer>
      </body>
    </html>
  )
}
```

### Navigation with Authentication

```tsx
// components/Navigation.tsx
import Link from 'next/link'
import { verifySession } from '@/lib/session'
import { SignOutButton } from './SignOutButton'

export default async function Navigation() {
  const session = await verifySession()

  return (
    <nav className="flex items-center justify-between p-4">
      <Link href="/" className="text-xl font-bold">
        My App
      </Link>
      
      <div className="flex gap-4">
        {session ? (
          <>
            <Link href="/dashboard">Dashboard</Link>
            <Link href="/profile">Profile</Link>
            <SignOutButton />
          </>
        ) : (
          <>
            <Link href="/login">Login</Link>
            <Link href="/signup">Sign Up</Link>
          </>
        )}
      </div>
    </nav>
  )
}
```

## Best Practices

### Error Handling

```tsx
// app/error.tsx
'use client'

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    <div className="flex flex-col items-center justify-center min-h-screen">
      <h2 className="text-2xl font-bold mb-4">Something went wrong!</h2>
      <button
        onClick={reset}
        className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Try again
      </button>
    </div>
  )
}

// app/not-found.tsx
import Link from 'next/link'

export default function NotFound() {
  return (
    <div className="flex flex-col items-center justify-center min-h-screen">
      <h2 className="text-2xl font-bold mb-4">Not Found</h2>
      <p className="mb-4">Could not find requested resource</p>
      <Link href="/" className="text-blue-500 hover:underline">
        Return Home
      </Link>
    </div>
  )
}
```

### Environment Variables

```bash
# .env.local
DATABASE_URL="postgresql://..."
STRIPE_SECRET_KEY="sk_test_..."
STRIPE_PUBLISHABLE_KEY="pk_test_..."
STRIPE_WEBHOOK_SECRET="whsec_..."
NEXTAUTH_SECRET="your-secret-key"
NEXTAUTH_URL="http://localhost:3000"
```

```tsx
// Access in Server Components
export default async function ServerPage() {
  const apiKey = process.env.API_KEY // Server-side only
  
  return <div>Server rendered content</div>
}

// Access in Client Components (must be prefixed with NEXT_PUBLIC_)
'use client'
export default function ClientPage() {
  const publicKey = process.env.NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY
  
  return <div>Client rendered content</div>
}
```

### Testing Setup

```tsx
// __tests__/page.test.tsx
import { render, screen } from '@testing-library/react'
import Page from '@/app/page'

jest.mock('next/navigation', () => ({
  useRouter() {
    return {
      prefetch: () => null
    }
  }
}))

describe('Page', () => {
  it('renders a heading', () => {
    render(<Page />)
    
    const heading = screen.getByRole('heading', { level: 1 })
    
    expect(heading).toBeInTheDocument()
  })
})
```

## Deployment Considerations

### Production Optimizations

- Enable `output: 'standalone'` for Docker deployments
- Configure ISR for frequently updated content
- Use edge functions for geographically distributed APIs
- Implement proper caching strategies
- Set up monitoring and analytics

### Security Best Practices

- Always validate and sanitize user inputs
- Use HTTPS in production
- Implement proper CORS policies
- Set secure cookie options
- Regular security audits and dependency updates

This comprehensive guide covers the essential aspects of building production-ready Next.js applications with your specified tech stack integration points.
