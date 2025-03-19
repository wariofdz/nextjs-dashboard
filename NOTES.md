# **NOTES FROM NEXTJS TUTORIAL**

# Table of Contents
1. [Creating new project](#chapter1)
2. [CSS Styling](#chapter2)
3. [Optimizing fonts and images](#chapter3)

<div id='chapter1'/>

# 1. Creating new project
Recommending use pnpm. To create a new project, we set on the folder that we’d like to keep our project and execute the command `create-next-app`:

```typescript 
npx create-next-app@latest nextjs-dashboard --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example" –use-pnpm
```

## Folder structure
The folder structure is:
* **_`/app`_**: Contains all the routes, components, and logic for your application, this is where you'll be mostly working from.
* **_`/app/lib`_**: Contains functions used in your application, such as reusable utility functions and data fetching functions.
* **_`/app/ui`_**: Contains all the UI components for your application, such as cards, tables, and forms. To save time, we've pre-styled these components for you.
* **_`/public`_**: Contains all the static assets for your application, such as images.
* **_`Config files`_**: You'll also notice config files such as next.config.ts at the root of your application. Most of these files are created and pre-configured when you start a new project using create-next-app. You will not need to modify them in this course.

## Typescript
**Tip**: We're manually declaring the data types, but for better type-safety, we recommend [`Prisma`](https://prisma.io) or [`Drizzle`](https://orm.drizzle.team/), which automatically generates types based on your database schema.

## Running development server
Execute on a terminal: 
```typescript 
pnpm run dev 
``` 
and Next.js runs development server on port **3000**.

<div id='chapter2'/>

# 2. CSS Styling 
Topics we’ll cover:

* How to add a global CSS file to your application.
* Two different ways of styling: Tailwind and CSS modules.
* How to conditionally add class names with the clsx utility package.

## Global styles
On **_`/app/ui`_** we find the **global.css**.

## Tailwind
[`Tailwind`](https://tailwindcss.com) is a CSS framework that speeds up the development process by allowing you to quickly write [`utility classes`](https://tailwindcss.com/docs/styling-with-utility-classes) directly in your React code. 

When you use `create-next-app` to start a new project, Next.js will ask if you want to use Tailwind. If you select _yes_, Next.js will automatically install the necessary packages and configure Tailwind in your application.

## CSS Modules
[`CSS Modules`](https://nextjs.org/docs/app/getting-started/css) allow you to scope CSS to a component by automatically creating unique class names, so you don't have to worry about style collisions as well. In the tutorial we use Tailwind.

## Using the `clsx` library to toggle class names

There may be cases where you may need to conditionally style an element based on state or some other condition. You put into the className tag.

```html
<span className={clsx('inline-flex items-center rounded-full px-2 py-1 text-sm', 
  { 'bg-gray-100 text-gray-500': status === 'pending', 
    'bg-green-500 text-white': status === 'paid', }, )} >
```

### Other styling solutions 
We could use Sass or CSS-in-JS libraries such as [styled-jsx](https://github.com/vercel/styled-jsx), [styled-components](https://github.com/vercel/next.js/tree/canary/examples/with-styled-components), and [emotion](https://github.com/vercel/next.js/tree/canary/examples/with-emotion).

<div id='chapter3'/>

# 3. Optimizing Fonts and Images
Topics we’ll cover:

* How to add custom fonts with `next/font`.
* How to add images with `next/image`.
* How fonts and images are optimized in Next.js.

## Why optimize fonts?

Next.js automatically optimizes fonts in the application when you use the `next/font` module. <u>**It downloads font files at build time and hosts them with your other static assets**</u>. This means when a user visits your application, there are no additional network requests for fonts which would impact performance.

## Why optimize images?

Next.js can serve static assets, like images, under the top-level `/public` folder. Files inside `/public` can be referenced in your application. With regular HTML you have to manually: 
* Ensure your image is responsive on different screen sizes.
* Specify image sizes for different devices.
* Prevent layout shift as the images load.
* Lazy load images that are outside the user's viewport.

Image Optimization is a large topic in web development that could be considered a specialization in itself. Instead of manually implementing these optimizations, you can use the `next/image` component to automatically optimize your images.

## The `<Image>` component

The `<Image>` Component is an extension of the HTML `<img>` tag, and comes with automatic image optimization, such as:

* Preventing layout shift automatically when images are loading.
* Resizing images to avoid shipping large images to devices with a smaller viewport.
* Lazy loading images by default (images load as they enter the viewport).
* Serving images in modern formats, like WebP and AVIF, when the browser supports it.

### Exercise: Adding the mobile and desktop hero image

Adding the `next/image` component to the `/app/page.tsx`:

```typescript
import Image from "next/image";
```

```html
  <Image
    src="/hero-desktop.png"
    width={1000}
    height={760}
    className="hidden md:block"
    alt="Screenshots of the dashboard project showing desktop version"
  />
```

```html 
  <Image
    src="/hero-mobile.png"
    width={560}
    height={620}
    className="block md:hidden"
    alt="Screenshots of the dashboard prjoject showing mobile version"
  />
```