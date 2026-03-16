Once you’ve gotten set up and done the tutorials, let’s do the following, in this order:

{{% steps %}}

1. Decide what your app’s **state** will look like. This is the minimum amount of data that needs to be maintained to represent what the app tracks at any given time. Define your initial state in your top-level component (e.g. App, or whatever you want to rename yours to), typically with hooks like `useState`. Consider which other components need their own local state. Minimize the complexity of state management, but make sure you make components as independent as possible so you don’t always rely on a single ‘master’ object. This is the basics of good O-O design.

2. Create **function components** to represent elements of your UI. Put each component in a separate file.

3. Next, make each component **return JSX**. Hard-code all your numbers first. Just get the JSX working so the site *looks* the same.

4. Next, pass the state from your top-level component to the **properties** of the child components. Make the hard-coded aspects of your UI dynamic based on the state/properties of the component. Again, aim for the UI to *look* the same way as you do this.

5. Finally, figure out your **event listeners**. The tic-tac-toe tutorial mentioned earlier is a useful reference on the proper way to pass around event listeners.

{{% /steps %}}