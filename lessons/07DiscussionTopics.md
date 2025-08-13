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

Assignment for Lesson 7

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

- Save todos to an Airtable base
- Load saved todos from Airtable on loading
- Display pending messages during network requests
- Display error messages on network or authentication error

### Instructions Part 1: Set up Airtable Base

An Airtable base is a collection of tables that stores data, similar to a database, but with a user-friendly interface that resembles a spreadsheet. Each base can contain multiple tables, each with its own set of rows and columns to organize and store different types of information.

We will use an Airtable table as an API to save data for the todo list.

#### Before You Begin

You'll need a place to store your personal access token, base id, and table name. These are details that we do not want to publish with our project so we'll use an environment file, .env.local. It has already been listed in the project's .gitignore so we don't have to worry about it being saved into version control.

After creating this week's working branch, create a environment file, `.env.local`, in the base directory of the project then paste in the following text:

```text
VITE_PAT=super_secret
VITE_BASE_ID=xxxxxxxxxxxxxxxxx
VITE_TABLE_NAME=TableName
```

As you go through the setup and documentation, you'll save those values for the project's use.

#### Create Base

- Your first steps is to [sign up for an Airtable account](https://airtable.com/signup). Once completed, follow the screenshots to set up your todo's base.
- Choose the Create button at the bottom left and choose to start from scratch.

![choose start from scratch](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/start-scratch.png)

- Rename the table's default name of "Table 1" to "Todos" by clicking on the down arrow on Table 1's tab.
- Save the table name to your environment file under the variable `VITE_TABLE_NAME`
- Update the first field name so that it reads "title" instead of "Title".
- Delete the other fields.

![hit down arrow and select edit field](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/update-fields.png)

- Create a new checkbox field named "isCompleted". This type of field acts a boolean.

![add isCompleted field](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/add-is-complete.png)

#### Generate Connection Credentials

- Click on profile picture to open menu.
- Select "Builder hub"

![navigate to builder hub](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/builder-hub.png)

- Select "Create new token".

![create a new personal access token](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/new-pat.png)

- Give the token a name that you'll remember.
- Add the scopes `data.records:read` and `data.records.write`
- Under the Access heading. Hit "Add a base" and choose your new one.
- Hit "Save changes" Copy the token value that shows on the next screen
- Save it to `VITE_PAT` in your environment file.

![configure access token](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/config-pat.png)

- We next have to find the base's id - one approach is to use the API documentation accessed from the base's help menu.

![navigate to customized api documentation](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/find-api-docs.png)

- After the introduction paragraphs, the documentation includes your personal base's id. Save that to `VITE_BASE_ID` in the environmental file.

![documentation is customized to match base config](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/custom-base-docs.png)

Take some time to look over the documentation for Airtable. As you read it over, keep in mind that we will **not** be using Airtable's JavaScript API client. It does not provide anything useful for us in React so it's better to look at the `curl` examples. That'll let you see how the urls and payloads look. Some of the things you can do with the API are pretty cool! Go take a look!

> [!note]
> The backslash `\` in the `curl` code examples lets us hit enter to continue adding arguments to the `curl` command.

```terminal

## using backslash

curl "https://api.airtable.com/v0/app8c6VR6IQ26pgKP/Todos?maxRecords=3&view=Grid%20view" \
  -H "Authorization: Bearer super_secret_api_key"

## without backslash and new line, it's harder to read the command

curl "https://api.airtable.com/v0/app8c6VR6IQ26pgKP/Todos?maxRecords=3&view=Grid%20view" -H "Authorization: Bearer super_secret_api_key"
```

### Instructions Part 2 : Load Todos from Airtable

Your app be updated to load the todos from Airtable and update the UI using a pessimistic update approach. While the todos are loading, you'll display a wait message to the user.

In App.jsx:

#### Establish Transition State

- Create an `isLoading` state variable with with a `defaultValue` of `false`. Destructure out its associated update function, `setIsLoading`, too.
- Create another state variable but name it `errorMessage` and default to an empty string, `""`. Include its update function too.

#### Fetch in App

You will use the `useEffect` hook to define and send off the initial fetch request that loads the todos from Airtable. You'll also work with `isLoading` to conditionally show a loading message to the user. If we ever have to make a user wait, it's important to indicate that our app is in the middle of a slow process. This lets the user know that the app is processing a request and they should wait for it to complete.

While still in App.jsx:

- Create constants, `url` and `token`, which will be used in the fetch requests.
  - ``const url = `https://api.airtable.com/v0/${import.meta.env.VITE_BASE_ID}/${import.meta.env.VITE_TABLE_NAME}`;``
  - ``const token = `Bearer ${import.meta.env.VITE_PAT}`;``
- create the `useEffect` that was discussed above:
  - Give it an empty dependency array.
  - In the body of the function in `useEffect`'s first argument do the following:
    - Define an async anonymous arrow function that takes no arguments and save it to `fetchTodos`
    - Make sure to call `fetchTodos()` before closing out the `useEffect`'s first argument.

So far, it should look like:

```jsx
{/*extract from App.jsx*/}
{/*...code*/}
useEffect(() => {
    const fetchTodos = async () => {};
    fetchTodos();
}, [])
{/*code continues...*/}
```

- continuing in `fetchTodos`
  - Use `setIsLoading` to update `isLoading` to `true`
  - Create an `options` object for the fetch request. Include:
    - A `method` property set to "GET",
    - And a `headers` property set to an object containing the following key/value pair: `"Authorization": token`.
  - Set up `try/catch/finally` blocks to handle the fetch:
    - in `try`
      - Save an awaited `fetch` to the const `resp` and pass in the `url` and `options` : `const resp = await fetch(url, options);`
      - If `resp.ok` evaluates to false in an `if` statement, throw a new `Error` that takes in `resp.message`.

After the `if` block that throws, you'll place your remaining logic to process the API's response.

- Await the value that `response.json()` returns. We will be working with the `records` array in the response. An example response would be:

```JSON
{
    "records": [
        {
            "id": "recEAx4TQF6JjzZLV",
            "createdTime": "2025-02-19T22:04:12.000Z",
            "fields": {
                "title": "test todo"
            }
        },
        {
            "id": "recx22h6JwjsMhCku",
            "createdTime": "2025-02-19T22:04:15.000Z",
            "fields": {
                "isCompleted": true,
                "title": "make dinner"
            }
        },
        {
            "id": "recFwX5Nyp1TpkUXI",
            "createdTime": "2025-02-19T22:04:17.000Z",
            "fields": {
                "title": "code out assignment examples"
            }
        }
    ]
}
```

There are several important details to note when you compare this response against the `todoList` state value:

- The properties, `isCompleted` and `title` are stored in a `fields` object.
- The `id` is at the top level of each todo object.
- Any fields that equal false or contain an empty string will not be returned with the todos.
- The `records` value will always be an array.

Because the "shape" of each todo is different than how they are represented in the app's `todoList` array, you will have to map the contents of `record` out of the response body. The same will be true for the payload of any PUT and PATCH requests.

- in a `map` method called on `records`, define an anonymous function:
  - It takes a `record` param.
  - It contains a `const todo = {}`.
  - Inside the object, assign the `record` properties to the appropriate todo properties.
  - If `isCompleted` is not truthy, you will want to explicitly set the property equal to `false`.

Use the below `map` examples as reference if needed:

```js
//example map from an Airtable response.records

const response = await response.json();
const fetchedExamples = response.records.map((record) => {
    const example = {
        id: record.id,
        //destructuring is a compact way to move all fields over
        //won't need to change this a lot as the app evolves
        ...record.fields,
        };
    if(!example.booleanProperty){
        //Airtable doesn't return properties whose values are false or empty strings
        //explicity setting property to false so field exists and prevents logic bugs
        example.booleanProperty = false;
    }
    return example;
})
setExamples([...fetchedExamples])
```

-or a more compact version-

```js
const {records} = await response.json()
setExamples(records.map(record)=> {
    const example = {
    id: record.id,
    ...record.fields,
    };
    if(!example.booleanProperty){
        example.booleanProperty = false;
    }
    return example;
})

```

After this, it's time to move on to the `catch` and `finally` blocks.

- The `catch` block:
  - Takes in an error parameter.
  - Use `setErrorMessage` with `error.message` to set an error message that we will display to the user shortly.
- The `finally` bock:
  - Set the `isLoading` state value back to false.

#### Display Loading Indicator in TodoList

- While still in App.jsx, pass `isLoading` into props for the `TodoList` instance.
- In TodoList:
  - Destructure `isLoading` out of its props.
  - Add a statement that evaluates `isLoading` in the JSX that conditionally renders the existing paragraph OR a new paragraph for the loading message.
  - The new paragraph should have a loading message to display the user. Eg: `<p>Todo list loading...</p>`

At this point, the todo list should display a message while the API is loading the todos. Once the fetch resolves, it re-renders with the todo list (Or the original paragraph if 0 todos are loaded).

![todo list displays loading message](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/loading-message.gif)

#### Display Error

In App:

- Add conditional block after the `TodoList` instance. Use the block to evaluate `errorMessage`.
  - If not empty, display a div containing:
    - A horizontal rule `<hr/>`.
    - A paragraph that renders `errorMessage`.
    - A dismiss button that empties `errorMessage`.

When an error happens with our initial fetch, a message is displayed to the user. We've intentionally made this setup as simple as possible so that we can re-use this error message for other operation errors.

### Instructions Part 3: Update Add New Todo Functionality

In part 3, you'll use an pessimistic state update approach for adding todos. You'll use transient state to change the text content of the button while the request is processing. We don't want pause the user but it's useful for them to know that the application is processing the last action before their todo is rendered . When the API has processed the response, the todo in the payload response gets added into the todo list state.

In App:

- Create state value`isSaving` with a `defaultValue` of false. You will need its update function too.
- In the add todo button
  - Replace the button text with a ternary block that evaluates `isSaving`.
    - If true, display "Saving..."
    - If `false`, display "Add Todo" or the text value you were already using.

It should now look like:

```jsx
<button disabled={isButtonDisabled}>
    {isSaving ? 'Saving...' : 'Add Todo'}
</button>
```

- Update `addTodo(newTodo)`:
  - Convert it to an async function.
  - Inside the function body...
    - Create a `payload` object containing a `records` array containing a single todo object.
    - Create an `options` object for the fetch request:
      - Use the "POST" method.
      - Use the same header options from the last request.
      - Stringify the `payload` object for `options.body`.

At this point, `addTodo` should look like:

```jsx
{/*extract from App.jsx*/}
{/*...code*/}

const addTodo = async (newTodo) => {
    const payload = {
      records: [
        {
          fields: {
            title: newTodo.title,
            isCompleted: newTodo.isCompleted,
          },
        },
      ],
    };
    const options = {
      method: 'POST',
      headers: {
        Authorization: token,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(payload),
    };
{/*code continues...*/}
```

- Continuing from inside `addTodo`
  - `try` block logic:
    - Set `isSaving` true.
    - Send a fetch request and assign it's awaited return value to `resp`.
      - (hint: `const resp = await fetch(url, options);`)
      - Throw an Error if `!resp.ok`
    - Destructure the `records` from the response by using `const { records } = await resp.json();`
    - Create a `savedTodo` object that:
      - assigns the `id` to the first array entry's id property.
      - destructures the remaining key/values pairs out of the only `records` object.
    - If the `isCompleted` property in the `records` object is not true, explicitly set that on property on `savedTodo` to `false`
      - This looks like `if (!records[0].fields.isCompleted) {savedTodo.isCompleted = false;}`
      - Recall that Airtable does not return false or empty fields in the `records`.
    - Update state using `setTodoList([...todoList, savedTodo]);`
  - `catch` block logic:
    - Log the error to console.
    - Update `setErrorMessage` state value with `error.message`. This will cause errors to show up for users.
  - `finally` block:
    - Set `isSaving` to false.

The updates you've made should flash a brief message on the button while the response processes:

![saving message shows briefly on submit button](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/saving-message.gif)

### Instructions Part 4: Update Functionality of Update and Complete Todo

For the final two refactors, you will be using an optimistic approach to UI management. This introduces the need to undo state changes and communicate unexpected API failures to the user. You'll set the updated value into state at the beginning of the `updateTodo` function but not before saving the original version temporarily. If there's a network disruption or some other issue that causes the fetch to fail, you'll use this original version to revert the todoList state back to its state before the error.

#### Update Todos Functionality

In App:

- update `updateTodo:
  - Convert it to an async function.
  - Save the original todo to `originalTodo`:
    - By finding it's associated object in the `todoList` array by each object's id.
    - It should look like: `const originalTodo = todoList.find((todo) => todo.id === editedTodo.id)`
  - Create `payload` object for the fetch request using `editedTodo`
    - The API expects `payload` to be a JSON object containing a `records` array of table records for the Todo table. For reference, here's the JavaScript object you define in preparation to do this:

```JSON
const payload = {
    records: [
        {
            id: editedTodo.id,
            fields: {
                title: editedTodo.title,
                isCompleted: editedTodo.isCompleted,
            },
        },
    ],
};
```

- Continued...
  - Create an `options` object for the fetch request.
    - Use the PATCH method.
    - Use the same Authentication header and include `'Content-Type': 'application/json'` in the `headers` object.
    - For the `body` property, convert the `payload` object to a JSON string using `JSON.stringify(payload)`
  - `try` block:
    - Send a fetch request using `url` as the endpoint and the `options` object just created.
      - Throw an error if `!resp.ok`.
  - `catch` block:
    - Log the error to console.
    - Update `errorMessage` using the ``setErrorMessage(`${error.message}. Reverting todo...`);``
    - Create a `revertedTodos` using the `originalTodo` to reset that todo's value to a previous state.
    - Update state with `setTodoList([...revertedTodos]);`
  - `finally` block:
    - Set the state value, `isSaving`, to false.

In TodoListItem:

We need to cover an edge case where an outdated value is displayed when a user saves a todo and clicks it again immediately.

- add a useEffect that resets the `workingTitle` to the `todo` prop's `.title` value
  - add `todo` to the dependency array.
  - call `setWorkingTitle` and pass in the value of `todo.title`

At this point, the todo app should:

1. Load the todos from Airtable.
2. Create new todos with the todo form and persist them in Airtable.
3. Allow users to edit todos which will then update in Airtable.
4. Display errors messages to the user if there are any fetch issues.

![error displayed to user at bottom of screen](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/assignments/assets/week-07/error-message.png)

#### Update Functionality to Complete Todos

You'll be left to update `completeTodo` with the necessary logic to communicate the completed todo to the Airtable API and keep the UI synced. You can use `updateTodo` as a reference as you work through this final portion. So that you have a checklist of details to think about: the final code should:

- [ ] optimistically update the UI:
  - [ ] The todo completes immediately in the UI immediately and allows user to continue to work with the todo list.
  - [ ] The fetch request happens in the background unless there's a request error.
  - [ ] The UI for the todo list reverts to its original state if the fetch response does not come back okay.
  - [ ] Display an error message to the user, using the `errorMessage` state if fetch encounters any problems.
- [ ] Fetch should use the same headers and method, and take in a payload very similar to the fetch found in `updateTodo`
- [ ] The same object transformation has to happen between the todo object as represented in state and the todo represented in the `records` property on the payload.

### Stretch Goals: Refactor for Reusable Code

You will probably have noticed that you are starting to introduce some duplicate code. While that is okay during prototyping and early development, before submitting any final code for a feature (in our case, this assignment), take the time to refactor your code to make it **[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)**. This will keep the codebase clean and easy to manage. Remember that you should commit your code now so that you have a good save point to return to if you need to start the refactor over again.

- Look for any items that are defined multiple times and assign them to a variable.
- Compare functions or procedures to find common logic that could be shared in a helper or utility function.
- Brainstorm some more user-friendly error messages.
- If you visual learner and have to add styling to help visualize the interface better:
  - Stick to class-based styles that you define in App.css.
  - Don't restructure anything in the JSX. We want to maintain a semantically sound html structure so that when we cover styling in week 10, several style choices applied during the assignment will work out properly.
  - Have fun but don't let styling get away of completing the weekly lesson. After all, a baker doesn't ice a cake until after it's baked and cooled.

### Closing Notes

 Next week we cover sorting and filtering. The discussion will take us through implementing sorting and searching on both local and API data stores. We'll then implement an API-based sorting and filtering strategy.


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

### Data Fetching

Integrating with an API is a common development task for SPAs. Seldom do they operate without pulling in external data. We can use established APIs for viewable information such as [news articles](https://medium.com/rakuten-rapidapi/top-10-best-news-apis-google-news-bloomberg-bing-news-and-more-bbf3e6e46af6), [movie summaries](https://developer.themoviedb.org/docs/getting-started), or [important Pokemon facts](https://pokeapi.co/?ref=public-apis). For interactive data, we usually work with a custom server hosting API to fetch data that is tailored for use in the UI. This can include user profile or account information, article or video view history, forum posts, and so on. Since some of this data is meant to be managed by the user, we also need to be able to save data changes to the backend.

Most of these data fetching tasks can be categorized into one of four basic tasks - creating, reading, updating, and deleting. These are so common across applications that they are collectively referred to as CRUD operations. Each type of operation uses a specific fetch method when creating a network request.

- **Create** - "POST"
- **Read** - "GET" - this is the default action when a fetch method is not specified
- **Update** - "PUT" or "PATCH"
  - PUT is used to completely replace an existing record with new data
  - PATCH only changes some parts of a record. Eg - a user updates their username then that field only is updated in the record
- **Delete** - "DELETE"

Every network request is a very slow process from the perspective of a browser. During a data request, we want to avoid making JavaScript to wait for the request to resolve before it continues running. Since JavaScript shares the main thread with other browser tasks, processing a network request synchronously causes the browser to become unresponsive. To allow the main thread to continue executing while the request is not yet fulfilled, we turn to _[Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)_. This article, [States and Fates](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md) helps explain the terminology promises use. There are two approaches to promises and asynchronous JavaScript code: using a promise's `then/finally/catch`methods or asynchronous functions with `await`.

> [!note]
> From here on, we will shorten **asynchronous** to **async** since that term is more frequently used than the full word.

#### Then/Finally/Catch

Using `then`, `finally`, and `catch` with fetch allows us to handle async operations with promise chaining. We can chain multiple `then` (or [`thenable`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#thenables) ) blocks together to process a response. We use `.catch` for error handling and `.finally` to run code after a promise settles, regardless of its outcome. This approach provides explicit control over async code execution flow. The example code below fetches a url for a random dog picture and then appends it to an html document body. Regardless if it is successful or throws, the `.finally` logs a statement to the console.

```jsx
fetch('https://dog.ceo/api/breeds/image/random')
  .then((resp) => {
    if (!resp.ok) {
      throw new Error(resp.status);
    }
    return resp.json();
  })
  .then((data) => {
    if (data.status != 'success') {
      throw new Error(data.status);
    }
    placeImage(data.message);
  })
  .catch((error) => {
    console.log(error.message);
  })
  .finally(() => {
    console.log('action completed');
  });

function placeImage(url) {
  const img = document.createElement('img');
  img.src = url;
  document.body.append(img);
}
```

#### Async/Await

Async/await, which also uses promises, is another JavaScript feature that allows us to write async code - but in a synchronous[^synchronous] style. In an async function we use the `await` keyword to pause the function's execution until a promise is resolved. The main thread can continue on with other JavaScript code or browser tasks during this time. Once the promise fulfills or is rejected, execution of the async function resumes and it has. A try/catch block is used to handle errors in async fetch operations when using async/await. The below is the async/await equivalent of the code from the previous example. We end up with code that slightly easier to read so we will continue using this approach.

```javascript
const fetchDog = async () => {
  try {
    const resp = await fetch('https://dog.ceo/api/breeds/image/random');
    if (!resp.ok) {
      throw new Error(resp.status);
    }
    const data = await resp.json();
    if (data.status != 'success') {
      throw new Error(data.status);
    }
    placeImage(data.message);
  } catch (error) {
    console.error(error.message);
  } finally {
    console.log('action completed');
  }
};

function placeImage(url) {
  const img = document.createElement('img');
  img.src = url;
  document.body.append(img);
}
fetchDog();
```

#### GET with useEffect

There are libraries that help us integrate network requests but the simplest way to get started us with `useEffect` and a `GET` request. Remember that this hook is used to [synchronize data with external systems](https://react.dev/learn/synchronizing-with-effects). Whenever a component first mounts the useEffect runs regardless its list of dependencies. We can take advantage of this to initiate a fetch request when `App` or some other component first mounts.

We place the fetch request into the parent component that maintains relevant state. The `useEffect` function is synchronous and expects there to be either 1.) no return value or 2.) a cleanup function. Fetch always returns a promise which will cause problems with the `useEffect`. Also, the `await` keyword is only available inside async functions. To get around these limitations, we declare and execute an async function containing the fetch inside the `useEffect` as seen in the example below. We're sticking with dogs for now, so this component includes the same fetch but adapted to work in a component and is [immediately invoked](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) after it is defined.

```jsx
import { useEffect, useState } from 'react';

export default function RandomDogPicture() {
  const [img, setImg] = useState(null);
  const [error, setError] = useState('');

  useEffect(() => {
    const url = 'https://dog.ceo/api/breeds/image/random';
    //fetchDog is now an IIFE
    (async () => {
      try {
        const resp = await fetch(url);
        if (!resp.ok) {
          throw new Error(resp.status);
        }
        const data = await resp.json();
        if (data.status != 'success') {
          throw new Error(data.status);
        }
        setImg(data.message);
      } catch (error) {
        setError(error.message);
      }
    })();
  }, []);

  return (
    <>
      {error && <p>{error}</p>}
      <img src={img} alt="" />
    </>
  );
}
```

> [!important]
> If you're coding along, you may have already noticed the occasional double image change when the app first loads. We need to resolve this bug.

Remember that in the development environment, `<StrictMode>...</StrictMode>` causes components to mount twice to expose impure component functions. This means that the useEffect is firing twice so two network requests are being sent off in rapid succession. In most other API fetch scenarios the data that is returned is same between the requests. The double mount in this typical case does not cause a problem with the UI. Since the [DOG CEO](https://dog.ceo) API returns a different picture for each request, the differing results of the two fetches causes our application to behave in an undesired manner.

We discuss rate limiting in week 9 but it's worth looking at a way to fix the component's behavior. As a good coding practice, we do not want to modify our component to behave differently based on StrictMode's presence. _This is akin to disabling ESLint warnings in an IDE - it hides warnings about undesired behavior but does not fix it!_ We cannot prevent the network request from happening twice but we can ignore the results of the first request. We accomplish this by setting a boolean `let isRan = false` at the beginning of the `useEffect`. We then only call `setImg(data.message)` if `isRan` is false when the the `if` block is finally evaluated. The final, and most important detail is to include a cleanup function that toggles `isRan` to true. Let's look at the refactored code:

```jsx
import { useEffect, useState } from 'react';

export default function RandomDogPic() {
  const [img, setImg] = useState(null);
  const [error, setError] = useState('');
  const url = 'https://dog.ceo/api/breeds/image/random';

  useEffect(() => {
    let isRan = false;
    (async () => {
      console.log('useEffect running');
      try {
        const resp = await fetch(url);
        if (!resp.ok) {
          throw new Error(resp.status);
        }
        const data = await resp.json();
        if (data.status != 'success') {
          throw new Error(data.status);
        }
        if (isRan) {
          console.log('skipped setImg()');
        } else {
          console.log('ran setImg()');
          setImg(data.message);
        }
      } catch (error) {
        setError(error.message);
      }
    })();
    return () => {
      console.log('cleanup performed');
      isRan = true;
    };
  }, []);

  return (
    <>
      {error && <p>{error}</p>}
      <img src={img} alt="" />
    </>
  );
}
```

To appreciate what this is accomplishes, we have to consider where the execution is being paused and the lexical scope of `isRan`. `isRan` is declared inside of the useEffect so is available to anything else written in that scope. This means that the cleanup function has access to it. This function also happens to be a [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) so continues to have access to `isRan` whenever it is eventually executed.

Going back into the function that runs fetch, code execution stops at `const resp = await fetch…`. While the fetch is pending, execution has not reached the `if` statement that evaluates `isRan`. As a result, if the cleanup function runs while the fetch is still pending, `isRan` is changed to `true` so the state update function, `setImg` isn't called. This drops the request's payload and avoids the changing image during the second render. In summary, the `RandomDogPic` component ends up ignoring the first request and displays the result of the second request.

The code above also includes a few `console.log` statements that provide us some insight into the execution order across the useEffect.

![page loads dog image](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/useeffect-skip-image.gif)

The following is logged to the console when the app first loads:

```console
[vite] connecting...                     client: 495:8
[vite] connected.                       client: 614:14

useEffect running                       App.jsx: 27:14
cleanup performed                       App. jsx:49:14
useEffect running                        App.]sx:27:14
skipped setImg ()                        App.]sx:38:18
ran setImg()                            App. jsx:41:18
»
```

The diagram below illustrates the sequence of execution.

```mermaid
sequenceDiagram
    participant UI
    participant Component
    participant img state
    box transparent useEffect
    participant useEffect
    participant fetch
    participant cleanup
    end
    participant API as DOG.CEO API


    Component-->>useEffect: 1st mount
    useEffect-->>fetch: 1st fetch
    fetch--)API: GET random image
    Component-->>cleanup: unmount
    cleanup-->>fetch: sets fetch 1 `isRan` to true
    Component->>useEffect: 2nd mount executes
    useEffect->>fetch: 2nd fetch
    fetch-)API: GET random image
    note right of fetch: 1st and 2nd response<br/> resolve after 2nd mount
    API--)fetch: 1st response
    rect lightgray
    fetch --xfetch: `isRan === true`<br/>drop 1st response
    end
    API-)fetch: 2nd response
    fetch->>img state: `isRan !== true` <br/>response 2 updates img state
    img state->>Component: state update requests re-render
    Component->>UI: dog picture appears
