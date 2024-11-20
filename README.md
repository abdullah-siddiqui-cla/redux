# Task 1 - Using Redux Store
## Objective
We’ll build a feature to manage a list of posts where users can mark or unmark posts as favorites. This will involve:

- Setting up Redux Toolkit.
- Creating a slice for posts.
- Implementing functionality to toggle a post’s "favorite" status.
- Connecting Redux to React components.


## Steps

## 1. Install Redux Toolkit
If Redux Toolkit is not already installed, run:
`npm install @reduxjs/toolkit react-redux`

## 2. Create a Redux Slice for Posts
### Explain:
A slice is a container for Redux state logic. It includes the initial state, actions, and reducers. Here, we’ll manage a list of posts with an isFavorite property.

### Guide:
- Create a new file called `postsSlice.js` in the src folder.
- Create a the posts slice in it using `createSlice` from redux toolkit. Import it like `import { createSlice } from "@reduxjs/toolkit";`

## 3. Configure the Redux Store
### Explain:
The store is the central place where all Redux state lives. We’ll add the posts slice reducer to the store.

### Guide:
- Create a `store.js` file in the src folder.
- Add the following code:
```
import { configureStore } from "@reduxjs/toolkit";
import postsReducer from "./postsSlice";

export const store = configureStore({
  reducer: {
    posts: postsReducer, // Add the posts reducer to the store
  },
});
```

### Explain:
- `configureStore` is a helper function to set up the Redux store easily.
- The `postsReducer` manages the posts state.

## 4. Provide the Store to the App
### Explain:
To use Redux in a React app, we need to wrap our application with the `Provider` component from React Redux.

### Guide:
- Open `index.js` (or `main.jsx` for Vite projects).
- Wrap the `<App />` component with the `Provider` and pass the store to it

```
...
import { Provider } from "react-redux"; // Import provider
import { store } from "./store"; // Import the store
...

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

### Explain:
The Provider makes the Redux store available to all components in the app.

## 5. Build the Posts Component
### Explain:
The `Posts` component will:
- Display a list of posts.
- Allow users to mark/unmark posts as favorites using a button.
- Allow users to add/remove ports using add and remove buttons

### Guide:
- Create a Component for `Posts`
- Use `useSelector`, `useDispatch` provided by redux toolkit to access the redux state and dispatch reducer actions. Import them with `import { useSelector, useDispatch } from "react-redux";`
- Import actions from the posts slice like `import { toggleFavorite } from "./postsSlice";`

```
import React from "react";
import { useSelector, useDispatch } from "react-redux"; // Import the useSelector and useDispatch
import { toggleFavorite } from "./postsSlice"; // Import actions from the redux slice

const Posts = () => {
  // Access posts state from the Redux store
  const posts = useSelector((state) => state.posts.posts);

  // Get dispatch to send actions
  const dispatch = useDispatch(); 

  const handleToggleFavorite = (id) => {
    // Dispatch the toggleFavorite action
    dispatch(toggleFavorite(id));
  };

  return (
    ...
  );
};

export default Posts;
```

### Explain:
- `useSelector`: Reads the `posts` state from the Redux store.
- `useDispatch`: Sends the `toggleFavorite` action to the store.

# Task 2: Fetching Posts Dynamically
## Objective
Fetch posts from an API (https://dummyjson.com/posts) and load them into the Redux store using a simple asynchronous action with `createAsyncThunk`.

## Steps
### 1. Update the Posts Slice
We will add a new `loadPosts` action that fetches posts from the API and updates the posts state. For hint: See the below code snippet,

```
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";

// Async thunk to fetch posts from the API
export const loadPosts = createAsyncThunk("posts/loadPosts", async () => {
  const response = await axios("https://dummyjson.com/posts");
  return response.data.posts; // Return the array of posts
});

const postsSlice = createSlice({
  ...,
  extraReducers: (builder) => {
    // This the callback which is executed loadPosts thunk is fulfilled
    builder.addCase(loadPosts.fulfilled, (state, action) => {
      // action.payload contains the posts loaded
      // Load `action.payload` into `state`
    })
    .addCase(loadPosts.rejected, (state, action) => {
      // This the callback which is executed loadPosts thunk is rejected
    }).addCase(loadPosts.pending, (state, action) => {
      // This the callback which is executed loadPosts thunk is in pending
    });
  },
});

```

### 2. Dispatch loadPosts in the Posts Component
- Update the `Posts` component to fetch posts when it mounts using `loadPosts` action
