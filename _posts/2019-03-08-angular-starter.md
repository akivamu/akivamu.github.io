---
layout: post
title: Angular starter
tags:
- JavaScript
---


This is a first look at an Angular app.  
Taking the reference code from official tutorial: [Tour of Heroes](https://angular.io/tutorial)

# Application

## `main.ts`

This is entrypoint to our app. There are some important lines:

### Environment

```typescript
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}
```

There are 2 env files: `environment.ts` and `environment.prod.ts`

- If we run by cmd `ng serve`, it will use `environment.ts`
- If we build by cmd `ng build --prod`, it will use `environment.prod.ts`

This replacement is defined in `angular.json`, search for `fileReplacements`

### Bootstrapping the root module

Should not be confused with JS modules: [https://angular.io/guide/ngmodule-vs-jsmodule](https://angular.io/guide/ngmodule-vs-jsmodule)

From now, **module** means **Angular module**

The app can contains multiple modules, the entrypoint code (`main.ts`) will bootstrap the
root module.

This is root module: `AppModule`, defined in `app.module.ts`

# Angular modules

Let look at the root modules: `app.module.ts`

## Decorator `@NgModule`

Detail about JS decorator:

- [https://ultimatecourses.com/blog/angular-decorators](https://ultimatecourses.com/blog/angular-decorators)
- [https://www.typescriptlang.org/docs/handbook/decorators.html](https://www.typescriptlang.org/docs/handbook/decorators.html)

We can see the code apply decorator `@NgModule` to the class `AppModule`, which effectively
marks the class `AppModule` as an Angular module - we are telling Angular that we have a module
with has some configurations (passed as parameters to the decorator call)

The same applies to method, property decorators.

## Bootstrapping the root component

Look at the decorator of root module: `AppModule`, we see the bootstrap option:

```typescript
bootstrap: [ AppComponent ]
```

This is the **Angular component**, this is the root component that Angular will create and
insert into `index.html`. Component defines views.

So, for the bootstrapping, it should go like this:

**Entrypoint -> Root module -> Root component**

# Angular components

Look at the `app.component.ts`, we use decorator `@Component` to tell Angular that the class
`AppComponent` is a component.

```typescript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
```

These are our component configurations: DOM element it belong to, template and styles.

Take a look at the html template, we can see the principal of component, similar to other web
UI framework.

## Import the components

Looking at the template `app.component.html`, there is a line:

```html
<app-messages></app-messages>
```

We can see the `AppComponent` uses an external component, denoted by the tag `<app-messages>`.

That component is defined in `messages.component.ts`, as `selector: 'app-messages'`

This is the Angular naming convention, parent refers to child by selector.

## Scope of components

Components in same module can reference/import each other.  
So each module should declare all the components in it, see the decorate `@NgModule` configuration
for `declarations`

More details: [understanding-angular-modules-ngmodule-and-their-scopes](https://medium.com/@cyrilletuzi/understanding-angular-modules-ngmodule-and-their-scopes-81e4ed6f7407)

# Routing

Looking at the root component `app.component.html` again, we see the routing related stuffs:

```html
<nav>
  <a routerLink="/dashboard">Dashboard</a>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
```

The directives `<router-outlet>` and `routerLink` are imported from `AppRoutingModule` which imports
Angular `RouterModule`.

The `<router-outlet>` presents the container for our routes, which display specific component
based on the current URL. See `app-routing.module.ts` for list of routes.

# Services

In common sense, services will do the logic stuffs, unrelated to the UI.  
In the simplest way, we can just import service as a JS module and use it.  

But in Angular, we use Dependencies Injection.

We declare a class with decorator `@Injectable`, registered it to DI system in `hero.service.ts`

```typescript
@Injectable({ providedIn: 'root' })
export class HeroService {}
```

And then, in the component, we inject the service:

```typescript
import { HeroService } from '../hero.service';

constructor(private heroService: HeroService) { }
```

# RxJS

Angular makes use of observables for async operations.

For Reactive Programming, read this awesome article for a good start: [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