```

When the app is loaded it mounts the component which then executes its `useEffect`. This sends off a fetch request and returns a cleanup function. As soon as the component mounts, it is unmounted, firing the returned cleanup function which updates `isRan` to be true. The component then mounts again, causing `useEffect` to run, sending a second network request. After the API returns the first response, it's dropped because its instance of `isRan` is true. React uses the second request because useEffect does not run the cleanup function so its instance of `isRan` is still false. This allows the fetch to set the state variable. This finally gives us a cute puppy to look at!

Of course, seeing only one dog is boring! Let's add a button that will load a new random dog picture each time it is clicked. Unfortunately, because of the internal dependency `isRan`, the first fetch request cannot be re-used. We want to create another request for the button.

```jsx
//...component code
  const fetchNewDog = async () => {
    try {
      const resp = await fetch(url);
      if (!resp.ok) {
        throw new Error(resp.status);
      }
      const data = await resp.json();
      if (data.status != "success") {
        throw new Error(data.status);
      }
      setImg(data.message);
    } catch (error) {
      setError(error.message);
    }
  };
  //component code...
  return (
    <main>
      <button style={{ display: "none" }} onClick={fetchNewDog}>
        Another Please!
      </button>
      {error && <p>{error}</p>}
      <img src={img} alt="" />
    </main>
  );
}
```

We can then update the JSX with the new button. `fetchDog` can be passed to the button's onClick handler props.

![clicking button loads another dog image](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/another.gif)

#### Loading CTD-Swag with Product Data from an API

Let's get back to CTD and load the product list in from the API.

A copy of the backend API can be found in here: [Code-the-Dream-School/ctd-swag-backend](https://github.com/Code-the-Dream-School/ctd-swag-backend). Instructions on how to set up and run the server are found in the project's readme. You will need to have a postgres database running locally - plenty of guides to set this up can be found online - choose one for your specific operating system.

> [!warning]
> Avoid putting API or authentication data directly into an application. These values, referred to as environmental variables, change between environments (local, staging, production, etc.) and some information such as credentials or API tokens are sensitive information that we do not want to publish with our repo. It's very difficult to remove these values out of version control history if accidentally committed!

Assuming that the API is in place, the first step is to create a configuration file, `.env.local`, at the top level directory of CTD-Swag. After it is created, confirm that there is a `*.local` entry in the `.gitignore` so that version control is not tracking this file. As a Vite-specific security precaution, all variable keys must have the prefix `VITE_` to be available for use inside our code. For now, all we need is a `VITE_API_BASE_URL="localhost:8641"`. This is the URL that forms the base of our fetch request. We access that value using `import.meta.env.VITE_API_BASE_URL`.

Now that we can access the base url, we update `useEffect` to get the list of products. We don't need to worry about discarding one of the responses since the request should contain the same data between API calls.

Here's the updated `useEffect`:

```jsx
//App.jsx
//... component code
const baseUrl = import.meta.env.VITE_API_BASE_URL;

