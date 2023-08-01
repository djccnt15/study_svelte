<script>
  import fastapi from "../lib/api"
  import Error from "../components/Error.svelte"
  import { push } from "svelte-spa-router"

  export let params = {}
  const id_comment = params.id_comment

  let error = {detail:[]}
  let comment_detail = {
    comment: {},
    content: {},
    post: {}
  }
  let id_post = ""
  let content = ""

  fastapi("get", "/api/board/comment/detail", {id_comment}, (json) => {
    comment_detail = json
    id_post = comment_detail.post.id
  })

  function update_comment(event) {
    event.preventDefault()
    let url = "/api/board/comment/upd?id_comment=" + id_comment
    let params = {
      content: content
    }
    fastapi("put", url, params,
      (json) => {
        push("/post/"+id_post)
      },
      (json_error) => {
        error = json_error
      }
    )
  }
</script>

<div class="container">
  <h5 class="my-3 border-bottom pb-2">답변 수정</h5>
  <Error error={error} />
  <form method="post" class="my-3">
    <div class="mb-3">
      <label for="content">내용</label>
      <textarea class="form-control" rows="10" bind:value="{content}"></textarea>
    </div>
    <button class="btn btn-primary" on:click="{update_comment}">수정하기</button>
  </form>
</div>