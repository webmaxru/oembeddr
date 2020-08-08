<script>
  export let name;

  import Toast from 'bootstrap/js/src/toast';
  import Tab from 'bootstrap/js/src/tab';

  let url = '';
  let result;

  async function getResult() {
    let response = await fetch(`https://www.tiktok.com/oembed?url=${url}`);

    if (response.ok) {
      let data = await response.json();

      if (data.html) return data;

      throw new Error('This kind of URL is not supported yet');
    }

    throw new HttpError('There was an issue with the server');
  }

  function submitHandler(e) {
    result = getResult();
  }

  function copyToClipboard(textAreaId, toastId) {
    var copyText = document.getElementById(textAreaId);

    copyText.select();
    copyText.setSelectionRange(0, 99999);

    document.execCommand('copy');

    let copySuccessToast = new Toast(document.getElementById(toastId));
    copySuccessToast.show();
  }
</script>

<style global type="text/scss">
  @import 'styles';
</style>

<main>

  <div class="container">

    <h1 class="display-4 text-center">{name}</h1>

    <form class="row mb-3" on:submit|preventDefault={submitHandler}>

      <div class="col-sm-12 col-md-9 mb-1">
        <input
          type="text"
          class="form-control form-control-lg"
          id="urlInput"
          placeholder="Social media post URL..."
          aria-label="Social media post URL"
          bind:value={url}
          required />

      </div>

      <div class="col-md-3">
        <button class="btn btn-lg btn-primary btn-block">Get a code</button>
      </div>

    </form>

    <div
      class="toast"
      role="alert"
      aria-live="assertive"
      aria-atomic="true"
      id="copySuccessToast"
      style="position: absolute; top: 0; right: 0;"
      data-delay="2000"
      data-animation="false">
      <div class="toast-header">
        <strong class="mr-auto">oEmbeddr</strong>
        <button
          type="button"
          class="ml-2 mb-1 close"
          data-dismiss="toast"
          aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="toast-body">The code was copied to the clipboard</div>
    </div>

    {#if result === undefined}
      <p class="lead">Enter URL of any social media post to get an HTML code for embedding and a preview</p>
    {:else}
      {#await result}

        <div class="progress">
          <div
            class="progress-bar progress-bar-striped progress-bar-animated"
            role="progressbar"
            aria-valuenow="100"
            aria-valuemin="0"
            aria-valuemax="100"
            style="width: 100%">
            Loading...
          </div>
        </div>

      {:then value}

        <ul class="nav nav-tabs mb-3" id="resultTab" role="tablist">
          <li class="nav-item" role="presentation">
            <a
              class="nav-link active"
              id="code-tab"
              data-toggle="tab"
              href="#code"
              role="tab"
              aria-controls="code"
              aria-selected="true">
              Code
            </a>
          </li>
          <li class="nav-item" role="presentation">
            <a
              class="nav-link"
              id="preview-tab"
              data-toggle="tab"
              href="#preview"
              role="tab"
              aria-controls="preview"
              aria-selected="false">
              Preview
            </a>
          </li>
        </ul>
        <div class="tab-content" id="resultTabContent">
          <div
            class="tab-pane fade show active"
            id="code"
            role="tabpanel"
            aria-labelledby="code-tab">
            <textarea class="form-control mb-1" id="htmlArea" rows="5" readonly>{value.html}</textarea>
            <button
              class="btn btn-secondary btn-sm"
              on:click={() => copyToClipboard('htmlArea', 'copySuccessToast')}>
              Copy to clipboard
            </button>

          </div>
          <div
            class="tab-pane fade"
            id="preview"
            role="tabpanel"
            aria-labelledby="preview-tab">

            <div class="embed-responsive embed-responsive-4by3 bg-white">
              <iframe
                class="embed-responsive-item"
                srcdoc="<!DOCTYPE html><html lang='en'><head><meta
                charset='utf-8'></head><body>{value.html}</body></html>" />
            </div>

          </div>
        </div>

      {:catch error}

        <div class="alert alert-danger" role="alert">{error.message}</div>

      {/await}
    {/if}

  </div>

</main>

<nav class="navbar navbar-expand fixed-bottom navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="/">&copy; Maxim Salnikov</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav mr-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="nav-link" href="https://github.com/webmaxru/oembeddr">
            GitHub Repo
          </a>
        </li>
        <li class="nav-item">
          <a
            class="nav-link"
            href="https://dev.to/webmaxru/building-oembeddr-using-azure-static-web-apps-part-1-svelte-bootstrap-5-and-tiktok-4ja9">
            Blogpost
          </a>
        </li>
      </ul>
      <span class="navbar-text">
        <a class="nav-link" href="https://twitter.com/webmaxru">
          Twitter: @webmaxru
        </a>
      </span>
    </div>
  </div>
</nav>
