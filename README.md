# vue-demo
Demonstrates how to use vue js

The purpose of this repository is to demonstrate the features of the vue js framework.  It's intended audience are those that have a background in front end frameworks or javascript and are wanting to learn vue js.  In an effort to make the learning process easier, the app is coded in a fashion to make demonstrate how the internal wiring of the framework works as opposed to apps that provide some business need.

Installation
1) Create a vue scaffolding project and select the desired configuration options.

```
[lambda@localhost vue-demo]$ npm create vue@latest
Need to install the following packages:
  create-vue@3.10.3
Ok to proceed? (y) y

Vue.js - The Progressive JavaScript Framework

✔ Project name: … vue-demo
✔ Add TypeScript? … No / Yes
✔ Add JSX Support? … No / Yes
✔ Add Vue Router for Single Page Application development? … No / Yes
✔ Add Pinia for state management? … No / Yes
✔ Add Vitest for Unit Testing? … No / Yes
✔ Add an End-to-End Testing Solution? › Nightwatch
✔ Add ESLint for code quality? … No / Yes
✔ Add Prettier for code formatting? … No / Yes
✔ Add Vue DevTools 7 extension for debugging? (experimental) … No / Yes

Scaffolding project in /home/lambda/repos/vue-demo/vue-demo...

Done. Now run:

  cd vue-demo
  npm install
  npm run format
  npm run dev

[lambda@localhost vue-demo]$ 
```

Things to Note After Installation About `vue-demo/src/main.ts` 
1) New application instance is created where the `App` parameter is the root component (i.e. html page).
   `const app = createApp(App)`.  Things to note about the `App.vue` are as follows:
   a) `import { RouterLink, RouterView } from 'vue-router'` - lets us use `RouterLink` which enables the url in the browser to be updated.
   b) `import HelloWorld from './components/HelloWorld.vue'` - Lets us use the `HelloWorld` component with `msg` passed to it.
2) Configures the use storage and a router via `app.use(createPinia())` and `app.use(router)`, respectively
3) Mounts the app to `<div id="app"></div>` DOM element found in `vue-demo/index.hml` via `app.mount('#app')`

Concepts
1) **Data Binding via Text Interpolation** (called mustaches and cannot be used inside html attributes)
   `vue-demo/vue-demo/src/App.vue` -> `<HelloWorld msg="You did it!" />` gets mapped to `HelloWorld` component in `vue-demo/vue-demo/src/components/HelloWorld.vue` -> `<h1 class="green">{{ msg }}</h1>` and rendered to the screen as
   
![image](https://github.com/user4924/vue-demo/assets/25931927/6bb24018-ed0f-4c6a-9e18-ca8a0257a5ee)

`{{ 1+2 }}` - JS expression evaluation

2) `v-html` attributes which are **directives that apply special reactive behavior to the rendered DOM**
   `<span v-html="rawHtml">Blue font</span>` would render in the DOM as `<span style="color: blue">Blue font</span>` , assuming `rawHtml` had value of `style="color: blue`

3) **Attribute Data Binding via Directive** - tells vue to keep `id` in sync with component's `dynamicId` (attribute removed is `null` or `undefined`; if attribute's rhs is boolean, it will also be included for an empty string, in addition to being included for truthy values)
   
   `<div v-bind:id="dynamicId"></div>` or shorthand `<div v-bind:id="dynamicId"></div>`
   
   `<div :id></div>` or shorthand for same name binding `<div v-bind:id></div>`

   ``<div :id="`list-${id}`"></div>`` (JS expression evaluation inside attribute data binding)

    binding functions (called everytime component updates => should be pure)

   ```
      <time :title="toTitleDate(date)" :datetime="date">
          {{ formatDate(date) }}
      </time>
    ```
      
4) **Dynmically binding multiple attributes**
    ```
    const objectOfAttrs = {
      id: 'container',
      class: 'wrapper'
   }
   <div v-bind="objectOfAttrs"></div>
    ```
5) Note that template expressions are sandboxed and have access to restricted list of globals.  Additional globals can be specified in `app.config.globalProperties`
6) Other directives
    - `<a v-on:click="doSomething"> click me </a>` or `<a @click="doSomething"> click me</a>`
7) **Dynamic Arguments** - value of null removes binding.  Note that in-DOM templates (templates in separate file), attributes in component must be in lowercase because they get converted in templates that way (i.e. they must match to bind).  attributes in SFC (single file components) do not.

    `<a :[attributeName]="url"> ... </a>` where `attributeName="href"` gets evalued to `<a :hfref="url"> ... </a>`

8) **Modifiers** - way on saying `preventDefault` on submit
    `<form @submit.prevent="onSubmit">...</form>`
9) Reactive State using refs (needed due to Vue's reactive system inner workings - DOM variable object access tracked via get on the attribute and on set it knows to refresh the DOM).  Can also pass refs into funciton while mainting reactivity state and access to latest value
    METHOD 1: update count via method inside component
   ```
      import { ref } from 'vue'
      
      export default {
        setup() {
          const count = ref(0)
      
          function increment() {
            // .value is needed in JavaScript
            count.value++
          }
      
          // don't forget to expose the function as well.
          return {
            count,
            increment
          }
        }
      }
      
      <button @click="increment">
        {{ count }}
      </button>
    ```

   METHOD 2: update count via ++ inside template
   ```
    import { ref } from 'vue'
    
    export default {
      // composition api hook
      setup() {
        const count = ref(0) //turns count into object with value attr
    
        // expose the ref to the template
        return {
          count 
        }
      }
    }

   <button @click="count++">
      {{ count }}     //no need to do count.value b/c done implicitly
    </button>   
    ```

      METHOD 3: update count by via SFC that exposes count and increment
   ```
      <script setup> // imports, vars, func are automatically available in template
      import { ref } from 'vue'
      
      const count = ref(0)
      
      function increment() {
        count.value++
      }
      </script>
      
      <template>
        <button @click="increment">
          {{ count }}
        </button>
      </template>
    ```

   NOTE: refs can also hold nested objects and track deeply (accessed properly via obj1.attr1-1.attr2-2 like immer).  Also shallow refs are possible where only values are checked.
   
11) **DOM updates not made synchronously** - updates on ticks

    ```
      import { nextTick } from 'vue'
      
      async function increment() {
        count.value++
        await nextTick()
        // Now the DOM is updated
      }
    ```
12)  **reactive limitations** (that's why ref is recommended)
      - all properties are tracked, not just value; reactive objects are proxies and only updating the proxy triggers updates
      - works only with objects, arrays, colelctions types (maps) and NOT primitive types
      - can't replace enitre reactivity tracked object cause original is lost
        ```
        let state = reactive({ count: 0 })

        // the above reference ({ count: 0 }) is no longer being tracked
        // (reactivity connection is lost!)
        state = reactive({ count: 1 })
        ```
      - not detstructing friendly
        ```
        const state = reactive({ count: 0 })
        
        // count is disconnected from state.count when destructured.
        let { count } = state
        // does not affect original state
        count++
        
        // the function receives a plain number and
        // won't be able to track changes to state.count
        // we have to pass the entire object in to retain reactivity
        callSomeFunction(state.count)
        ```
14)  Reactive and ref interactions 
    - accessing reactive wrapped ref
15)  At this point (Additional Ref Unwrapping Details located at https://vuejs.org/guide/essentials/reactivity-fundamentals.html#additional-ref-unwrapping-details ), I started coding the topics and combined the notes in the code.  








