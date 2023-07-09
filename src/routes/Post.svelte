<script>
  import fastapi from "../lib/api"
  import Error from "../components/Error.svelte"
  import moment from "moment";
  moment.locale("ko")

  export let params = {}

  let id_post = params.id_post
  let post_detail = {
    post: {},
    content: {},
    category: {},
    user: {},
  }
  let list_comment = []
  let comment_content = ""
  let error = {detail:[]}

  function get_post() {
    fastapi("get", "/api/board/post/detail", {id_post}, (json) => {
      post_detail = json.post_detail,
      list_comment = json.comment_list
    })
  }

  get_post()

  function create_comment(event) {
    event.preventDefault()
    let url = "/api/board/comment/create?id_post=" + id_post
    let params = {
      content: comment_content
    }
    fastapi("post", url, params,
      (json) => {
        comment_content = ""
        let error = {detail:[]}
        get_post()
    },
      (err_json) => {
        error = err_json
      }
    )
  }
</script>

<div class="container my-3">
  <!-- post -->
  <h2 class="border-bottom py-2">{post_detail.content.subject}</h2>
  <div class="card my-3">
    <div class="card-body">
      <div class="card-text" style="white-space: pre-line;">{post_detail.content.content}</div>
      <div class="d-flex justify-content-end">
        <div class="badge bg-light text-dark p-2">
          작성일: {moment(post_detail.post.date_create).format("YYYY년 MM월 DD일 hh:mm a")}<br><br>
          수정일: {moment(post_detail.content.date_upd).format("YYYY년 MM월 DD일 hh:mm a")}
        </div>
      </div>
    </div>
  </div>

  <!-- comment -->
  <h5 class="border-bottom my-3 py-2">{list_comment.length}개의 답변이 있습니다.</h5>
    {#each list_comment as comment}
      <div class="card my-3">
        <div class="card-body">
          <div class="card-text" style="white-space: pre-line;">{comment.content.content}</div>
          <div class="d-flex justify-content-end">
            <div class="badge bg-light text-dark p-2">
              작성일: {moment(comment.comment.date_create).format("YYYY년 MM월 DD일 hh:mm a")}<br><br>
              수정일: {moment(comment.content.date_upd).format("YYYY년 MM월 DD일 hh:mm a")}
            </div>
          </div>
        </div>
      </div>
    {/each}

  <!-- submit comment -->
  <Error error={error} />
  <form method="post" class="my-3">
    <div class="mb-3">
      <textarea rows="10" bind:value={comment_content} class="form-control" />
    </div>
    <input type="submit" value="답변등록" class="btn btn-primary" on:click="{create_comment}" />
  </form>
</div>