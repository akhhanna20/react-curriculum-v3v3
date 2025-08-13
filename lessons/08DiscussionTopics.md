# # Discussion Topics

## Overview

No overview provided

## Learning Objectives

Learning objectives will be defined as the lesson progresses.

## Topics Covered

Topics will be covered as the lesson progresses.

## Status

pending

## Assignment

Assignment for Lesson 8

### Objective

No objective specified

### Expected Capabilities

Expected capabilities will be defined as the lesson progresses.

### Instructions

Instructions will be provided when the lesson is generated.

### Tasks

#### Task 1: Task 1

## Weekly Assignment Instructions

### Expected App Capabilities

After completing this week's assignment, your app should:

- Use the API to sort todos by `title` or `createdTime`
- Use the API to search for todos based on title contents

### Instructions Part 1: Using Airtable's Query Parameters

#### Setup

- Add field, `createdTime` for default sort to your Airbase table. This will end up being the default sort field. We are not be able to use the already existing `createdTime` on the `record` object because Airtable functions only work with `fields` properties which are those defined by the user in the table. Use the following options in the field creator:
- Name the field "createdTime".
- Set it to "Date".
- Use the same timezones for everyone and set the timezone to GMT/UTC.
- Set a default value of "Current date".

![configure createTime field](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-08/created-time-config.png)

In App.jsx

#### Examine the APIs Query Parameters

You'll be using Airtable's query parameters to set the sort and filter options. Since we want to set up a default sort order we'll look at the Airtable's `sort`

- Navigate to your table's help documents and select "List records" from the left-hand side navigation. If you don't recall the process to get to that, here's another way you can select your base to get customized documentation: [Introduction - Airtable Web API](https://airtable.com/developers/web/api/introduction)
- Scroll down to `sort` and you'll be able to read about how to work with the `sort` URL query parameter.
  - This will give you some sense about how Airtable's parameters get turned into these ugly URL strings:

```text
sort%5B0%5D%5Bfield%5D=title
sort%5B0%5D%5Bdirection%5D=desc

https://api.airtable.com/v0/airtable_table_id/Todos?sort%5B0%5D%5Bfield%5D=createdTime&sort%5B0%5D%5Bdirection%5D=desc
```

Each one of those character combinations that start with a "%" sign - as in `%5B`, `%5D`, and so on, are characters that have been re-encoded to be URL safe. To better read the string contents, you'll have to rely on a URI decoder or looking up the characters one at a time. Coincidentally, JavaScript comes with a build-in decoder: `decodeUri()` that you can run that in the browser console to see the original string:

```console
decodeURI('https://api.airtable.com/v0/airtable_table_id/Todos?sort%5B0%5D%5Bfield%5D=createdTime&sort%5B0%5D%5Bdirection%5D=desc')

"https://api.airtable.com/v0/airtable_table_id/Todos?sort[0][field]=createdTime&sort[0][direction]=desc"
```

From the decoded string, we can tell that the Airtable API takes string representations of the configuration that Airtable uses to process the request. We don't fully know how the API works in reality, but we can liken this string to setting 2 properties on an object using bracket notation, one at a time. Remember that APIs differ from each other, so other APIs may not follow this same approach. This is why developers rely so heavily on documentation to assist with their work.

2 config params also means we need to represent those values in state so a user can toggle them.

- Still in App, create new state variables for the params with associated update functions:
  - `sortField`: initial value of "createdTime"
  - `sortDirection`: initial value of "desc" as in descending

#### URL Encoding Utility Function

You already employ the `url` in four locations: the `useEffect`, `addTodo`, `updateTodo`, and `completeTodo`. All fetches need to use the same query parameters so that the todo list doesn't spontaneously re-order itself or show all items in a filtered view. You'll centralize the logic in a utility function that exists outside of the component.

- Define a function `encodeUrl` above the `App` function definition:
  - It takes an object that includes the properties: `sortField, sortDirection`.
  - Inside the function, define a template literal that combines the 2 sort query parameters:
    - ``let sortQuery = `sort[0][field]=${sortField}&sort[0][direction]=${sortDirection}`;``
    - This compares to the console output above, but `createdTime` is replaced with a `${sortField}` and `desc` is replaced with `${sortDirection}`.
  - It returns a method call to `encodeURI` that takes in a template literal that appends the query parameters to `url`. The function should resemble:

