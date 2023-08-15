<script>
  import fastapi from "../lib/api"
  import Error from "../components/Error.svelte"
  import { link, push } from "svelte-spa-router"
  import { is_login, username } from "../lib/store"
  import moment from "moment";
  moment.locale("ko")

  export let params = {}

  let id_post = params.id_post
  let post_detail = {
    post: {},
    content: {},
    category: "",
    category_t1: "",
    user: {},
    count_vote: ""
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
    let url = "/api/board/comment/create?id_post="+id_post
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

  function del_post(_post_id) {
    if(window.confirm("정말로 삭제하시겠습니까?")) {
      let url = "/api/board/post/del"
      let params = {
        id_post: _post_id
      }
      fastapi("delete", url, params,
        (json) => {
          push("/"+post_detail.category_t1)
        },
        (err_json) => {
          error = err_json
        }
      )
    }
  }

  function del_comment(id_comment) {
    if(window.confirm("정말로 삭제하시겠습니까?")) {
      let url = "/api/board/comment/del"
      let params = {
        id_comment: id_comment
      }
      fastapi("delete", url, params,
        (json) => {
          get_post()
        },
        (err_json) => {
          error = err_json
        }
      )
    }
  }

  function vote_post(id_post) {
    if(window.confirm("정말로 추천하시겠습니까?\n이미 추천한 글을 추천하면 추천이 취소됩니다.")) {
      let url = "/api/board/post/vote?id_post=" + id_post
      fastapi("post", url, params,
        (json) => {
          get_post()
        },
        (err_json) => {
          error = err_json
        }
      )
    }
  }

  function vote_comment(id_comment) {
    if(window.confirm("정말로 추천하시겠습니까?\n이미 추천한 댓글을 추천하면 추천이 취소됩니다.")) {
      let url = "/api/board/comment/vote?id_comment=" + id_comment
      fastapi("post", url, params,
        (json) => {
          get_post()
        },
        (err_json) => {
          error = err_json
        }
      )
    }
  }
</script>

<div class="container my-3">
  <!-- post -->
  <h2 class="border-bottom py-2">{post_detail.content.subject}</h2>
  <div class="card my-3">
    <div class="card-body">
      <div class="card-text" style="white-space: pre-line;">{post_detail.content.content}</div>
      <div class="d-flex justify-content-end">
        <div class="badge bg-light text-dark p-2 text-start">
          <div class="mb-2">작성자: {post_detail.user ? post_detail.user.username : ""}</div>
          <div>
            작성일: {moment(post_detail.post.date_create).format("YYYY년 MM월 DD일 hh:mm a")}<br><br>
            수정일: {moment(post_detail.content.date_upd).format("YYYY년 MM월 DD일 hh:mm a")}
          </div>
        </div>
      </div>
      <div class="my-3">
        <button class="btn btn-sm btn-outline-secondary"
          on:click="{vote_post(post_detail.post.id)}">추천
          <span class="badge rounded-pill bg-success">
            {#if post_detail.count_vote == null}0
            {:else}{ post_detail.count_vote }
            {/if}
          </span>
        </button>
        {#if post_detail.user.username && $username === post_detail.user.username }
          <a use:link href="/post-update/{post_detail.post.id}"
            class="btn btn-sm btn-outline-secondary">수정</a>
          <button class="btn btn-sm btn-outline-secondary"
            on:click={() => del_post(post_detail.post.id)}>삭제</button>
        {/if}
      </div>
    </div>
  </div>

  <button class="btn btn-secondary" on:click="{() => {push("/"+post_detail.category_t1)}}">목록으로</button>

  <!-- comment -->
  <h5 class="border-bottom my-3 py-2">{list_comment.length}개의 답변이 있습니다.</h5>
    {#each list_comment as comment}
      <div class="card my-3">
        <div class="card-body">
          <div class="card-text" style="white-space: pre-line;">{comment.content.content}</div>
          <div class="d-flex justify-content-end">
            <div class="badge bg-light text-dark p-2 text-start">
              <div class="mb-2">작성자: {comment.user ? comment.user.username : ""}</div>
              <div>
                작성일: {moment(comment.comment.date_create).format("YYYY년 MM월 DD일 hh:mm a")}<br><br>
                수정일: {moment(comment.content.date_upd).format("YYYY년 MM월 DD일 hh:mm a")}
              </div>
            </div>
          </div>
          <div class="my-3">
            <button class="btn btn-sm btn-outline-secondary"
              on:click="{vote_comment(comment.comment.id)}">추천
              <span class="badge rounded-pill bg-success">
                {#if comment.count_vote == null}0
                {:else}{ comment.count_vote }
                {/if}
              </span>
            </button>
            {#if comment.user && $username === comment.user.username }
            <a use:link href="/comment-update/{comment.comment.id}"
              class="btn btn-sm btn-outline-secondary">수정</a>
            <button class="btn btn-sm btn-outline-secondary"
              on:click={() => del_comment(comment.comment.id) }>삭제</button>
            {/if}
          </div>
        </div>
      </div>
    {/each}

  <!-- submit comment -->
  <Error error={error} />
  <form method="post" class="my-3">
    <div class="mb-3">
      <textarea rows="10" bind:value={comment_content} disabled={$is_login ? "" : "disabled"} class="form-control" />
    </div>
    <input type="submit" value="답변등록" class="btn btn-primary {$is_login ? "" : "disabled"}" on:click="{create_comment}" />
  </form>
</div>