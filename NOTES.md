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

```ts 
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
```ts 
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

Topics we'll cover:

* What static rendering is and how it can improve your application's performance.
* What dynamic rendering is and when to use it.
* Different approaches to make your dashboard dynamic.
* Simulate a slow data fetch to see what happens.

## What is Static Rendering?

With **Static Rendering, <u>data fetching and rendering happens on the server at build time</u>** (when you deploy) or when revalidating data. There are some benefits of static rendering:

1. **Faster Websites**. This ensures that users can access your website's content more quickly and reliably because there are prerender content and can distribute globally.
2. **Reduced Server Load**. Because the content is cached, your server does not have to generate content for each user request. 
3. **SEO**. Prerendered content is easier for search engines.

It's useful for UI with **no data** or **data that is shared across users**, such as a static blog post or product page. In opposite, static rendering is not a good fit for showing
the lastest changes in your dashboard because the application will not reflect the latest changes. 

## What is Dynamic Rendering?

With **Dynamic Rendering, <u>content is rendered on the server for each user at request time</u>** (when the user visits the page). There are some benefits of dynamic rendering:

* **Real-Time Data**. Dynamic rendering allows your application to display real-time or frequently updated data. This is ideal for applications where data changes often.
* **User-Specific Content**. It's easier to serve personalized content, such as dashboards or user profiles, and update the data based on user interaction.
* **Request Time Information**. Dynamic rendering allows you to access information that can only be known at request time, such as cookies or the URL search parameters.

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter9" />

# 9. Streaming
Topics we'll cover:

* What streaming is and when you might use it.
* How to implement streaming with `loading.tsx` and Suspense.
* What loading skeletons are.
* What Next.js Route Groups are, and when you might use them.
* Where to place React Suspense boundaries in your application.

## What is Streaming?

**Streaming** is a data transfer technique that allows you to break down a route into smaller *chunks* and progressively stream them from the server to the client as they become ready.

You can prevent slow data requests from blocking your whole page. This allows the user to see and interact with parts of the page without waiting for all the data to load before any UI 
can be shown to the user. Works perfect with React's component model because each component can be considered as a chunk.

There are two ways you implement streaming in Next.js:

1. At the page level, with the `loading.tsx` file (which creates `<Suspense>` for you).
2. At the component level, with `<Suspense>` for more granular control.

## Streaming a whole page with `loading.tsx`

`loading.tsx` is a special Next.js file built on top of React Suspense. It allows you to create fallback UI to show as a replacement while page content loads.

## Adding loading skeletons

A loading skeleton is a simplified version of the UI. Many websites use them as a placeholder (or fallback) to indicate to users that the content is loading.

## Route Groups

A [**Route Groups**](https://nextjs.org/docs/app/building-your-application/routing/route-groups), allow you to organize files into logical groups without affecting the URL path structure. When you create a new folder using parentheses `()`, the name won't be included in the URL path. So `/dashboard/(overview)/page.tsx` becomes `/dashboard`. You need move your `loading.tsx` and `page.tsx` inside. 

You're using a route group to ensure `loading.tsx` only applies to your dashboard overview page. However, you can also use route groups to separate your application into sections (e.g. `(marketing)` routes and `(shop)` routes) or by teams for larger applications.

## Streaming a component

We can also be more granular and stream specific components using **React Suspense**.

`<Suspense>` allows you to defer rendering parts of your application until some condition is met (e.g. data is loaded). You can wrap your dynamic components in Suspense. Then, pass it a fallback component to show while the dynamic component loads.

## Deciding where to place your Suspense boundaries

Where you place your Suspense boundaries will depend on a few things:

1. How you want the user to experience the page as it streams.
2. What content you want to prioritize.
3. If the components rely on data fetching.

Where you place your suspense boundaries will vary depending on your application. In general, it's good practice to move your data fetches down to the components that need it, and then wrap those components in Suspense. But there is nothing wrong with streaming the sections or the whole page if that's what your application needs.

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter10" />

# 10. Partial Prerendering (PPR)
Topics we'll cover:

* What Partial Prerendering is.
* How Partial Prerendering works.

**Partial Prerendering (PPR)** is an experimental feature introduced in Next.js 14 and new rendering model that allows you to combine the benefits of static and dynamic rendering in the same route. 

## How does Partial Prerendering work?

PPR uses React's [`Suspense`](https://react.dev/reference/react/Suspense) to defer rendering parts of your application until some condition is met (e.g. data is loaded).

The Suspense fallback is embedded into the initial HTML file along with the static content. At build time (or during revalidation), the static content is **prerendered** to create a static shell. The rendering of dynamic content is **postponed** until the user requests the route.

Wrapping a component in Suspense doesn't make the component itself dynamic, but rather <u>Suspense is used as a boundary between your static and dynamic code</u>.

<p align="right"><a href=#topoftoc>Top</a> :arrow_up:</p>

<div id="chapter11" />

# 11. Adding Search and Pagination
Topics we'll cover:

* Learn how to use the Next.js APIs: `useSearchParams`, `usePathname`, and `useRouter`.
* Implement search and pagination using URL search params. 

## Why use URL search params?

You'll be using URL search params to manage the search state. 

There are some benefits to implement search with URL params:

1. **Bookmarkable and shareable URLs**. User can bookmark the current state of the application, including queries and filters, for future reference or sharing.
2. **Server-side rendering**. URL params can be directly consumed on the server to render the initial state.
3. **Analytics and tracking**. Having search queries and filters directly in the URL makes it easier to track user behaviour without requiring additional client-side logic. 

## Adding the search functionality

There are the Next.js client hooks that you'll use to implement the search funcionality:

* **`useSearchParams`** - Allows you to access the parameters of the current URL. Returning an object with the params and his values. E.g: { page: 1, query: 'pending' }
* **`usePathname`** - Lets you read the current URL's pathname.
* [**`useRouter`**](https://nextjs.org/docs/app/api-reference/functions/use-router#userouter) - Enables navigation between routes within client components programmatically.

Immediately, you'll see the steps to implement it. Further detail on the tutorial:

### Capture the user's input

In our example, you'll notice to use `_"use client"_`. This is a Client Component, **which means you can use event listeners and hooks**. We create a function to catch the changes when
the input value changes. 

```ts
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
 
export default function Search({ placeholder }: { placeholder: string }) {
 `function handleSearch(term: string) {
    console.log(term);
  }`
 
  return (
    <div className="relative flex flex-1 flex-shrink-0">
      <label htmlFor="search" className="sr-only">
        Search
      </label>
      <input
        className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
        placeholder={placeholder}
       `onChange={(e) => {
          handleSearch(e.target.value);
        }}`
      />
      <MagnifyingGlassIcon className="absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
    </div>
  );
}
```

### Update the URL with the search params

You use the `useSearchParams` hook from `next/navigation` and assign it to a variable. Inside the function above, we create a new variable with `URLSearchParams` instance.

`URLSearchParams` is a Web API that provides utility methods for manipulating the URL query parameters. Instead of creating a complex string literal, you can use it to get the params string like 
`?page=1&query=a`. Now, you can use Next.js's `useRouter` and `usePathname` hooks to update the URL. `useRouter` has a method `replace` to replace the pathname of URL.

### Keeping the URL 