```jsx
{/*extract from App.jsx*/}
{/*...code*/}

const encodeUrl = ({ sortField, sortDirection }) => {
  let sortQuery = `sort[0][field]=${sortField}&sort[0][direction]=${sortDirection}`;
  return encodeURI(`${url}?${sortQuery}`);
};
{/*code continues...*/}
```

- Replace all references to `url` in App with `encodeUrl()`.
  - At each location, pass in an object containing the state variables `sortDirection` and `sortField`.

 If you are using ESLint, you should see a warning appear on your `useEffect:`

![missing sortDirection and sortField dependencies](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-08/missing-deps.png)

- Update the `useEffect`'s dependency array to include the state values, `sortDirection` and `sortField`. The `useEffect will now fire` each time a user sets the `sortDirection` and `sortField`.

### Instructions Part 2: View Control Forms

You now need to provide the user with a way to update the params.

- Define a new component, `TodosViewForm` on the `src/features` directory.
- Return a form that contains a div that will contain the form controls.
  - Add a `label` containing the text "Sort by".
  - Add a `select` element that is associated with that label
  - Give the `select` element 2 `option` elements:
    - 1st: value="title" and includes the text, "Title"
    - 2nd: value="createdTime and includes the text "Time added"
  - Add another `label` containing "Direction" just after the previous label and select.
  - Add another `select` with `option` elements for:
    - "Ascending" using "asc" for the value
    - "Descending" using "desc" for the value
- Default export the component.
- In `App`, import it and place a horizontal rule between TodoList instance and the div containing the error message display.
- Below the `hr`, add an instance of `TodosViewForm`. The `hr` and `form` will show below the todos now:

![sort form on bottom of todos](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-08/todos-hr-sort-form.png)

- Into `TodosViewForm` instance, pass in the props `sortDirection`, `setSortDirection`, `sortField`, and `setSortField`.

Back over in `TodosViewForm` you'll wire up the form so that each time the user updates the options, the state updates will cause the `useEffect` to automatically fetch from Airtable.

- Destructure `sortDirection`, `setSortDirection`, `sortField`, and `setSortField` out of the component's props.
- On `select`element for the sort by:
  - Add an `onChange`handler:
    - Its callback is an anonymous function that:
      - Takes the event object
      - Calls `setSortField` with the event target's value.
  - Add a `value` props and assign it to `sortField`.
  - Ensure that each `option`'s value matches the target field names.
- On the `select` element for the sort direction:
  - Add an `onChange` handler that uses an anonymous function that:
    - Takes the event object
    - Calls `setSortDirection` with the event target's value.
- To define a function `preventRefresh` whose only job is to prevent the page from refreshing if a user accidentally hits enter while working with this form.
- pass it to an `onSubmit` props on the `form` element.

At this point, the form should allow you to select either "Title" or "Time Added" and the API will automatically return the sorted todos and update the UI.

![Time Added can be sorted ascending or descending](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-08/time-sort.png)

### Instructions Part 3: Search Todo Titles

In this part, you'll add a search field an button to the form that was just created. It will take in a string and then added the url params before it's encoded.

#### Update Utility Function

- In `App`, create the state value (and update function) for `queryString` with an empty string for an initial value.
- Update `encodeUrl` utility function:
  - Add `queryString` to the argument object.
  - Create an updatable variable (`let`) `searchQuery` set to an empty string.
  - Add `${searchQuery}` to the end of the template literal that is used in the return value.
  - Above the return statement, use an `if` block to assign a value to `searchQuery` is truthy.
    - If true, update `searchQuery` in the block: ``searchQuery = `&filterByFormula=SEARCH("${queryString}",+title)`;``
- Update each `encodeUrl` call in App to include `queryString` in its params object. Each of the 3 calls should now resemble: `encodeUrl({ sortDirection, sortField, queryString })`
- Update the `useEffects` dependency array by adding `queryString`.

