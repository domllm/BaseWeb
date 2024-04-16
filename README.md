# BaseWeb

Baseweb is a project that has been developed as a continuation and enhancement of the work initially presented by the Astria AI team on their GitHub repository, `headshots-starter`. We extend our gratitude to the Astria AI team for their valuable contribution to the open-source community and for providing a solid foundation upon which we have been able to build Baseweb.

The original `headshots-starter` project, hosted at https://github.com/astriaai/headshots-starter, is an open-source initiative aimed at facilitating the creation of professional AI-generated headshots within minutes. This innovative solution leverages the power of Astria's AI model training and inference capabilities, combined with a user-friendly interface powered by Next.js and a robust database and authentication system provided by Supabase.


## How It Works

The app is powered by:

- 🚀 [Astria](https://www.astria.ai/) for AI model training & inference
- ▲ [Next.js](https://nextjs.org/) for app and landing page
- 🔋 [Supabase](https://supabase.com/) for DB & Auth
- 📩 [Resend](https://resend.com/) (optional) to email user when headshots are ready
- ⭐️ [Shadcn](https://ui.shadcn.com/) with [Tailwind CSS](https://tailwindcss.com/) for styles
- ▲ [Vercel](https://vercel.com/) for deployments
- 💳 [Stripe](https://stripe.com/) for billing


## Running Locally

To create your own app, follow these steps:

### 1. Vercel template

To setup Supabase/Vercel and your github repo, click on the Vercel Deploy Button and follow the steps.

IMPORTANT: In the Supabase integration step: Make sure you leave the Create sample tables option checked. This might take a few minutes to complete.


The Vercel Deployment will create a new repository with this template on your GitHub account and guide you through a new Supabase project creation. The Supabase Vercel Deploy Integration will set up the necessary Supabase environment variables and run the SQL migrations to set up the Database schema on your account. You can inspect the created tables in your project's Table editor.

This will create the tables with their respective columns and RLS policies:

- credits
- images
- models
- samples

### 2. Clone your newly created repo:

```
git clone {{your-repo-name}}
```

### 3. Enter your newly created repo's directory:

```
cd {{your-repo-name}}
```

### 4. Install dependencies:

For npm:

```bash
npm install
```

For yarn:

```bash
yarn
```

### 5. Magic Link Auth (Supabase)

In your supabase [dashboard](https://supabase.com/dashboard/), select newly created project, go to Authentication -> Email Templates -> Magic Link and paste the following template:

```
<h2>Magic Link</h2>
<p>Follow this link to login:</p>
<p><a href="{{ .SiteURL }}/auth/confirm?token_hash={{ .TokenHash }}&type=email">Log In</a></p>
```

Then, make sure to setup your site URL and redirect urls in the supabase dashboard under Authentication -> URL Configuration.

For example:

Site URL: https://headshots-starter.vercel.app

Redirect URL: https://headshots-starter.vercel.app/**

### 6. Create a [Astria](https://www.astria.ai/) account

In your `.env.local` file:

- Fill in `your_api_key` with your [Astria API key](https://www.astria.ai/users/edit#api)
- Fill in `your-webhook-secret` with any arbitrary URL friendly string eg.`shadf892yr398hq23h`
- Fill in `your-vercel-url` with a url to catch webhooks from Astria. This will be your vercel deployment url or Ngrok tunnel locally (eg. https://{your-hosted-url}/astria/train-webhook)
- Fill in `your-blob-read-write-token` with your Vercel Blob token (steps below)

### 7. Configure [Vercel Blob](https://vercel.com/docs/storage/vercel-blob/quickstart#client-uploads) for image uploads

In your Vercel project, create a [Blob store](https://vercel.com/docs/storage/vercel-blob/quickstart#create-a-blob-store)

- In your Vercel dashboard, select the Storage tab, then select the Connect Database button.
- Under the Create New tab, select Blob and then the Continue button.

Then to configure in your .env:

- In your Vercel dashboard, select the Settings tab, then select the Environment Variables tab.
- Copy your `BLOB_READ_WRITE_TOKEN` to your .env

### 8. Create a [Resend](https://resend.com/) account (Optional)

- Fill in `your-resend-api-key` with your Resend API Key if you wish to use Resend to email users when their model has finished training.

### 9. Configure [Stripe](https://stripe.com) to bill users on a credit basis. (Optional)

The current setup is for a credit based system. 1 credit = 1 model train.

To enable Stripe billing, you will need to fill out the following fields in your `.env.local` file:

- STRIPE_SECRET_KEY=your-stripe-secret-key
- STRIPE_WEBHOOK_SECRET=your-stripe-webhook-secret
- STRIPE_PRICE_ID_ONE_CREDIT=your-stripe-price-id-one-credit
- STRIPE_PRICE_ID_THREE_CREDITS=your-stripe-price-id-three-credit
- STRIPE_PRICE_ID_FIVE_CREDITS=your-stripe-price-id-five-credit
- NEXT_PUBLIC_STRIPE_IS_ENABLED=false # set to true to enable Stripe payments

You need to do multiple things to get Stripe working:

- Get your Stripe API secret key from the [Stripe Dashboard](https://dashboard.stripe.com/test/apikeys)
- Create a [Stripe Webhook](https://dashboard.stripe.com/test/webhooks) that will point to your hosted URL. The webhook should be listening for the `checkout.session.completed` event. The webhook should point to `your-hosted-url/stripe/subscription-webhook`.
- Create a [Stripe Price](https://dashboard.stripe.com/test/products) for each credit package you want to offer.
- Create a [Stripe Pricing Table](https://dashboard.stripe.com/test/pricing-tables) and replace the script @/components/stripe/StripeTable.tsx with your own values. It should look like this:

```js
<stripe-pricing-table
  pricing-table-id="your-stripe-pricing-table-id"
  publishable-key="your-stripe-publishable-key"
  client-reference-id={user.id}
  customer-email={user.email}
></stripe-pricing-table>
```

Here are the products you need to create to get Stripe working with our example, checkout the images [Here](/public/Stripe/)

To create them go on the Stripe dashboard, search for Product Catalog and then click on the add product button on the top right of the screen. You will need to create 3 products, one for each credit package as shown in the images before. We set them to One time payments, but you can change that if you want to and you can set the price too. After creating the products make sure to update the variables in the .env.local [your-stripe-price-id-one-credit, your-stripe-price-id-three-credit, your-stripe-price-id-five-credit] with their respective price ids, each price id is found in the product page at the bottom.

### 10. Start the development server:

For npm:

```bash
npm run dev
```

For yarn:

```bash
yarn dev
```

### 11. Visit `http://localhost:3000` in your browser to see the running app.