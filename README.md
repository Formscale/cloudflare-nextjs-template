# Cloudflare Next.js Template

### WIP!

A starter template for building a Next.js app with Cloudflare Pages.

Sponsored by [Formscale](https://formscale.com).

Note: Some commands may be incompatible with Windows machines.

## Included

- Cloudflare D1/R2
- Next.js
- TypeScript
- ESLint
- Prettier
- Shadcn
- Prisma
- Tailwind CSS
- Zod

## Getting Started

Clone the repository:

```bash
git clone https://github.com/Formscale/cloudflare-nextjs-template.git
```

Install dependencies:

```bash
pnpm install
```

Set a site name, name, and branch name in the `src/constants/config.ts` file.

```ts
const config = {
  SITE_NAME: "Cloudflare Next.js Template",
  NAME: "nextjs-template",
  MAIN_BRANCH: "master",
} as const;

export default config;
```

Generate types:

```bash
pnpm run types
```

Generate Prisma client (if not starting fresh):

```bash
pnpm run generate
```

Then, run the development server:

```bash
pnpm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser to see the page.

## Database (D1)

### Initial Setup

Create a new database:

```bash
pnpm run create:db
```

You may be prompted to sign in to Cloudflare.

Copy your database name & ID to the `wrangler.jsonc` file.

```json
"d1_databases": [
  {
    "binding": "DB",
    "database_name": "nextjs-template-db",
    "database_id": "__YOUR_D1_DATABASE_ID__"
  }
]
```

Note: If starting fresh, clear the migrations folder:

```bash
pnpm run migrate:clear
```

Add your first migration (e.g., "add_users_table"):

```bash
pnpm run migrate:initial add_users_table
```

Apply the migration to generate D1 locally:

```bash
pnpm run apply
```

### Subsequent changes

Edit the prisma schema file:

```prisma
// prisma/schema.prisma

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String? // new field
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

Create a new migration (replace "add_name_field" with your migration name):

```bash
pnpm run migrate add_name_field
```

If present, remove these lines from the migration file:

```sql
-- DropTable
PRAGMA foreign_keys=off;
DROP TABLE "_cf_METADATA";
PRAGMA foreign_keys=on;
```

To create and immediately apply the migration:

```bash
pnpm run migrate:apply add_name_field # local
pnpm run migrate:apply:remote add_name_field # production
```

### Managing migrations

List all migrations:

```bash
pnpm run list # local
pnpm run list:remote # production
```

Apply pending migrations:

```bash
pnpm run apply # local
pnpm run apply:remote # production
```

Generate Prisma client:

```bash
pnpm run generate
```

## Storage (R2)

### Initial Setup

Create a new bucket:

```bash
pnpm run create:bucket
```

Copy your bucket name to the `wrangler.jsonc` file.

```json
"r2_buckets": [
  {
    "binding": "BUCKET",
    "bucket_name": "nextjs-template-bucket"
  }
]
```

Adding files to your bucket:

```tsx
import { getRequestContext } from "@cloudflare/next-on-pages";

const { env } = getRequestContext();

const key = "file.txt"; // unique identifier for the file
const file = new TextEncoder().encode("Hello, world!"); // file content

await env.BUCKET.put(key, file); // add file
await env.BUCKET.get(key); // retrieve file
await env.BUCKET.delete(key); // delete file
await env.BUCKET.list(); // list all files
```

## Add Custom Color

Add a custom color to the `globals.css` file.

```css
@theme inline {
  --color-custom: var(--color-custom); /* Change "custom" to your desired color name */
}

:root {
  --color-custom: #ffce00; /* Custom color */
}
```

Use the custom color in your components.

```tsx
<h1 className="text-custom-color">Custom Color</h1>
```

## Add Custom Font

### Using Google Fonts

Import any font from Google Fonts using the `next/font/google` package in the `layout.tsx` file.

```tsx
import { Geist, Geist_Mono } from "next/font/google";

const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
});

const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});
```

Add the font variables to the `html` tag.

```tsx
<html lang="en" className={`${geistSans.variable} ${geistMono.variable} antialiased`}>
  {/* ... */}
</html>
```

Update the `globals.css` file to include the font variables.

```css
@theme inline {
  --font-sans: var(--font-geist-sans);
  --font-mono: var(--font-geist-mono);
}
```

Use the font variables in your components.

```tsx
<h1 className="font-sans">Custom Font</h1>
```

### Using Local Fonts

Add a font and its variants (in ttf format) to the `public/assets/fonts` folder.

Example:

```
/public/assets/fonts/
InstrumentSerif/
  /InstrumentSerif-Italic.ttf
  /InstrumentSerif-Regular.ttf
```

Add the font to the "layout.tsx" file.

```tsx
import localFont from "next/font/local";

const instrumentSerif = localFont({
  src: [
    {
      path: "../../public/assets/fonts/InstrumentSerif/InstrumentSerif-Regular.ttf",
      weight: "400",
      style: "normal",
    },
    {
      path: "../../public/assets/fonts/InstrumentSerif/InstrumentSerif-Italic.ttf",
      weight: "400",
      style: "italic",
    },
  ],
  variable: "--font-instrument-serif",
});
```

Add the font variable to the `html` tag.

```tsx
<html lang="en" className={`${instrumentSerif.variable} antialiased`}>
  {/* ... */}
</html>
```

Update the `globals.css` file to include the font variable.

```css
@theme inline {
  --font-serif: var(--font-instrument-serif);
}
```

Use the font variable in your components.

```tsx
<h1 className="font-serif">Custom Font</h1>
```

## Adding Metadata

### Changing favicon

Convert your favicon to a ico format and add it to the `src/app` folder. (Use [this tool](https://svg-to-png-jpeg-favicon.vercel.app/) to generate an ico file.)

```
/src/app/
favicon.ico
```

### Adding OG Image

Add an OG image to the `public/assets` folder in 1200x630px size, PNG format, and named `og-image.png`.

```
/public/assets/
og-image.png
```

### Changing metadata

Edit the `metadata.ts` file.

```ts
export const metadata: Metadata = {
  // Edit with your own metadata
  title: "Cloudflare Next.js Template",
  description: "A template for building a website with Cloudflare Next.js",
  keywords: ["cloudflare", "next.js", "template"],
  robots: "index, follow",
  metadataBase: new URL("https://cloudflare-nextjs-template.com"),
  // ...
};
```

## Add Shadcn Components

```bash
pnpm dlx shadcn@latest add
```

Select the components you'd like to add by pressing "Space" to select/deselect or "a" to select all.

## Deploy on Cloudflare

```bash
pnpm run deploy
```