#### Update Form

- In `App`, to the `TodosViewForm` instance, add props `queryString` and `setQueryString`,
- In `TodosViewForm`:
  - Destructure these props out.
  - Add a `div` above the previous one and include:
  - A label containing the text "Search todos:"
  - An input with props:
  - `type="text"`
  - `value={queryString}`
  - `onChange((e)=> {setQueryString(e.target.value)`
  - A button:
  - Set to the type of button.
  - Contains the text "Clear".
  - And empties `queryString` whenever it's clicked.

Your user is now able to filter the todos based on an input that they then can also reset.

![demo of todo list functionality](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-08/search-demo.gif)

### Closing Notes

This week you added in functionality to sort the todos by title or its creation date and filter the titles to match a string input. To minimize code edits across several fetches, you extracted the logic to build the URL string into a utility function. Also, as a result of how form state is managed, all updates happen live! Each time a user types or selects an option, the `useEffect` initiates a fetch using the updated url params. This produces so pretty snappy results but can eventually become a burden on the network and API services as the application grows or a user's todo's start growing in number. Next week' we will look at a few ways limit network traffic and perform some other optimizations to the codebase to keep the interface running smoothly.


```

```

### Submission Instructions

Please submit on time

### Checklist

Checklist will be provided when the lesson is generated.

### Check for Understanding

Understanding checks will be provided when the lesson is generated.

## Subsections

### # Discussion Topics

## Discussion Topics

### Sorting

#### Default Sorting

After some (pretend) success, CTD has decided to add more items to its eCommerce store:

![scrolling product list](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-08/long-scroll.gif)

What is readily apparent with the growth is the store's page is much longer and products are out of order. We can improve the user's experience by immediately sorting the products alphabetic order and giving the them other sorting options.

To get started, we'll declare a new utility function above the App component to keep our sorting logic. To respect to React's functional programming approach we choose methods that return data instead of update some existing value. When sorting an array, we have two methods available to help us sort `.toSorted()` and`.sort()`. We choose `.toSorted()` because it returns a new array.

```js
// extract from App.jsx
//...code
function sortByBaseName(productItems) {
  return productItems.toSorted((a, b) => {
    const baseNameA = a.baseName.toLowerCase();
    const baseNameB = b.baseName.toLowerCase();
    if (baseNameA > baseNameB) {
      return 1;
    }
    if (baseNameA < baseNameB) {
      return -1;
    }
    return 0;
  });
}
const baseUrl = import.meta.env.VITE_API_BASE_URL;
function App() {
//code continues...
```

To finish up this first iteration of sorting, we then add it to the `useEffect` that processes our initial fetch request. `sortByBaseName` is then called with the response to alphabetize the array we pass `setInventory`.

```js
// extract from App.jsx
//...code
useEffect(() => {
  (async () => {
    try {
      const resp = await fetch(`${baseUrl}/products`);
      if (!resp.ok) {
        throw new Error(resp.status);
      }
      const products = await resp.json();
      const sortedProducts = sortByBaseName({
        productItems: products,
        isSortAscending: true,
      });
      setInventory([...sortedProducts]);
    } catch (error) {
      console.error(error);
    }
  })();
}, []);
//code continues...
```

CTD-Swag's product list now shows in alphabetic order. That is a helpful start but I think we can do better.

#### Sort by Field

CTD Swag's users may also want to browse the shop in different ways. For, example, a user may want to sort by price. To allow the user to browse by price, we'll create a form at the top of the page. For now, we just construct the basic form with static content to visualize the purpose of the form.

![sort options](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-08/sort-options.png)

We then update the utility function `sortByBaseName` by adding `isSortAscending` to the arguments and updating the sorting logic.

```js
// extract from App.jsx
//...code
function sortByBaseName({ productItems, isSortAscending }) {
  return productItems.toSorted((a, b) => {
    const baseNameA = a.baseName.toLowerCase();
    const baseNameB = b.baseName.toLowerCase();
    if (baseNameA > baseNameB) {
      if (isSortAscending) {
        return 1;
      } else {
        return -1;
      }
    }
    if (baseNameA < baseNameB) {
      if (isSortAscending) {
        return -1;
      } else {
        return 1;
      }
    }
    return 0;
  });
}
//code continues...
```

