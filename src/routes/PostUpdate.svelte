<script>
  import { push } from "svelte-spa-router"
  import fastapi from "../lib/api"
  import Error from "../components/Error.svelte"

  export let params = {}
  let id_post = params.id_post

  let error = {detail:[]}
  let post_detail = {
    post: {},
    content: {},
    category: {},
    user: {},
  }
  let category = ""
  let subject = ""
  let content = ""

  fastapi("get", "/api/board/post/detail", {id_post}, (json) => {
    post_detail = json.post_detail
    category = post_detail.category.category
    subject = post_detail.content.subject
    content = post_detail.content.content
  })

  function update_question(event) {
    event.preventDefault()
    let url = "/api/board/post/upd/?id_post=" + id_post
    let params = {
      category: category,
      subject: subject,
      content: content,
    }
    fastapi("put", url, params,
      (json) => {
        push("/post/" + id_post)
      },
      (json_error) => {
        error = json_error
      }
    )
  }
</script>

<div class="container">
  <h5 class="my-3 border-bottom pb-2">질문 수정</h5>
  <Error error={error} />
  <form method="post" class="my-3">
    <div class="mb-3">
      <label for="subject">제목</label>
      <input type="text" class="form-control" bind:value="{subject}">
    </div>
    <div class="mb-3">
      <label for="content">내용</label>
      <textarea class="form-control" rows="10" bind:value="{content}"></textarea>
    </div>
    <button class="btn btn-primary" on:click="{update_question}">수정하기</button>
  </form>
</div>