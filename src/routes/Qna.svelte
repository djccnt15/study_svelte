<script>
  import fastapi from "../lib/api"
  import { link } from "svelte-spa-router"
  import { page, is_login, keyword } from "../lib/store";
  import moment from "moment";
  moment.locale("ko")

  let list_post = []
  let size = 10
  let total = 0
  let kw = ""
  $: total_page = Math.ceil(total/size)

  function get_list_post() {
    let params = {
      page: $page,
      size: size,
      keyword: $keyword
    }
    fastapi("get", "/api/board/list/qna", params, (json) => {
      list_post = json.post_list
      total = json.total
      kw = $keyword
    })
  }

  $: $page, $keyword, get_list_post()
</script>

<div class="container my-3">
  <div class="row my-3">
    <div class="col-6">
      <a use:link href="/post-create"
        class="btn btn-primary {$is_login ? "" : "disabled"}">질문 등록하기</a>
    </div>
    <div class="col-6">
      <div class="input-group">
        <input type="text" class="form-control" bind:value="{kw}">
        <button class="btn btn-outline-secondary" on:click={() => {$keyword = kw, $page = 0}}>
          찾기
        </button>
      </div>
    </div>
  </div>
  <table class="table">
    <thead>
      <tr class="table-dark">
        <th>번호</th>
        <th>제목</th>
        <th>작성일시</th>
        <th>작성자</th>
      </tr>
    </thead>
    <tbody>
      {#each list_post as post, i}
        <tr>
          <td>{total - ($page * size) - i}</td>
          <td>
            <a use:link href="/post/{post.post.id}">{post.content.subject}</a>
            {#if post.count_comment != null}
              댓글: <span class="text-danger small mx-2">{post.count_comment}</span>
            {/if}
            {#if post.count_vote != null}
              추천: <span class="text-danger small mx-2">{post.count_vote}</span>
            {/if}
          </td>
          <td>{moment(post.post.date_create).format("YYYY년 MM월 DD일 hh:mm a")}</td>
          <td>{post.user.username}</td>
        </tr>
      {/each}
    </tbody>
  </table>

  <!-- pagination start -->
  <ul class="pagination justify-content-center">
    <!-- first page -->
    <li class="page-item {$page <= 0 && "disabled"}">
      <button class="page-link" on:click="{() => {$keyword = kw, $page = 0}}">처음</button>
    </li>
    <!-- previous page -->
    <li class="page-item {$page <= 0 && "disabled"}">
      <button class="page-link" on:click="{() => $page--}">이전</button>
    </li>
    <!-- page num -->
    {#each Array(total_page) as _, loop_page}
      {#if loop_page >= $page-5 && loop_page <= $page+5}
      <li class="page-item {loop_page === $page && "active"}">
        <button on:click="{() => $page = loop_page}" class="page-link">{loop_page+1}</button>
      </li>
      {/if}
    {/each}
    <!-- next page -->
    <li class="page-item {$page >= total_page-1 && "disabled"}">
      <button class="page-link" on:click="{() => $page++}">다음</button>
    </li>
    <li class="page-item {$page >= total_page-1 && "disabled"}">
      <button class="page-link" on:click="{() => {$keyword = kw, $page = total_page-1}}">끝</button>
    </li>
  </ul>
  <!-- pagination end -->
</div>