We create another utility function, `sortByPrice`, that behaves similar `sortByBaseName` but is much shorter since we are working with numbers:

```js
// extract from App.jsx
//...code
function sortByPrice({ productItems, isSortAscending }) {
  return productItems.toSorted((a, b) => {
    if (isSortAscending) {
      return a.price - b.price;
    } else {
      return b.price - a.price;
    }
  });
const baseUrl = import.meta.env.VITE_API_BASE_URL;
function App() {
}
//code continues...
```

We we now employ 2 `useState` hooks so that we can make that form into a controlled form.

```js
// extract from App.jsx
//...code
const [isSortAscending, setIsSortAscending] = useState(true);
const [sortBy, setSortBy] = useState('baseName');
//code continues...
```

This form isn't going to be re-used but to keep the codebase organized we refactor the form out of App and into a `ProductViewForm` located at `src/features/ProductViewForm/ProductViewForm.jsx`.

In the new `ProductViewComponent` we update the placeholder values to work with state, converting the form to a controlled form. Since HTML stores values as either strings or numbers, we need a helper function to convert the value used to update `isSortAscending` back into a boolean. Here is the final `ProductViewForm`:

```js
// extract from ProductViewForm.jsx
function ProductViewForm({
  setSortBy,
  setIsSortAscending,
  sortBy,
  isSortAscending,
}) {
  //helper to convert text back into a boolean
  const handleSortDirectionChange = (e) => {
    const sortDirection = e.target.value;
    if (sortDirection === 'false') {
      setIsSortAscending(false);
    } else {
      setIsSortAscending(true);
    }
  };

  return (
    <form className="filterForm">
      <div className="filterOption">
        <label htmlFor="sortBy">Sort by: </label>
        <select
          name="sortBy"
          id="sortBy"
          value={sortBy}
          onChange={(e) => setSortBy(e.target.value)}>
          <option value="baseName">Product Name</option>
          <option value="price">Price</option>
        </select>
      </div>
      <div className="filterOption">
        <label htmlFor="sortDirection">Direction: </label>
        <select
          name="sortDirection"
          id="sortDirection"
          value={isSortAscending}
          onChange={handleSortDirectionChange}>
          <option value={true}>Ascending</option>
          <option value={false}>Descending</option>
        </select>
      </div>
    </form>
  );
}

export default ProductViewForm;
```

Finally, we'll create a `useEffect` in `App` that watches for changes on either of the new state values. This `useEffect` ties in our utility functions to sort the product list. Note that if we were to work with `inventory` directly into the useEffect, that state value would become a dependency of that `useEffect`. We can avoid this by passing in a function to update state rather than setting it directly. The reason why we are able to do this is that the state update function provides the old state value as an argument that we can use to return a new value for the state. This behavior can be made more apparent through by naming the argument `previous`, similar to how we would use `item` in a map going over `listItems`: eg `listItems.map((item)=> {…`

```js
// extract from App.jsx
//...code
useEffect(() => {
  if (sortBy === 'baseName') {
    setInventory((previous) =>
      sortByBaseName({ productItems: previous, isSortAscending }),
    );
  } else {
    setInventory((previous) =>
      sortByPrice({ productItems: previous, isSortAscending }),
    );
  }
}, [isSortAscending, sortBy]);
//code continues...
```

These updates results in a product list that the user can sort by the product name or the price:

![changing sort options](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-08/sort-options.gif)

### Filtering

Filters are useful for showing only a a sub-set of product items that contain the term. This can shorten the list of items the user needs to look. Not surprisingly, array's `.filter` method is central to this feature. Let's get started with a basic filter function. We'll allow the user to filter on a match in `baseName`, `baseDescription`, and the `variantDescription` properties on each item.

