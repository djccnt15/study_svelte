<script>
  import Router from 'svelte-spa-router'
  import Home from "./routes/Home.svelte"
  import Post from "./routes/Post.svelte"
  import UserCreate from "./routes/UserCreate.svelte"

  const routes = {
    '/': Home,
    '/post/:id': Post,
    '/user-create': UserCreate,
  }
</script>

<Router {routes}/>