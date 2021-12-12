# Reusable web components with Svelte

## tldr summary
I tried creating a simple test weather widget that pulls data from openweathermap api with Svelte and modify it to be used as a web component. The end result is a JS file of ~14kb and you can see a live implementation hosted here [Link](https://cloudy-smart-metatarsal.glitch.me/).

End result

![image](https://user-images.githubusercontent.com/26542107/145725582-be27d310-3989-4609-a862-97eec6068896.png)

![image](https://user-images.githubusercontent.com/26542107/145725549-de5eb06f-468d-4214-a4e4-94ecf2f6608f.png)


## Why Svelte
One of the most popular frontend libraries at the moment is React.js but it is not very usable for web components. The main reason is that like other library you need to add all its dependencies to your project/component and react.js and react-dom.js with any web component no matter how small it is you will have attached like 150kb of dependencies and here is where Svelte comes in

Writing web components the hardcore way, pure JS is a bit of a pain. You need to write your component in pure JS with element.innerHTML and than all your code is wrapped in template literals and you lose any autocomplete, suggestion, emmet or similar coding help that you usually have, and for complex components, it would be very hard to write it and maintain later.

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
As we said, Svelte is a compiler so all your code, in the end, will be optimized, minimized and also all the unused scss will be deleted in the build.
Svelte also has a lot less boilerplate code or 8 lines block of code with array destructuring to update one variable like it is the case of React but the user experience and ease of use is subjective so I will just leave a meme here :D 

![svelereact](https://user-images.githubusercontent.com/26542107/145724696-ea4abbed-200a-40fe-8c00-c31a155a98b6.jpg)


2. ### You can use SCSS that is minified and unused code is deleted from the build


3. ### Easy props and state definition and usage
In svelte all the props at their core are variables and to define a simple city prop with an initial value of 'Pula' for example, is simply 
`export let city = "Pula";`


4. ### Svelte await block
if we need to get data from some API and display it in some elements, for example, we can use the svelte special await block that looks like:
```
{#await somePromise}
 before promise is resolved
{:than}
after promise is resolved
{/await
```

Example of fetching weather data and adding it in the header:
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


5. ### Svelte each block
Going through a list of elements and generating required items is very easy using the each block nad it looks like:
```
{#each items as item}
    <li> {item} </li>
{/each}
```

Real example:
```
{#each forecastListItems as day}
    <div class="forecast-day">
         <p>{data.list[day].dt}</p>
         <p>{data.list[day].main.temp}</p>
         <p>{data.list[day].weather[0].main}</p>
   </div>
{/each}
```

6. ### Svelte if block
Very easy to use if-else block implementation, example:
```
{#if units === "metric"}
     <p class="temp-unit temp-unit-metric" on:click={changeUnits}>O</p>
{:else if units === "imperial"}
     <p class="temp-unit temp-unit-imperial" on:click={changeUnits}>F</p>
{/if}
```

# Conclusion
In my opinion, using Svelte to create reusable web components is a great choice because of all the advantages listed above, and those are just some of the most useful ones.