```js
const items = [
  //contains an offline copy of listItems
];
function filterByQuery({ productItems, searchTerm }) {
  const term = searchTerm.toLowerCase();
  return productItems.filter((item) => {
    if (item.baseName.toLowerCase().includes(term)) {
      return item;
    } else if (item.baseDescription.toLowerCase().includes(term)) {
      return item;
    } else if (item.variantDescription.toLowerCase().includes(term)) {
      return item;
    }
  });
}
const filteredItems = filterByQuery({
  productItems: items,
  searchTerm: 'pillow',
});
console.log(filteredItems);
```

This initial `filterQuery` when ran on its own results in the following output:

```terminal
#terminal output from running code above:
[
  {
    id: 179,
    baseName: 'Throw Pillow',
    variantName: 'Peach',
    price: 44.99,
    baseDescription: 'Comfortable throw pillow and an excellent conversation starter',
    variantDescription: 'Peach cotton with large pale peach logo',
    image: 'throw-pillow-peach.png',
    inStock: true
  },
  {
    id: 180,
    baseName: 'Throw Pillow',
    variantName: 'Turquoise',
    price: 44.99,
    baseDescription: 'Comfortable throw pillow and an excellent conversation starter',
    variantDescription: 'Turquoise cotton with large dark blue logo',
    image: 'throw-pillow-turquoise.png',
    inStock: true
  },
  {
    id: 185,
    baseName: 'Pillow Case',
    variantName: 'Orange',
    price: 23.99,
    baseDescription: 'Comfortable pillow case and an excellent conversation starter',
    variantDescription: 'Orange cotton with large pale orange logo',
    image: 'pillow-case-orange.png',
    inStock: true
  },
  {
    id: 186,
    baseName: 'Pillow Case',
    variantName: 'Turquoise',
    price: 23.99,
    baseDescription: 'Comfortable pillow case and an excellent conversation starter',
    variantDescription: 'Turquoise cotton with large dark blue logo',
    image: 'pillow-case-turquoise.png',
    inStock: true
  }
]
```

Our now utility function works so now we need to create the rest of the filter feature before we integrate it. For now, we place it with the other utility functions at the top of the App component file. We next need to add filter UI elements to the form we made while implementing sort.

![filter field added](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-08/filter-field.png)

At this point, we have to consider how this filter should interact with our global state. We don't want this this function removing anything from the `inventory` so we will have to create an intermediate working state to provide a `filteredInventory` to the product list. Its associated `setFilteredInventory` state function is called alongside `setInventory` in the `useEffect` that fetches the inventory. We then replace the `inventory` prop with `filteredInventory` in ProductList instance.

```jsx
{/*extract from App.jsx*/}
{/*...code*/}
<ProductList
   inventory={filteredInventory}{/*no need to change the prop key name*/}
   handleAddItemToCart={handleAddItemToCart}
></ProductList>
{/*code continues...*/}
```

Whenever a user adds a filter term, we filter `inventory` and provide the returned value to the `setFilterInventory`. We employ another `useEffect` to coordinate the live updating query results. Since we want the filter to run every time `query` changes, we add that to the `useEffect`'s dependency array. ESLint will also provide a warning that `inventory` needs to be added to the dependency array too. Since it remains unchanging after it is set, we can add it to that array. We end up with a `useEffect` that allows ProductList to generate ProductItems on the fly that include any variant that matches the filter.

```js
// extract from App.jsx
//...code
useEffect(() => {
  setFilteredInventory(filterByQuery({ productItems: inventory, searchTerm }));
}, [searchTerm, inventory]);
//code continues...
```

Here is the update interface in action:

![filter live-updates search for terms](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-08/filter-pillow.gif)

Sort and search implemented locally are appropriate for applications that work with a limited amount of data. We already have all product data on hand so these approaches great for improving the experience users have with CTD Swag.

Applications that use larger datasets commonly paginate the data returned from an API into digestible chunks. Think of how many products Amazon.com has. It would be impossible to send all of their product information is set to the UI. In these cases, sorting and filtering are handled by the API. Just like the sort and search feature we just implemented, we don't need any other React tools to create an SPA that relies on an API for search and filtering.


**Video URL:** No video available

**Code Examples:**

No code examples available

**External Links:**

No external links available

**Quizzes:**

No quizzes available

## Supplemental Videos

No supplemental videos available

## References

No references available

## Podcast URL

No podcast available