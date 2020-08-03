+++
title = "Vue Navigation Guards"
date = "2020-07-15"
author = "Kareem" 
authorTwitter = "kareemdagg" #do not include @
cover = "https://miro.medium.com/max/3920/1*oZqGznbYXJfBlvGp5gQlYQ.jpeg" #cover image to show for this post
tags = ["vue.js", "javascript", "frontend"] #what tags (in the tags page) to list this post under
keywords = ["vue.js", "javascript", "frontend"] #keywords to set for SEO
description = "Navigation guards (via `vue-router`) provide a way to control navigation through our routes either by redirecting it or canceling it. This has a number of practical applications. This short post looks into the different ways we can hook into route navigation, as well as some of each method's possible drawbacks" #text that shows in the post list view (if showFullContent) is false. Also set as description for SEO
showFullContent = false #whether to show all post content in list view (overrides description field if true)
weight = 0 #priority to set post in list and search views. 0 is default priority, 1 pins post, lower weight = higher priority. 
+++

- [About](#about)
- [Approaches](#approaches)
  - [Global](#global)
  - [Per-route](#per-route)
  - [In-component](#in-component)
- [Navigation Resolution flow](#navigation-resolution-flow)

# About
---
Navigation guards (via `vue-router`) provide a way to control navigation through our routes either by redirecting it or canceling it. This has a number of practical applications. For instance authenticating that only authorized users have access to a certain page of your site, or personalizing experiences and only loading specific content for certain users.   

There are a number of ways to hook into the route navigation process: _globally_, _per-route_, or _in-component_.

- **global**
  - `beforeEach`: action before entering any route (no access to this scope)
  - `beforeResolve`: action before the navigation is confirmed, but after in-component guards (same as beforeEach with this scope access)
  - `afterEach`: action after the route resolves (cannot affect navigation)
- **per-route**
  - `beforeEnter`: action before entering a specific route (unlike global guards, this has access to this)
- **in-component**
  - `beforeRouteEnter`: action before navigation is confirmed, and before component creation (no access to this)
  - `beforeRouteUpdate`: action after a new route has been called that uses the same component
  - `beforeRouteLeave`: action before leaving a route

**NOTE:**
1. Params or query changes won’t trigger enter/leave navigation guards. You can either watch the $route object to react to those changes, or use the beforeRouteUpdate in-component guard.
2. Make sure to always call the next function, otherwise the hook will never be resolved.

# Approaches
---

## Global

```js
const router = new VueRouter({ ... })

// Before Guards
router.beforeEach((to, from, next) => {
  // ...
})

// Resolve Guards
// beforeResolve guards will be called right before the navigation is confirmed
// after all in-component guards and async route components are resolved
router.beforeResolve((to, from, next) => {
  // ...
})

// After Hooks
router.afterEach((to, from) => {
  // ...
})
```

## Per-route

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // perform an action before entering the route '/foo'
        // has access to the `this` component instance
        // ...
      }
    }
  ]
})
```

## In-component

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // called before the route that renders this component is confirmed.
    // does NOT have access to `this` component instance,
    // because it has not been created yet when this guard is called!
    // However, you can access the instance by passing a callback to next. 
    // The callback will be called when the navigation is confirmed
    // and the component instance will be passed to the callback as the argument
    beforeRouteEnter (to, from, next) {
      next(vm => {
        // access to component instance via `vm`
      })
    }
  },
  beforeRouteUpdate (to, from, next) {
    // called when the route that renders this component has changed,
    // but this component is reused in the new route.
    // For example, for a route with dynamic params `/foo/:id`, when we
    // navigate between `/foo/1` and `/foo/2`, the same `Foo` component instance
    // will be reused, and this hook will be called when that happens.
    // has access to `this` component instance.
  },
  beforeRouteLeave (to, from, next) {
    // called when the route that renders this component is about to
    // be navigated away from.
    // has access to `this` component instance.
  }
}
```

# Navigation Resolution flow
---

1. Navigation triggered.
2. Call leave guards in deactivated components.
3. Call global beforeEach guards.
4. Call beforeRouteUpdate guards in reused components.
5. Call beforeEnter in route configs.
6. Resolve async route components.
7. Call beforeRouteEnter in activated components.
8. Call global beforeResolve guards.
9. Navigation confirmed.
10. Call global afterEach hooks.
11. DOM updates triggered.
12. Call callbacks passed to next in beforeRouteEnter guards with instantiated instances.
