# Switcher

Type-safe TypeScript-first minimalistic router for React & Preact apps.

**Why?**

- Designed with TypeScript's type inference in mind
- Universal code (browser & Node.js)
- Functional API
- Maximum type-safety
- Autocomplete for routes, params and meta information
- Say goodbye to `any`!
- Say goodbye to exceptions!

## Installation

The library is available as npm packages for [React](https://www.npmjs.com/package/@switcher/react) and [Preact](https://www.npmjs.com/package/@switcher/preact):

```sh
# For React
npm install @switcher/react --save
# Or using Yarn:
yarn add @switcher/react

# For Preact
npm install @switcher/preact --save
# Or using Yarn:
yarn add @switcher/preact
```

## Get started

To start using Switcher you need to add a module to your app, i.e. `router.ts`. There you going to initialize the router and export it's interface to your application.

Here's the router from [Fire Beast blog](https://firebeast.dev/) source code:

> Note that even though the triple call `route('home')('/')()` looks alien, it allows TypeScript to infer dictionary of route names (`home`, `tutorials`, etc.), optional meta information and let you specify the params type.

```ts
import { createRouter, InferRouteRef, route } from '@switcher/preact'

// Routes
export const appRoutes = [
  route('home')('/')(),

  route('tutorial')<{ slug: string }>('/tutorials/:slug')(),

  route('tutorial-chapter')<{ tutorialSlug: string; chapterSlug: string }>(
    '/tutorials/:tutorialSlug/chapters/:chapterSlug'
  )(),

  route('post')<{ categoryId: string; postId: string }>(
    '/:categoryId/:postId'
  )()
]

// Routing methods
export const {
  buildHref,
  useRouter,
  RouterContext,
  RouterLink,
  resolveLocation,
  refToLocation
} = createRouter(appRoutes)

// Type to use in prop definitions
export type AppRouteRef = InferRouteRef<typeof appRoutes>
```

Then you need to initialize the router context in your root component and pass the initial URL:

```tsx
import { h } from 'preact'
import { RouterContext, useRouter } from './router'

export default function UI() {
  const router = useRouter(location.href)

  return (
    <RouterContext.Provider value={router}>
      <Content />
    </RouterContext.Provider>
  )
}
```

Then you'll be able to access the router context in nested components and render the corresponding page:

```tsx
import { h } from 'preact'
import { useContext } from 'preact/hooks'
import { RouterContext } from './router'
import HomePage from './HomePage'
import PostPage from './PostPage'
import TutorialPage from './TutorialPage'
import TutorialChapterPage from './TutorialChapterPage'
import NotFoundPage from './NotFoundPage'

export default function Content() {
  const { location } = useContext(RouterContext)

  switch (location.name) {
    case 'home':
      return <HomePage />

    case 'post': {
      const { postId } = location.params
      return <PostPage postId={postId} />
    }

    case 'tutorial': {
      const { slug } = location.params
      return <TutorialPage slug={slug} />
    }

    case 'tutorial-chapter': {
      const { tutorialSlug, chapterSlug } = location.params
      return (
        <TutorialChapterPage
          tutorialSlug={tutorialSlug}
          chapterSlug={chapterSlug}
        />
      )
    }

    case '404':
    default:
      return <NotFoundPage />
  }
}
```

To navigate between the pages you could use `RouterLink` or `navigate` function from the router context:

```tsx
import { h } from 'preact'
import { RouterContext, RouterLink } from '#app/router'
import { signOut } from './auth'

export default function Navigation() {
  const { navigate } = useContext(RouterContext)

  return (
    <ul>
      <li>
        <RouterLink to={{ name: 'home' }}>Home</RouterLink>
      </li>

      <li>
        <button onClick={() => signOut().then(() => navigate({ to: 'home' }))}>
          Sign out
        </button>
      </li>
    </ul>
  )
}
```

## Changelog

See [the changelog](./CHANGELOG.md).

## License

[MIT © Sasha Koss](https://kossnocorp.mit-license.org/)
