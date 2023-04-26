<script>
  import Router from 'svelte-spa-router'
  import Board_qna from "./routes/Board_qna.svelte"

  const routes = {
    '/': Board_qna,
  }
</script>

<Router {routes}/>