useEffect(() => {
  (async () => {
    try {
      const resp = await fetch(`${baseUrl}/products`);
      if (!resp.ok) {
        throw new Error(resp.status);
      }
      const products = await resp.json();
      console.log(products);
      setInventory([...products]);
    } catch (error) {
      console.error(error);
    }
  })();
}, []);
//component code...
```

### UI Update Strategies

Before we plan any more changes, we need to consider how to manage UI state as the application interacts with an API. Network operations, even on a fast connection, are not instantaneous. They take milliseconds to seconds to resolve. While that does not sound like much, it's enough time to to frustrate users depending on what they are doing and what feedback they expect the app. We need to address the state of the application between the time when a user commits a change and when it is actually processed by a back end.

This _intermediate_ state affects both the perceived performance and the users ability to rely on highly reliable representation of state. When do we want to ensure that the information displayed is accurate and when do we prefer the UI to be fast? In other words: do we want to make sure that the data is saved first before updating the UI, or should we update the UI immediately based on a user event and then save changes to the API's backend afterwards? The first approach is known as a "pessimistic" UI update strategy while the second is "optimistic". Setting the emotional tone of each word aside, they describe how we prioritize data and state synchronization. Each has its advantages and disadvantages.

#### Pessimistic

A pessimistic strategy is based on the assumption that there is a possibility of operation failure and places an emphasis on making sure a system update is successful before updating the UI. This approach requires that the state of the UI accurately reflects the data as it is stored on the back end. Rather than speculating about the success of a transaction, we wait for the API to send a success or failure response and then use that response's payload to update application state.

For example from life is transferring money between two accounts using a banking app. When a user submits the transfer request, the interface doesn't automatically update account balances. Instead, it waits for the bank's server to process the request and send a response back. Usually during this time, the UI may show a spinning wheel or loading bar that lets the user know that the transaction is still pending. When a response is received it will contain updated account balance information that the application then uses to update state.

This strategy is much easier of the two to implement. The implementation details are usually formulaic and result in compact, easy to read code. A typical breakdown of state change operations using the pessimistic strategy:

1. A user triggers an event that requires an API response
2. Relevant data is gathered and inserted into a fetch request
3. The request is sent off to an API. Note: This pauses any updates to the UI that depend on the values being updated
4. The API server formulates a response containing data up update the UI state
5. The server responds with an update.
6. The response data is applied to state.

##### Advantages

- **Accurate State**: The UI's state accurately reflects the data saved on the back end
- **Simplicity**:Simpler to implement than optimistic strategies

##### Disadvantages

- **Slow**: Some UI operations feels sluggish to users even on fast internet connections

##### Best Used For

- **Data Integrity**: Maintaining the integrity and accuracy of data displayed in the UI, ensuring that updates are finalized and reflected accurately after successful confirmation from the backend.
- **Risk Mitigation**: When the impact of displaying incorrect or incomplete information in the UI is significant, a pessimistic approach reduces the risk of misleading users with incomplete or erroneous data.
- **Form Submissions**: In scenarios where form submissions require validation from the server before proceeding, especially in cases involving sensitive data or multi-step processes.
- **Compliance Requirements**: For applications subject to regulatory compliance or audit requirements, a pessimistic strategy helps enforce strict data validation and auditing controls.
- **Concurrency Control**: When dealing with shared resources or collaborative editing scenarios, pessimistic locking mechanisms help prevent conflicts and ensure data consistency.
- **Code Simplicity**: When the application's developer(or team) prioritizes code simplicity so that features can be rapidly implemented or refactored with relative ease.

#### Optimistic

The Merriam-Webster dictionary provides a definition for optimism as "an inclination to put the most favorable construction upon actions and events or to anticipate the best possible outcome". When approaching a UI change optimistically, we prioritize a responsive UI assuming that most of the API interactions will succeed. The smoothness of operation is also worth the effort that comes with complex error handling and recovery.

We handle errors just like when using the pessimistic strategy but also need to be able to roll back intermediate state changes. For example, a user of a social media app hits the like button on a post. A thumbs-up, smiley face, heart, or some other icon shows up in a reactions area along with other people's reaction icons. This happens instantaneously in the UI, giving the user feedback that indicates they've liked the post and can continue skimming their feed. Meanwhile, the app sends off a request to the social media platform's API with data needed for the backend to update the post. If all goes well, nothing else needs to be done and the user is already engaged in other tasks. The UI is already updated and reflects the data stored on the API.

However, if there's a failure while processing the request, the app needs to undo changes to the UI and notify the user that there was an issue. The code to implement this can get very complex. Depending on the application, developers tend to rely on specialized frameworks or libraries to manage state synchronization between the front end and back end. We will not be using any libraries and keep any examples as simple as possible.

##### Advantages

- **Perceived Performance**: Provides instant feedback to users without waiting for server responses, enhancing the perceived speed of the application.
- **Improved Responsiveness**: Enhances user experience by creating a smoother and more responsive interface.
- **Offline Support**: Enables functionalities even when offline by immediately updating the UI, with actual data sync happening later.

##### Disadvantages

- **Reconciliation of Server Responses with Local State**: Requires careful handling to ensure a seamless user experience and data integrity.
- **Data Consistency**: Risk of inconsistency if the optimistic update doesn't match the actual server response.
- **Recovery From Errors**: Handling errors becomes more complex as you need to manage discrepancies between the optimistic update and server response.

##### Best Used For

- **Highly Responsive Interfaces:** When user experience and perceived speed are crucial, such as interactive messaging platforms or collaborative editing tools.
- **Non-critical Data Operations:** Suitable for non-critical operations where occasional inconsistencies are acceptable and can be resolved later without significant impact on user experience.
- **Offline Functionality:** Useful for scenarios where users need to perform actions offline and sync data later, like note-taking apps or task managers.

#### A Note on Transient State

With the the ability to save data to a server, it's important to remember that state changes which are limited to the UI do not create data worth saving. These temporary UI state values should not be synchronized with the back end. Here are a few examples:

1. **Form Field Validation State:** Flags indicating the validity of user input in client-side form validation.
2. **Sorting or Filtering Preferences:** User-defined sorting or filtering options applied to view data on the client side, such as changing the order of displayed items in a list.
3. **UI Theme Preferences:** User selected themes, color schemes, or layout preferences for the interface that enhance the user experience but are specific to individual users and do not affect shared data. _This depends on the importance placed on user experience customization for the specific app._
4. **Local Notifications:** Displaying temporary notifications or alerts within the app for user feedback.
5. **Client-Side Calculations:** Intermediate results or calculations performed in the UI for dynamic updates or real-time feedback.

#### Planning User and Cart Features in CTD-Swag

##### Identify Development Tasks

> [!note]
> Most of the time this sort of information is brainstormed as a team so you'll rarely have to do this on your own as a junior developer.

After learning about asynchronous operations and how to synchronize application state with an API, we're ready to make some expansions to CTD-Swag. As we build out each new feature, we have to determine which strategy we to use. To do so we start by listing the tasks a user should be able accomplish in the UI after the new feature is implemented. Then need to consider what role state plays while a user interacts with the application. To help with the thought process we take an educated guess about what key tasks that we can anticipate while developing the feature. From there we'll determine if it needs optimistic or pessimistic UI features.

- **An existing user needs to be able to sign in**
  - an authentication form needs to be displayed by hitting a login button
    - the login form can be re-used by conditionally displaying certain fields and the correct button
  - a POST request needs to be sent to `/login`
  - on a successful API response, the user is logged in (this will be same data as sign up response )
    - response data will include user info:
      - account info, excluding password
      - user id - needed to create a fetch request to GET cart
      - JWT authentication token needs be saved as an HttpOnly cookie
- **A new user needs to be able to sign up for an account.**
  - a user details form needs to be displayed by a hitting a sign up button
  - a POST request needs to be sent to `/users` on submit
  - on a successful API response, the user is logged in
    - response data will include user info:
      - account info, excluding password
      - user id - needed to create a fetch request to GET cart
      - JWT authentication token saved as HttpOnly cookie
- **A user's cart should be saved to their account if they are logged in**
  - when user is signed in, a fetch GETs the user's shopping cart.
  - when user adds item to cart
    - cart in UI should update automatically
    - PUT request containing the full cart is sent to API
      - success is silent
      - failure rolls back cart change and displays error message to user
- **A user submitting the cart update expects the cart to reflect the updated as it's represented on the server.**
  - users expect to wait for remote confirmation when submitting a form
  - only local state is updated while making changes
  - when user submits the form, a PUT request containing cart is sent to API
  - a successful cart update response replaces the cart in the app state which automatically updates state managed locally on the form
  - when the back end reports a failure of that operation
  - UI rolls local state back to the previous app state before user edits (this is the same way the current cancel button works)
  - display error message

##### Matching Actions to UI Updating Strategy

###### Pessimistic

- **An existing user needs to be able to sign in.**
- **A new user needs to be able to sign up for an account.**
- **User cart edits should update the user's saved cart when the user confirms the updates.**

All of the data changes from the above tasks can be considered major operations. A user record is needed to save user info to the server so it must exist before they are able to log in. For a user to log in or resume a previous session, their user information and cart contents must be loaded on the front end before populating UI elements. The cart edits also need to be confirmed before updating the interface. Since this is done through a form, the user already expects a save confirmation from the back end before changes are finalized. For these features to behave in a desired manner where we are emphasizing data accuracy, we will take a pessimistic approach.

###### Optimistic

**A logged in user's cart items should be saved to their account**

We want to avoid making a user having to wait to take further actions after adding an item to their cart. Delaying a user to make sure the item has been added to the stored cart is a detriment to the shopping experience. The increased wait time a user must endure decreases the likeliness that they will add more items to their cart. This is definitely not good for the user or the store! To keep the shopping experience pleasant, an item it should immediately appear in the cart while the app is synchronizes cart information in the background. If the API fails for any reason, we will need to revert the state changes. An emphasis on user experience indicates we should adopt an optimistic strategy for this feature. Luckily, our state recovery mechanism is already implemented!

##### Implementing User and Cart Features

We are now ready to expand the functionality of CTD Swag. The discussion will focus on state updates and API synchronization using fetch and how they relate to intermediate UI state. In week 11, we'll cover some more advanced state management techniques that will help simplify UI state updates. For now, we will stick with the `useState` hook.

##### Log in an Existing User

For this task we need to create a form that accepts an email and password. Since we don't want to clutter the interface any further, we will add a Log In button beside the cart that toggles the visibility of a dialog containing the auth form. We will use a state variable, `isAuthFormShown` to control this dialog's visibility.

![submitting auth form](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login3.gif)

We next consider the POST request that we send to authenticate. An initial request looks like:

```jsx
// extract from App.jsx
//...component code

