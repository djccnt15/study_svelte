<script>
  import Router from "svelte-spa-router"
  import Navigation from "./components/Navigation.svelte"
  import Home from "./routes/Home.svelte"
  import Post from "./routes/Post.svelte"
  import Qna from "./routes/Qna.svelte"
  import Community from "./routes/Community.svelte"
  import PostCreate from "./routes/PostCreate.svelte"
  import UserCreate from "./routes/UserCreate.svelte"
  import UserLogin from "./routes/UserLogin.svelte"
  import PostUpdate from "./routes/PostUpdate.svelte"
  import CommentUpdate from "./routes/CommentUpdate.svelte"

  const routes = {
    "/": Home,
    "/post/:id_post": Post,
    "/qna": Qna,
    "/community": Community,
    "/post-create": PostCreate,
    "/user-create": UserCreate,
    "/user-login": UserLogin,
    "/post-update/:id_post": PostUpdate,
    "/comment-update/:id_comment": CommentUpdate
  }
</script>

<Navigation />
<Router {routes}/>