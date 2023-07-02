<script>
  import Router from "svelte-spa-router"
  import Navigation from "./components/Navigation.svelte"
  import Home from "./routes/Home.svelte"
  import Post from "./routes/Post.svelte"
  import Qna from "./routes/Qna.svelte"
  import Community from "./routes/Community.svelte"
  import UserCreate from "./routes/UserCreate.svelte"

  const routes = {
    "/": Home,
    "/post/:id_post": Post,
    "/qna": Qna,
    "/community": Community,
    "/user-create": UserCreate,
  }
</script>

<Navigation />
<Router {routes}/>