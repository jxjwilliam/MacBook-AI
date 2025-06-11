# Authentication System

## Supported Providers
- Google OAuth
- GitHub OAuth
- Email/Password
- Magic Links

## Security Measures
- **Session Management**:
  - JWT with short expiry (1 hour)
  - Refresh tokens (30 day expiry)
  - Secure, HttpOnly cookies
- **Rate Limiting**:
  - 5 attempts per minute
  - IP-based blocking after 10 failures
- **Password Requirements**:
  - Minimum 12 characters
  - Required symbols/numbers

## Implementation Details
```typescript
// Example auth guard component
export default function AuthGuard({ children }: { children: ReactNode }) {
  const { user, loading } = useUserContext();
  
  if (loading) return <LoadingSpinner />;
  if (!user) return <RedirectToLogin />;
  
  return children;
}
```

## Social Auth Flow
1. User selects provider
2. Redirect to Supabase auth
3. OAuth handshake
4. Return to app with session
5. Create user profile if new
