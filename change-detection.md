https://angular.io/guide/change-detection

The core principles of change detection in Angular are:

1. **Zone.js:**
   Angular relies on Zone.js to intercept asynchronous operations, such as event handling, HTTP requests, and timers. This allows Angular to run change detection and update the view when asynchronous operations complete.

2. **Change Detection Strategies:**
   Angular provides two change detection strategies:

   - **Default (NgZone):** This strategy triggers change detection for all components in the application when any asynchronous event occurs.
  
   - **OnPush:** This strategy makes a component check for changes only when its input properties change or when an event is triggered within the component. It optimizes performance by reducing the number of components checked during change detection.

3. **Immutable Data:**
   Angular prefers immutable data structures. When you change the reference of an object or array, Angular detects the change automatically. This is crucial for the OnPush change detection strategy.

4. **ngZone:**
   The NgZone service provides a way to explicitly run change detection. You can inject NgZone into a component and use it to run specific code outside or inside the Angular zone.
