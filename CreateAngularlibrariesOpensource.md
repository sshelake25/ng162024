Creating open-source Angular libraries involves packaging and distributing reusable components or services that can be easily integrated into other Angular projects. Below is a step-by-step guide to developing an open-source Angular library along with an example.

### Step 1: Set Up Your Library Project

```bash
ng new my-awesome-library --create-application=false
cd my-awesome-library
ng generate library awesome-library
```

### Step 2: Implement a Simple Component

In your library project (`projects/awesome-library/src/lib`), create a simple Angular component:

```typescript
// projects/awesome-library/src/lib/awesome.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'lib-awesome',
  template: '<p>{{ message }}</p>',
})
export class AwesomeComponent {
  @Input() message: string | undefined;
}
```

### Step 3: Export the Component

Ensure that the component is exported from the library module:

```typescript
// projects/awesome-library/src/lib/awesome.module.ts
import { NgModule } from '@angular/core';
import { AwesomeComponent } from './awesome.component';

@NgModule({
  declarations: [AwesomeComponent],
  exports: [AwesomeComponent],
})
export class AwesomeModule {}
```

### Step 4: Build and Package the Library

Build the library to create the distributable package:

```bash
ng build awesome-library
```

### Step 5: Publish to npm

Ensure you have an npm account. If not, create one at [npmjs.com](https://www.npmjs.com/).

Login to your npm account using:

```bash
npm login
```

Navigate to the dist folder and publish your library:

```bash
cd dist/awesome-library
npm publish
```

### Step 6: Using the Published Library

Create a new Angular application and install your library:

```bash
ng new my-app
cd my-app
npm install awesome-library
```

Now, you can use your library in your Angular application:

```typescript
// src/app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AwesomeModule } from 'awesome-library';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AwesomeModule],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

And in your component:

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: '<lib-awesome message="Hello from Awesome Library"></lib-awesome>',
})
export class AppComponent {}
```

### Step 7: Share Your Library

Share the GitHub repository of your library so that other developers can contribute or use your library.

Congratulations! You've just created and published an open-source Angular library. This example demonstrates a simple process, and you can extend it by adding more components, services, documentation, tests, and other features based on your library's purpose.
