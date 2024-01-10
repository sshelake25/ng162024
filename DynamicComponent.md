https://angular.io/guide/dynamic-component-loader

Dynamic component creation in Angular involves using `ComponentFactoryResolver` and `ViewContainerRef`. This allows you to dynamically create and insert components into the DOM at runtime. Here's a step-by-step example:

### 1. Create a Dynamic Component:

Let's say you have a dynamic component named `DynamicComponent`:

```typescript
// dynamic.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-dynamic',
  template: '<p>{{ message }}</p>',
})
export class DynamicComponent {
  @Input() message: string | undefined;
}
```

### 2. Create a Host Component:

Create a host component that will dynamically load the `DynamicComponent`:

```typescript
// host.component.ts
import { Component, ComponentFactoryResolver, ViewChild, ViewContainerRef } from '@angular/core';
import { DynamicComponent } from './dynamic.component';

@Component({
  selector: 'app-host',
  template: '<ng-container #dynamicContainer></ng-container>',
})
export class HostComponent {
  @ViewChild('dynamicContainer', { read: ViewContainerRef }) dynamicContainer: ViewContainerRef | undefined;

  constructor(private componentFactoryResolver: ComponentFactoryResolver) {}

  loadDynamicComponent(message: string): void {
    // Dynamically create the DynamicComponent
    const componentFactory = this.componentFactoryResolver.resolveComponentFactory(DynamicComponent);
    const componentRef = this.dynamicContainer?.createComponent(componentFactory);

    // Set input property on the dynamically created component
    if (componentRef) {
      componentRef.instance.message = message;
    }
  }
}
```

### 3. Update App Module:

Include both `DynamicComponent` and `HostComponent` in your module:

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { DynamicComponent } from './dynamic.component';
import { HostComponent } from './host.component';

@NgModule({
  declarations: [DynamicComponent, HostComponent],
  imports: [BrowserModule],
  bootstrap: [HostComponent],
})
export class AppModule {}
```

### 4. Use the Host Component in App Component:

Use the `HostComponent` in your main `AppComponent`:

```typescript
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: '<app-host></app-host>',
})
export class AppComponent {}
```

### 5. Run the Application:

```bash
ng serve
```

Visit `http://localhost:4200/` in your browser. The `DynamicComponent` should be dynamically loaded into the `HostComponent`.

### Explanation:

- The `DynamicComponent` is a simple component with an input property `message`.

- The `HostComponent` contains a `ViewContainerRef` (`dynamicContainer`) where the `DynamicComponent` will be dynamically inserted.

- In the `loadDynamicComponent` method of the `HostComponent`, we use `ComponentFactoryResolver` to resolve the `DynamicComponent` factory. We then create the component using `createComponent` and set its input properties.

- The main `AppComponent` uses the `HostComponent`, which, in turn, dynamically loads the `DynamicComponent`.

This example demonstrates the basics of dynamically creating and loading components in Angular. Depending on your use case, you might have more complex scenarios or dynamic component loading triggered by user interactions or other events.
