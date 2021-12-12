# Reusable web components with Svelte
## Why Svelte
One of the most popular frontend libraries at the moment is React.js but it is not very usable for web components. The main reason is that as any other library you need to add all its dependancies to your project/component and react.js and react-dom.js with any web component no mather how small it is you will have attached like 150kb of dependancies and here is where Svelte comes in

Writing web components the hardcore way, pure JS is a bit of a pain. You need to write your component in pure JS with element.innerHTML and than all your code is wraped in template literals and you lose any autocomplete, suggestion, emmet or similar coding help that you usually have, and for complex components it would be very hard to write it and maintain later.

Example of a pure JS web component:
```
template.innerHTML = `
  <style>
    h3 {
      color: coral;
    }
  </style>

  <div class="user-card">
    <img />
    <div>
      <h3></h3>
      <div class="info">
        <p><slot name="email"/></p>
        <p>PHONE</p>
      </div>
      <button id="toggle-info">Hide Info</button>
    </div>
  </div>
`;
```

## Pros of Svelte
1. ### Bundle size
As we said, Svelte is a compiler so all your code in the end will be optimized, minimised and also all the unused scss will be deleted in the build.
Svelte also has a lot less boilerplate code or 8 lines block of code with array destructuring to update one variable like it is the case of React but the user experience and ease of use is subjective so I will just leave a meme here :D 

![svelereact](https://user-images.githubusercontent.com/26542107/145724696-ea4abbed-200a-40fe-8c00-c31a155a98b6.jpg)

2. ### You can use SCSS that is minified and unused code is deleted from the build

3. ### Easy props and state definition and usage
In svelte all the props at their core are variables and to define a simple city prop with an initial value of 'Pula' for example, is simply `export let city = "Pula";`

4. ### Svelte await block
if we need to get data from some API and display it in some elements for example, we can use the svelte special await block that looks like:
```
{#await somePromise}
 before promise is resolved
{:than}
after promise is resolved
{/await
```

Example of fetching weather data and add it in a header:
```
  {#await getWeather(urlWeather)}
    <p>Loading...</p>
  {:then data}
    <header>
      <h1>{data.name}, {data.weather[0].description}</h1>
      <img src={initialData.weatherIconURL + data.weather[0].icon + initialData.weatherIconExtension} alt="" />
    </header>
  {/await}
```



