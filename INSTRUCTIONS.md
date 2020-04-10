# Cache Management

## Background

Application state management is arguably one of the hardest problems in
application development. This is evidenced by the myriad of libraries available
to accomplish it. In my experience, the issue is made even more challenging by
overengineering, pre-mature abstraction, and lack of proper categorizing of
state.

State can be lumped into two buckets:

1. UI state: Modal is open, item is highlighted, etc.
2. Server cache: User data, tweets, contacts, etc.

A great deal of complexity comes when people attempt to lump these two distinct
types of state together. When this is done, UI state which should not be global
is made global because Server cache state is typically global so it naturally
causes us to just make everything global. It's further complected by the fact
that caching is one of the hardest problems in software development in general.

We can drastically simplify our UI state management if we split out the server
cache into something separate.

A fantastic solution for managing the server cache on the client is
[`react-query`](https://github.com/tannerlinsley/react-query). It is a set of
React hooks that allow you to query, cache, and mutate data on your server in a
way that's flexible to support many use cases and optimizations but opinionated
enough to provide a huge amount of value. And thanks to the power of hooks, we
can build our own hooks on top of those provided to keep our component code
really simple.

Here are a few examples of how you can use react-query that are relevant for our
exercise:

```javascript
function readTweet(queryKey, {tweetId}) {
  return tweetClient.read(tweetId).then(data => data.tweet)
}

function App({tweetId}) {
  const result = useQuery({
    queryKey: ['tweet', {tweetId}],
    queryFn: readTweet,
  })
  // result has several properties, here are a few relevant ones:
  //   status
  //   data
  //   error
  //   isLoading
  //   isError
  //   isSuccess

  const [removeTweet, state] = useMutation(() => tweetClient.remove(tweetId))
  // call removeTweet when you want to execute the mutation callback
  // state has several properties, here are a few relevant ones:
  //   status
  //   data
  //   error
  //   isLoading
  //   isError
  //   isSuccess
}
```

📜 here are the docs:

- `useQuery`:
  https://github.com/tannerlinsley/react-query/blob/61d0bf85a37b7369067433a68e310bc1a6c23558/README.md#usequery
- `useMutation`:
  https://github.com/tannerlinsley/react-query/blob/61d0bf85a37b7369067433a68e310bc1a6c23558/README.md#usemutation

That should be enough to get you going.

## Exercise

👨‍💼 Our users are anxious to get going on their reading lists. Several already
have some books picked out! We've got the backend and the UI all ready to go.
Your co-worker 🧝‍♀️ even created a `list-items-client.js` for you to interact with
the list items data. Now you need to wire up our UI with that client and we'll
be good to go.

This stuff touches a lot of files, but I'm confident that you can get this
working. Good luck!

### Files

- `./src/utils/books.js`
- `./src/utils/list-items.js`
- `./src/utils/api-client.js`
- `./src/components/status-buttons.js`
- `./src/components/rating.js`
- `./src/components/book-row.js`
- `./src/screens/discover.js`
- `./src/screens/book.js`
- `./src/components/list-item-list.js`,

## Extra Credit

### 💯 Wrap the <App /> in a <ReactQueryConfigProvider />

In the `./src/index.js` file, create a queryConfig object here and enable
`useErrorBoundary` and disable `refetchAllOnWindowFocus`

📜 Learn more about error boundaries:
https://reactjs.org/docs/error-boundaries.html

📜 Learn more about query config:
https://github.com/tannerlinsley/react-query/blob/61d0bf85a37b7369067433a68e310bc1a6c23558/README.md#reactqueryconfigprovider

```javascript
const queryConfig = {
  /* your global config */
}

ReactDOM.render(
  <ReactQueryConfigProvider config={queryConfig}>
    <App />
  </ReactQueryConfigProvider>,
  document.getElementById('root'),
)
```

### 💯 Handle mutation errors properly

Currently, if there's an error during a mutation, we don't show the user
anything. Instead, we should show the error message to the user. You'll need to
pass this to `useMutation` in the `./src/utils/list-items.js` module as the
second argument:

```javascript
const defaultMutationOptions = {
  useErrorBoundary: false,
  throwOnError: true,
}
```

But you may need to disable `throwOnError` in some cases, so have those custom
hooks accept some options and merge the `defaultMutationOptions` with the
options you receive.

For example, the `<NotesTextarea />` component in `./src/screens/book.js` will
need to pass `{throwOnError: false}` and instead it will use the `error` that
comes from `useMutation` to display the error inline. You can use this UI:

```javascript
{
  error ? (
    <span role="alert" css={{color: colors.danger, fontSize: '0.7em'}}>
      <span>There was an error:</span>{' '}
      <pre
        css={{
          display: 'inline-block',
          overflow: 'scroll',
          margin: '0',
          marginBottom: -5,
        }}
      >
        {error.message}
      </pre>
    </span>
  ) : null
}
```

### 💯 Add a loading spinner for the notes

If you made it this far, then you're a real champ. I'm going to let you figure
this one out on your own. Try to add an inline loading spinner to the notes in
`./src/screens/book.js`.

## 🦉 Elaboration and Feedback

After the instruction, if you want to remember what you've just learned, then
fill out the elaboration and feedback form:

https://ws.kcd.im/?ws=Build%20React%20Apps%20%F0%9F%93%98&e=06%3A%20Cache%20Management&em=
