<script>
  import Router from "svelte-spa-router"
  import Navigation from "./components/Navigation.svelte"
  import Home from "./routes/Home.svelte"
  import Post from "./routes/Post.svelte"
  import UserCreate from "./routes/UserCreate.svelte"

  const routes = {
    "/": Home,
    "/post/:id_post": Post,
    "/user-create": UserCreate,
  }
</script>

<Navigation />
<Router {routes}/>