# **Personal NOTES from NEXTJS Tutorial**

<div id="topoftoc">

# Table of Contents
1. [Creating new project](#chapter1)
2. [CSS Styling](#chapter2)
3. [Optimizing Fonts and Images](#chapter3)
4. [Creating Layouts and Pages](#chapter4)
5. [Navigating between Pages](#chapter5)
6. [Setting Up your Database](#chapter6)
7. [Fetching Data](#chapter7)
8. [Static and Dynamic Rendering](#chapter8)
9. [Streaming](#chapter9)
10. [Partial Prerendering](#chapter10)
11. [Adding Search and Pagination](#chapter11)
12. [Mutating Data](#chapter12)
13. [Handling Errors](#chapter13)

<br/>

***

<br/>

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

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

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

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

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

```ts
import Image from "next/image";
```

```ts
  <Image
    src="/hero-desktop.png"
    width={1000}
    height={760}
    className="hidden md:block"
    alt="Screenshots of the dashboard project showing desktop version"
  />
```

```ts 
  <Image
    src="/hero-mobile.png"
    width={560}
    height={620}
    className="block md:hidden"
    alt="Screenshots of the dashboard prjoject showing mobile version"
  />
```
<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter4" />

# 4. Creating Layouts and Pages
Topics we’ll cover:

* Create the `dashboard` routes using file-system routing.
* Understand the role of folders and files when creating new route segments.
* Create a nested layout that can be shared between multiple dashboard pages.
* Understand what colocation, partial rendering, and the root layout are.

## Nested routing

Next.js uses file-system routing where **folders** are used to create nested routes. **Each folder represents a route segment** that maps to a URL segment. 

![Schema of folders representation in URL Segment](/public/docs/folders-to-url-segments.avif)

You can create separate UIs for each route using `layout.tsx` and `page.tsx` files. 

In addition, `page.tsx` is a **special Next.js file** that exports a React component, and **it's required for the route to be accessible**. For example, `/app/dashboard/page.tsx` is associated with the `/dashboard` path.

## Creating the dashboard layout

Next.js, you can use a special `layout.tsx` file to create UI that is shared between multiple pages. In our case, we create a `/dashboard` folder and we create inside a layout.tsx file. 
We import the `<SideNav />` component into the layout. It'll be a part of layout. On the other part, we import a children prop. This child can either be a page or another layout. 

One of benefits of using layouts on Next.js is that on navigation, **only the page components update while the layout won't re-render**. This is called [partial rendering](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering). Without partial rendering, each navigation would cause the full page to re-render on the client. Rendering only the segment that changes reduces the amount of data transferred and execution time, leading to improved performance.

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter5" />

# 5. Navigating between Pages
Topics we’ll cover:

* How to use the `next/link` component.
* How to show an active link with the `usePathname()` hook.
* How navigation works in Next.js.

## The `<Link>` component

In Next.js, you can use the `<Link />` Component to link between pages in your application. `<Link>` allows you to do [client-side navigation](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#how-routing-and-navigation-works) between routes. You should be able to navigate between the pages without seeing a full refresh. 

You can import the `Link` component from `next/link`.

## Automatic code-splitting and prefetching

To improve the navigation experience, Next.js automatically code splits your application by route segments.

Splitting code by routes means that pages become isolated which makes your application faster.

In production environment, Next.js automatically prefetches the code for the linked route in the background. By the time the user clicks the link, the code for the destination page will already be loaded in the background, and this is what makes the page transition near-instant.

## Pattern: Showing active links

A common UI pattern is to show an active link to indicate to the user what page they are currently on. 

To do this, you need to get the user's current path from the URL. Next.js provides a hook called [usePathname()](https://nextjs.org/docs/app/api-reference/functions/use-pathname) that you can use to check the path and implement this pattern.

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter6" />

# 6. Setting Up your Database

In this chapter, we could learn how to push our project to GitHub and create a Vercel account. 

Further more details here, in the [tutorial](https://nextjs.org/learn/dashboard-app/setting-up-your-database). :smile:

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter7" />

# 7. Fetching Data
Topics we'll cover:

* Learn about some approaches to fetching data: APIs, ORMs, SQL, etc.
* How Server Components can help you access back-end resources more securely.
* What network waterfalls are.
* How to implement parallel data fetching using a JavaScript Pattern.

## Choosing how to fetch data

### API layer

APIs are an intermediary layer between your application code and database. You'd need an API in cases like fetching data from the client, to avoid exposing your DB secrets to the client.

In Next.js, you can create API endpoints using [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers).

### Database queries

When you're creating a full-stack application, you'll also need to write logic to interact with your database. For relational databases as PostgreSQL, you can use SQL or ORM. You can use this when you fetch data on the server by **React Server Components**.

### Using Server Components to fetch data

By default, Next.js applications use [**React Server Components**](https://react.dev/reference/rsc/server-components).

There are a few benefits to using them, like:

* **Server Components** support JavaScript Promises, providing a solution for asynchronous tasks like data fetching natively. You can use **`async/await`** syntax without needing `useEffect`, `useState` or other data fetching libraries.
* Server Components run on the server, so you can keep expensive data fetches and logic on the server, only sending the result to the client.
* Since Server Components run on the server, you can query the database directly without an additional API layer. 

### Using SQL

In terms of fetching data, SQL allows you to write targeted queries to fetch and manipulate specific data. You can call `sql` anywhere on the server, like a Server Component.

## What are request waterfalls?

A **_waterfall_** refers to a sequence of network requests that depend on the completion of previous requests. In the case of data fetching, each request can only begin once the previous request has returned data. This behavior can also be unintentional and impact performance.

## Parallel data fetching

A common way to avoid waterfalls is to initiate all data requests at the same time - in parallel.

In JavaScript, you can use the [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) or [`Promise.allSettled()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled) functions to initiate all promises at the same time. 

By using this pattern, you can:

- Start executing all data fetches at the same time, which is faster than waiting for each request to complete in a waterfall.
- Use a native JavaScript pattern that can be applied to any library or framework.

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter8" />

# 8. Static and Dynamic Rendering
