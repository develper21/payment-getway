# 🚀 Vercel Deployment Checklist

## ✅ Code Changes (COMPLETED)
- [x] Fixed `utils/supabase/admin.ts` - Lazy-load Supabase client
- [x] Created deployment documentation

## 📋 Before Deploying

### 1. Add Environment Variables to Vercel
Go to your Vercel project → Settings → Environment Variables and add:

#### Required Variables:
- [ ] `NEXT_PUBLIC_SUPABASE_URL`
- [ ] `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- [ ] `SUPABASE_SERVICE_ROLE_KEY`
- [ ] `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
- [ ] `STRIPE_SECRET_KEY`
- [ ] `STRIPE_WEBHOOK_SECRET`
- [ ] `NEXT_PUBLIC_SITE_URL`

**Important:** Select all environments (Production, Preview, Development) for each variable!

### 2. Get Your Values

#### From Supabase Dashboard (https://app.supabase.com):
1. Select your project
2. Go to Settings → API
3. Copy the values

#### From Stripe Dashboard (https://dashboard.stripe.com):
1. Go to Developers → API keys
2. Copy publishable and secret keys
3. Go to Developers → Webhooks
4. Add endpoint: `https://your-app.vercel.app/api/webhooks`
5. Select events: `product.*`, `price.*`, `customer.subscription.*`, `checkout.session.completed`
6. Copy the signing secret

### 3. Push to GitHub
```bash
cd /home/narvin/Documents/FullStack/subscriptionPayments/frontend
git add .
git commit -m "Fix: Lazy-load Supabase admin client for Vercel deployment"
git push origin main
```

### 4. Deploy
Vercel will automatically deploy if connected to GitHub, or run:
```bash
vercel --prod
```

## 🧪 After Deployment

### Test Your Deployment:
1. [ ] Visit your site URL
2. [ ] Check build logs in Vercel dashboard
3. [ ] Test authentication flow
4. [ ] Test Stripe checkout
5. [ ] Verify webhook is receiving events

### Verify Webhook:
```bash
# Test webhook endpoint
curl -X POST https://your-app.vercel.app/api/webhooks \
  -H "Content-Type: application/json" \
  -d '{"test": true}'
```

## 📚 Documentation Created
- `DEPLOYMENT_FIX_SUMMARY.md` - Technical details of the fix
- `VERCEL_DEPLOYMENT_GUIDE.md` - Complete deployment guide
- `DEPLOYMENT_CHECKLIST.md` - This checklist

## 🆘 Troubleshooting

### Build fails with same error?
- Double-check all environment variables are set
- Make sure you selected all environments
- Redeploy after adding variables

### Runtime errors?
- Check Vercel function logs
- Verify environment variable values are correct
- Check Supabase and Stripe dashboards for errors

### Webhook not working?
- Verify webhook URL in Stripe matches deployment URL
- Check webhook signing secret matches
- Review Vercel function logs

## ✨ Success Indicators
- ✅ Build completes without errors
- ✅ No "supabaseUrl is required" error
- ✅ Webhook endpoint responds
- ✅ Stripe events are processed
- ✅ Database updates work correctly

---

**Need help?** Check the detailed guides:
- `VERCEL_DEPLOYMENT_GUIDE.md` for step-by-step instructions
- `DEPLOYMENT_FIX_SUMMARY.md` for technical details
