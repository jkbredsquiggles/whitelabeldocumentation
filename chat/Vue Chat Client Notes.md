## Data Reactivity

This is the first feature of VV that needs UI reactivity

- e.g. When a new message is added to a conversation, the UI needs to detect this and automatically re-render (without reloading the entire page)
- injecting chatService (which holds the data) into components will not provide reactivity on its deeper properties
- so I'm not using "inject" for things that need to be reactive (ChatService and its data)
- we need to use "data" property of Vue to listen to the chatService's deeper data, instead of "inject"
- This pattern is used throughtout the chat component

https://forum.vuejs.org/t/computed-property-not-updating/21148/6

## Typescript

The chatService and new chat-related components are written in typescript.

- typescript is now optionally supported in the Vue app, for any `.ts` files or `.vue` files
- when declaring variables/parameters, add `: <type>` to its right, where `type` is the variable/parameter's type.