async function handleAuthenticate(credentials) {
  const options = {
    method: 'POST',
    body: JSON.stringify(credentials),
    headers: { 'Content-Type': 'application/json' },
  };
  try {
    const resp = await fetch(`${baseUrl}/auth/login`, options);
    if (!resp.ok) {
      //status will be 401 if authentication fails
      //we want to handle it differently than other errors
      if (resp.status === 401) {
        console.dir(resp);
      }
      throw new Error(resp.status);
    }
    console.dir(userData);
  } catch (error) {
    console.dir(error);
  }
}
//...continued component code
```

This function is passed through props to the login form and fired off with an event handler that also prevents the page from reloading:

```jsx
// extract from src/features/Auth/AuthForm.jsx

import { useState } from 'react';
import ctdLogo from '../../assets/icons/mono-blue-logo.svg';

function AuthForm({ handleCloseAuthForm, handleAuthenticate }) {
  const [email, setEmail] = useState('dev@test.com');
  const [password, setPassword] = useState('password123');

  function handleSubmit(e) {
    e.preventDefault();
    handleAuthenticate({ email, password });
  }
  return (
    <>
  <div className="authFormScreen"></div>
     <form className="authForm">
{/*continued component code...*/}
  <div className="authButtons">
          <button
            disabled={!email || !password}
            type="button"
            onClick={handleSubmit}
          >
            Submit
          </button>
          <button type="button" onClick={handleCloseAuthForm}>
            Cancel
          </button>
        </div>
        {/*continued component code...*/}
```

With that in place with a few `console.dir` statements, we get the auth response's payload logged to the console.

Here's the current UI appearance for successful login and failed login:

![failed login attempt](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-error.gif)
Here are the relevant console outputs so that we can determine how to integrate it to our existing state:

```javascript
// successful login
Object {
 cartItems: Array [ {…}, {…} ],
 message: "Login successful",
 token: "<<secret-token-string>>";
 user: Object { id: 19, firstName: "First", lastName: "Last", … };
 // continued...
​}

// failed login
Response {
 url: "http://localhost:8641/auth/login",
 status: 401,
 ok: false,
 statusText: "Unauthorized",
 ​​// continued...
}

```

When the user hits the submit button, it initiates the POST request and the UI should display that the system is processing the login. When we get the success message from the server, we'll close the login form and use the API response to populate the user's cart. If the API informs us that a login attempt failed we will show an appropriate message in the modal and give the user a chance to log in again.

We now have to consider intermediate state. To indicate when the app is waiting for a response, we create a boolean state variable `isAthenticating`. We also create another state variable, `user` to hold the user's information while they are logged in. We'll tell whether a user is logged in when it is no longer an empty object. Finally, we need to be able to display an error if there is an authentication issue.

```jsx
//extract from App.jsx
const [isAuthenticating, setIsAuthenticating] = useState('false');
const [user, setUser] = useState({});
const [authError, setAuthError] = useState('')'
```

From these three state variables we can display content based on the four following UI conditions for before, during, and after a user logs in:

- **not authenticated** - We want to display a log in button beside the cart.
- **authenticating** - We want to display an in-progress message and a spinner while authentication is in progress.
- **authenticated** - We want to change the "login" button to "logout" and display an account name near the cart.
- **failed authentication** - We want to display a dismissible message about failed log in attempts.

> [!danger]
> Error messages should be helpful for the user but not too specific. Sharing too many details about system errors can pose a security risk. For example, if we let the user know if it was the email or password that caused authentication to fail, a malicious hacker can test a list of email addresses to see if they match any user that is registered in the system. It's better in this case to limit the message to whether logging was successful or not. Most of the time, this is taken care of on the back end but front-end developers need to be careful not to expose exploitable data.

In the updated `handleAuthenticate`, when the fetch requests initiates, it changes `isAuthenticating` to `true`. Whenever it resolves `isAuthenticating` is set back to `false`. A successful login will set the user's value with the API response. If there is an error, `authError` is set with the API's error message.

```jsx
//extract from App.jsx
async function handleAuthenticate(credentials) {
  const options = {
    method: 'POST',
    body: JSON.stringify(credentials),
    headers: { 'Content-Type': 'application/json' },
  };
  try {
    setIsAuthenticating(true);
    const resp = await fetch(`${baseUrl}/auth/login`, options);
    if (!resp.ok) {
      if (resp.status === 401) {
        setAuthError(resp.statusText);
      }
      throw new Error(resp.status);
    }
    const userData = await resp.json();
    //assigning an new object that's more convenient to work with
    setUser({ ...userData.user, token: userData.token });
    setIsAuthenticating(false);
  } catch (error) {
    console.log(error.message);
  }
}
```

Finally, we use those state values to update the content in the UI. We'll take care of items that use intermediate state - loading message and the spinner. We pass `isAuthenticating` to the auth form. Using conditional rendering, we'll toggle between the form and the loading elements. If the authentication is successful, we'll also close out the form dialog window.

![loading spinner shows briefly during login](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login2.gif)

That quick state change makes it challenging to work with html elements and styling if you forget to take advantage of React's dev tools. In the Components tab of the browser's dev tools, you are able to see and change state values in all of the components rendered on the page. We can find the one that toggles the dialog's loading message and set it to true. This results in the loading elements staying on screen.

![alt](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-spinner.gif)

With this open, we can also insert and position the error message to the bottom of the dialog. Remember to reset `authError` to an empty string if the user successfully logs in or they close the dialog out. We don't want that value persisting in the application.

![login dialog shows error message on bad login attempt](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-error.gif)

Our final step is to update UI content for the user based on the `user` state variable. This includes populating the cart, changing the login button to log out (text and functionality), and adding the welcome message.

##### Setting up Cart

One of the key features of adding users and carts is to populate the cart. Luckily, this is a simple matter. Recall that in the console statements, the response object contained an `cartItems` entry. We don't have to go through the awkward transformation of product items into cart items again. Our API is set up to deliver the cart items so we can add them directly into the cart. We can take a closer look at that fetch response but this time in the browser's Network tab.

![pointing out server-based properties in cart items](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/cart-state-server-fields.png)

We just have to confirm that the variable names for each property matches the corresponding value in the response response (eg: `cart` vs `cartList` vs `cartItems`). We can then destructure the cart items directly into the the cart's state updater function to update the cart.

```js
// extract from App.jsx
//...code
async function handleAuthenticate(credentials) {
  const options = {
    method: 'POST',
    body: JSON.stringify(credentials),
    headers: { 'Content-Type': 'application/json' },
  };
  try {
    setIsAuthenticating(true);
    const resp = await fetch(`${baseUrl}/auth/login`, options);
    if (!resp.ok) {
      if (resp.status === 401) {
        setAuthError('email or password incorrect');
      }
      throw new Error(resp.status);
    }
    const userData = await resp.json();
    // this is a LOT of state update functions in a row!!
    // we fix this in week 11
    setUser({ ...userData.user, token: userData.token });
    setCart([...userData.cartItems]);
    setAuthError('');
    setIsAuthenticating(false);
    setIsAuthFormOpen(false);
  } catch (error) {
    setIsAuthenticating(false);
    console.log(error.message);
  }
}
//code continues...
```

###### Updating Button and Adding Welcome Message

We pass the Header additional props:

```jsx

{/*extract from App.jsx*/}
{/*...component code*/}
  function handleLogOut() {
    setUser({});
    setCart([]);
  }

  return (
    <>
  <Header
   cart={cart}
   handleOpenCart={() => setIsCartOpen(true)}
   handleOpenAuthForm={() => setIsAuthFormOpen(true)}
   //new props
   handleLogOut={handleLogOut} //wipes out the user and cart values
   user={user} //used to tell if user logged in
  />
{/*component code continues...*/}
```

In the Header component, we determine there is a user by looking for the existence of `user.id` which will only be set if there is a user logged in. There are better approaches to solving this problem but we'll cover that in week 11 when we address advanced state topics. We'll also use conditional rendering to toggle between the existing log in button and a new sign out button.

```jsx
{
  /*extract from Header.jsx*/
}
{
  /*...code*/
}
<div className="userActions">
  {user.id ? (
    <>
      <span>Hi, {user.firstName}</span>
      <button className="authButton signOut" onClick={handleLogOut}>
        Sign out
      </button>
    </>
  ) : (
    <button className="authButton" type="button" onClick={handleOpenAuthForm}>
      Log in
    </button>
  )}
</div>;
{
  /*code continues...*/
}
```

Here is the updated UI functionality for logging in:

![cart loads at login](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-cart-loaded2.gif)

##### Sign Up New User

Signing up a new user uses the same pessimistic UI strategy that logging in employs. Since we already saw an example of a pessimistic approach, we'll just summarize the changes for signing up a new user. We first added a register button beside login.

![login and register buttons](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-register-buttons.png)

Both buttons call the same handler, `handleOpenAuthDialog`, to open the dialog, but we also set an `isRegistering` state variable that we use to show either the `LoginForm` or the `RegisterForm` form in the `AuthDialog`. We then extract the login form from the into its own `LoginForm` component and then create a `RegisterForm` component.

![toggling between auth and login dialogs](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-auth-dialogs.gif)

We then create a new POST fetch request to the `/auth/register` endpoint. If all goes well with the registration, then we use the response to set the `user` state value, completing a successful registration and login. We handle any registration errors by displaying messages in the form with the same `authError` state value the login form uses.

![user registers and logs in](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/register-form.gif)

###### Sync Cart Form Update

Our API's `/cart` endpoint incudes a PATCH method. Our response include the auth token in the Authorization header and a stringified version of the workingCart state value. We'll adapt the handler function from the existing confirm update button to make our request. On a successful response, we then set the current `cart` value that propagates to all components dependent on that data.

Note the flicker in the gif below - this is caused by the brief change of `workingCart` to the previous `cart` value before the API response completes. When the cart is updated from the fetch response, that `workingCart` is being changed again.

![alt](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/confirm-update-temp-reversion.gif)

We can prevent that rapid change by relying on a state boolean, `isCartSyncing`. While it's set to true, we prevent the `useEffect` in the `Cart` component making any state updates to `workingCart`. When the response resolves, we toggle `isCartSyncing` so that `Cart` can resume syncing its `workingCart` with the global `cart`.

```js
// extract from Cart.jsx
//...component code
//resets `workingCart`
useEffect(() => {
  if (isFormDirty || isCartSyncing) {
    //prevents setWorkingCart from running
    return;
  }
  setWorkingCart(cart);
}, [cart, isFormDirty, isCartSyncing]);

//code continues...
```

After adding the appropriate intermediate state variables to configure the display of our components, we have the following function that synchronizes the cart with the API:

```js
// extract from App.jsx
//...code
async function handleSyncCart(workingCart) {
  if (!user.id) {
    setCart(workingCart);
    return;
  }
  setIsCartSyncing(true);
  const options = {
    method: 'PATCH',
    body: JSON.stringify({ cartItems: workingCart }),
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${user.token}`,
    },
  };
  try {
    const resp = await fetch(`${baseUrl}/cart`, options);
    if (!resp.ok) {
      console.log('resp not okay');
      if (resp.status === 401) {
        throw new Error('Not authorized. Please log in.');
      }
      const cartData = await resp.json();
      //cartData.error on all other errors from this endpoint
      if (cartData.error) {
        throw new Error(cartData.error);
      }
      //catch-all
      throw new Error('Error occurred while syncing');
    }
    const cartData = await resp.json();
    setCart([...cartData]);
    //clean up state variables
    setIsCartSyncing(false);
    setCartError('');
  } catch (error) {
    setCartError(error.message);
    setIsCartSyncing(false);
  }
}
//code continues...
```

On review of that function, we can see a pattern emerge from how we tie together API requests with our application's state:

1. we set up intermediate state
2. set fetch's options
3. send request
4. handle expected errors
5. update global state with the API response
6. reset intermediate state
7. handle unanticipated errors

And here is the final cart behavior!

![logs in and opens loaded cart](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/login-cart-loaded.gif)

Note how pessimistic strategy of UI updates doesn't make the experience _bad_ or _slow_ as long as it's applied to major operations. In the state updates that we have done so far, making the user wait for the back end has only a part of confirmation tasks. In the last feature planned, making the user to wait while adding items to the cart would be detrimental to the shopping experience.

###### Add Items (With an optimistic UI strategy)

Our final feature saves a logged in user's shopping cart items to their account. The optimistic approach applies changes to the UI and then saves the change to the backend. Depending on our needs, we either ignore successful responses back or swap out a placeholder value with the API response data. If there is an error, we will need to recover from it. For this feature, we'll ignore successful responses and, if there's an error, de-increment the cart item's quantity and then show an error message.

To get started, we add the following to the existing `handleAddItemToCart` function:

```js
// extract from App.jsx
//...code

async function handleAddItemToCart(id) {
//...existing function code
    if (!user.id) {
  //exit out of function to prevent anon fetches
  return;
    }
    //API accepts only these fields
    const payload = {
      userId: updatedCartItem.userId,
      productId: updatedCartItem.productId,
      quantity: updatedCartItem.quantity,
    };
    const options = {
      method: 'POST',
      body: JSON.stringify(payload),
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${user.token}`,
      },
    };
    try {
      const resp = await fetch(`${baseUrl}/cart`, options);
      if (!resp.ok) {
  console.log(resp);
  const failData = await resp.json()
  console.dir(failData);
      }
    } catch (error) {
      console.log(error.message);
      //TODO code to de-increment item count here
    }
//code continues...
```

From here, we start looking to the error responses generated if the response is not okay. Each console statement examines the data as it's processed. We'll send a response with a bad token to observe a 401 response. This is coming from the `console.dir` on line 29 of the code extract above:

```console
Response { type: "cors", url: "http://localhost:8641/cart", redirected: false, status: 401, ok: false, statusText: "Unauthorized", headers: Headers(2), body: ReadableStream, bodyUsed: false }
App.jsx:176:16
```

We'll simulate a POST response that send a bogus productId to the backend:

```console
Response { type: "cors", url: "http://localhost:8641/cart", redirected: false, status: 500, ok: false, statusText: "Internal Server Error", headers: Headers(2), body: ReadableStream, bodyUsed: false }
App.jsx:176:16
```

We'll want to show a "not logged in" message for an authorization error and a generic error, "cart failed to save" for everything else. We do this in the `if (!resp.ok){}` block with a new `cartItemError` state variable and toggle a dismissible message dialog using `setIsDialogOpen`.

```js
// extract from App.jsx
//...code
if (!resp.ok) {
  if (resp.status === 401) {
    setCartItemError(
      'Your item could not be saved. Log out and log back in again to continue',
    );
  } else {
    setCartItemError('Cart failed to save');
  }
  setIsDialogOpen(true);
}
//code continues...
```

![error saving item](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/saving-error.png)

![error confirming cart update](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/cart-saving-error.png)

After setting up the alert dialog, we finally need to revert the cart item to its state prior to adding it to the cart. We still have access to target list item so we can decrement its quantity. If the quantity goes down to 0, we then remove it from the cart entirely.

```js
// extract from App.jsx
//...code

if (updatedCartItem.quantity === 1) {
  setCart([...cart.filter((item) => item.productId !== id)]);
} else {
  const revertedCartItem = {
    ...updatedCartItem,
    quantity: updatedCartItem.quantity - 1,
  };
  if (revertedCartItem) {
    setCart([
      ...cart.filter((item) => item.productId !== id),
      revertedCartItem,
    ]);
  }
}
//code continues...
```

As seen in the gif below, the cart number updates immediately. When an error response is received, it reverts back to its previous value. This lets us know the item has been removed.

![cart displaying error dialog](https://raw.githubusercontent.com/Code-the-Dream-School/react-curriculum-v3/refs/heads/main/learns-app-content/lessons/assets/week-07/cart-saving-error.gif)


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