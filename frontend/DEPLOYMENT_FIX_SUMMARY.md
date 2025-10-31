# Deployment Fix Summary

## Problem
Your Vercel deployment was failing with this error:
```
Error: supabaseUrl is required.
    at /vercel/path0/frontend/.next/server/chunks/657.js:34:40196
```

## Root Cause
The Supabase admin client in `utils/supabase/admin.ts` was being initialized at **module load time** (during the build process):

```typescript
// ❌ OLD CODE - Initialized at build time
const supabaseAdmin = createClient<Database>(
  process.env.NEXT_PUBLIC_SUPABASE_URL || '',
  process.env.SUPABASE_SERVICE_ROLE_KEY || ''
);
```

During Vercel's build process:
1. Next.js tries to collect page data for all routes (including `/api/webhooks`)
2. The webhook route imports `admin.ts`
3. `admin.ts` tries to create a Supabase client immediately
4. Environment variables are **not available** during build time
5. Supabase SDK throws error: "supabaseUrl is required"

## Solution Applied
Converted the Supabase admin client to **lazy-loading** (created only when actually used at runtime):

```typescript
// ✅ NEW CODE - Lazy-loaded at runtime
const getSupabaseAdmin = () => {
  const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL;
  const supabaseServiceRoleKey = process.env.SUPABASE_SERVICE_ROLE_KEY;

  if (!supabaseUrl || !supabaseServiceRoleKey) {
    throw new Error(
      'Missing Supabase environment variables. Please set NEXT_PUBLIC_SUPABASE_URL and SUPABASE_SERVICE_ROLE_KEY.'
    );
  }

  return createClient<Database>(supabaseUrl, supabaseServiceRoleKey);
};
```

All 10 references to `supabaseAdmin` were updated to `getSupabaseAdmin()`.

## Files Modified
- ✅ `utils/supabase/admin.ts` - Fixed lazy-loading of Supabase client

## Next Steps
1. **Configure environment variables in Vercel** (see `VERCEL_DEPLOYMENT_GUIDE.md`)
2. **Push changes to GitHub**:
   ```bash
   git add .
   git commit -m "Fix: Lazy-load Supabase admin client for Vercel deployment"
   git push origin main
   ```
3. **Vercel will auto-deploy** (or run `vercel --prod`)

## Why This Works
- ✅ Build process no longer tries to initialize Supabase client
- ✅ Client is created only at runtime when webhooks are actually called
- ✅ Environment variables are available at runtime
- ✅ Better error messages if env vars are missing

## Testing
After deployment, test your webhook:
```bash
curl -X POST https://your-app.vercel.app/api/webhooks \
  -H "Content-Type: application/json" \
  -d '{"test": true}'
```

You should get a response (not a 500 error).
