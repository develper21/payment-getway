# Vercel Deployment Guide

## Issue Fixed
The build error `supabaseUrl is required` has been resolved by converting the Supabase admin client from being initialized at module load time to lazy-loaded at runtime.

## Required Environment Variables

You **MUST** configure these environment variables in your Vercel project settings before deployment:

### Supabase Variables
1. `NEXT_PUBLIC_SUPABASE_URL` - Your Supabase project URL
2. `NEXT_PUBLIC_SUPABASE_ANON_KEY` - Your Supabase anonymous key
3. `SUPABASE_SERVICE_ROLE_KEY` - Your Supabase service role key (⚠️ Keep this secret!)

### Stripe Variables
4. `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` - Your Stripe publishable key
5. `STRIPE_SECRET_KEY` - Your Stripe secret key (⚠️ Keep this secret!)
6. `STRIPE_WEBHOOK_SECRET` - Your Stripe webhook signing secret (⚠️ Keep this secret!)

### Site Configuration
7. `NEXT_PUBLIC_SITE_URL` - Your production site URL (e.g., `https://your-app.vercel.app`)

### Optional GitHub OAuth (if using)
8. `SUPABASE_AUTH_EXTERNAL_GITHUB_REDIRECT_URI`
9. `SUPABASE_AUTH_EXTERNAL_GITHUB_CLIENT_ID`
10. `SUPABASE_AUTH_EXTERNAL_GITHUB_SECRET`

## How to Add Environment Variables in Vercel

### Method 1: Via Vercel Dashboard
1. Go to your project on [Vercel Dashboard](https://vercel.com/dashboard)
2. Click on your project
3. Go to **Settings** → **Environment Variables**
4. Add each variable:
   - Enter the **Key** (e.g., `NEXT_PUBLIC_SUPABASE_URL`)
   - Enter the **Value** (paste your actual value)
   - Select environments: **Production**, **Preview**, and **Development**
   - Click **Save**
5. Repeat for all required variables

### Method 2: Via Vercel CLI
```bash
# Install Vercel CLI if not already installed
npm i -g vercel

# Login to Vercel
vercel login

# Link your project
vercel link

# Add environment variables
vercel env add NEXT_PUBLIC_SUPABASE_URL
vercel env add NEXT_PUBLIC_SUPABASE_ANON_KEY
vercel env add SUPABASE_SERVICE_ROLE_KEY
vercel env add NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY
vercel env add STRIPE_SECRET_KEY
vercel env add STRIPE_WEBHOOK_SECRET
vercel env add NEXT_PUBLIC_SITE_URL
```

## Where to Find Your Values

### Supabase Values
1. Go to [Supabase Dashboard](https://app.supabase.com)
2. Select your project
3. Go to **Settings** → **API**
4. Copy:
   - **Project URL** → `NEXT_PUBLIC_SUPABASE_URL`
   - **anon public** key → `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - **service_role** key → `SUPABASE_SERVICE_ROLE_KEY`

### Stripe Values
1. Go to [Stripe Dashboard](https://dashboard.stripe.com)
2. Go to **Developers** → **API keys**
3. Copy:
   - **Publishable key** → `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
   - **Secret key** → `STRIPE_SECRET_KEY`
4. For webhook secret:
   - Go to **Developers** → **Webhooks**
   - Add endpoint: `https://your-app.vercel.app/api/webhooks`
   - Select events: `product.*`, `price.*`, `customer.subscription.*`, `checkout.session.completed`
   - Copy **Signing secret** → `STRIPE_WEBHOOK_SECRET`

## Deploy Steps

1. **Add all environment variables** (as described above)
2. **Push your code** to GitHub:
   ```bash
   git add .
   git commit -m "Fix: Lazy-load Supabase admin client for Vercel deployment"
   git push origin main
   ```
3. **Vercel will automatically deploy** (if connected to GitHub)
4. Or **manually deploy**:
   ```bash
   vercel --prod
   ```

## Verify Deployment

After deployment:
1. Check build logs for any errors
2. Test your webhook endpoint: `https://your-app.vercel.app/api/webhooks`
3. Verify Stripe webhook is receiving events

## Troubleshooting

### Build still fails?
- Double-check all environment variables are set correctly
- Make sure you selected all environments (Production, Preview, Development)
- Try redeploying after adding variables

### Webhook not working?
- Verify `STRIPE_WEBHOOK_SECRET` matches your Stripe webhook endpoint
- Check Vercel function logs for errors
- Ensure webhook URL in Stripe matches your deployment URL

### Database connection issues?
- Verify Supabase credentials are correct
- Check if your Supabase project is active
- Ensure RLS policies are configured correctly

## Need Help?
If you encounter any issues, check:
- Vercel deployment logs
- Vercel function logs (for runtime errors)
- Stripe webhook logs
- Supabase logs
