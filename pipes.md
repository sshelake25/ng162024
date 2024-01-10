https://angular.io/guide/pipe-template

In Angular, pipes are used for transforming data within templates. Angular distinguishes between pure and impure pipes based on their behavior and performance characteristics.

### Pure Pipes:

Pure pipes are called only when Angular detects a change in the input value passed to the pipe. If the input value remains unchanged, Angular reuses the previous result, avoiding unnecessary recalculations.

Here's an example of a pure pipe:

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'double',
  pure: true,
})
export class DoublePipe implements PipeTransform {
  transform(value: number): number {
    console.log('Pure Pipe called');
    return value * 2;
  }
}
```

And in your template:

```html
<p>{{ 5 | double }}</p>
```

In this example, the `DoublePipe` is a pure pipe that doubles the input value. If the input value changes, the pipe is recalculated; otherwise, it reuses the previous result.

### Impure Pipes:

Impure pipes, on the other hand, are called on every change detection cycle, regardless of whether the input has changed or not. This can lead to decreased performance, as impure pipes may be executed more frequently than necessary.

Here's an example of an impure pipe:

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'double',
  pure: false,
})
export class DoublePipe implements PipeTransform {
  transform(value: number): number {
    console.log('Impure Pipe called');
    return value * 2;
  }
}
```

And in your template:

```html
<p>{{ 5 | double }}</p>
```

In this example, the `DoublePipe` is marked as impure with `pure: false`. This means it will be called on every change detection cycle, even if the input value hasn't changed.

### Performance Considerations:

- **Pure Pipes:**
  - Recommended for most use cases.
  - More efficient as they are only recalculated when the input changes.
  - Use them when the output of the pipe is solely dependent on the input values.

- **Impure Pipes:**
  - Use with caution as they may have performance implications.
  - Useful when the pipe has external dependencies or its result depends on factors other than the input values.

### Summary:

Choose between pure and impure pipes based on your specific use case and performance requirements. Pure pipes are generally more efficient, but impure pipes can be useful in scenarios where constant recalculation is necessary.

It's important to note that, starting from Angular version 4.3, all pipes are considered impure by default if their `pure` property is not explicitly set to `true`. Ensure that you explicitly set the `pure` property when creating custom pipes to avoid unexpected behavior.
