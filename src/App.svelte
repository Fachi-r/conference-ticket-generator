<script lang="ts">
  // form data bindings
  // let name = $state("");
  // let email = $state("");
  // let github = $state("");
  // let avatar: string | ArrayBuffer | null = $state(null);
  let avatarInput: HTMLInputElement | null = $state(null);
  let imageEl: HTMLImageElement | null = $state(null);

  let name = $state("Jonatan Kristof");
  let email = $state("jonatan@email.com");
  let github = $state("@jonatankristof0101");
  let avatar: string | ArrayBuffer | null = $state("./slkjd");

  let valid = $state(true);

  const defaultUploadImage = "/images/icon-upload.svg";

  function handleUpload(
    event: Event & { currentTarget: EventTarget & HTMLInputElement },
  ) {
    let files = event.currentTarget.files;
    if (!files) return;

    let file = files[0];

    const reader = new FileReader();
    reader.addEventListener("load", function () {
      avatar = reader.result;
      imageEl?.setAttribute("src", String(avatar));
    });
    reader.readAsDataURL(file);
  }

  function handleKeydown(
    event: KeyboardEvent & { currentTarget: EventTarget & HTMLDivElement },
  ) {
    if (event.key === "Enter") avatarInput?.click();
    if (event.key === "Escape") removeImg(event);
  }

  function removeImg(
    event: (MouseEvent | KeyboardEvent) & {
      currentTarget: EventTarget & HTMLElement;
    },
  ) {
    event.preventDefault();
    event.stopPropagation();
    imageEl?.setAttribute("src", defaultUploadImage);
    avatar = null;
  }

  function changeImg(
    event: (MouseEvent | KeyboardEvent) & {
      currentTarget: EventTarget & HTMLElement;
    },
  ) {
    event.preventDefault();
    event.stopPropagation();
    avatarInput?.click();
  }

  function handleSubmit(
    e: (MouseEvent | SubmitEvent) & {
      currentTarget: EventTarget & HTMLElement;
    },
  ) {
    e.preventDefault();
    e.stopPropagation();

    // Do some validation

    console.log(e?.currentTarget);

    valid = !valid;
  }
</script>

<div class="background">
  <!-- Background -->
  <picture>
    <source srcset="/images/background-desktop.png" media="(width >= 1080px)" />
    <source srcset="/images/background-tablet.png" media="(width >= 720px)" />
    <img
      class="background-image"
      src="/images/background-mobile.png"
      alt=""
      aria-hidden="true"
    />
  </picture>
  <!-- squiggly top -->
  <img
    class="squiggly-line top"
    src="/images/pattern-squiggly-line-top.svg"
    alt=""
    aria-hidden="true"
  />
  <!-- squiggly bottom -->
  <picture>
    <source
      srcset="/images/pattern-squiggly-line-bottom-desktop.svg"
      media="(width >= 1080px)"
    />
    <img
      class="squiggly-line bottom"
      src="/images/pattern-squiggly-line-bottom-mobile-tablet.svg"
      alt=""
      aria-hidden="true"
    />
  </picture>

  <!--patterns -->
  <div class="patterns">
    <div class="pattern-circle">
      <img src="/images/pattern-circle.svg" alt="" aria-hidden="true" />
    </div>
  </div>
</div>

<main>
  <!-- Logo -->
  <div class="logo">
    <img src="/images/logo-full.svg" alt="Logo" />
  </div>

  <h1 class="heading">
    {#if !valid}
      Your Journey to Coding Conf 2025 Starts Here!
    {:else}
      Congrats, <span>{name}</span>! Your ticket is ready.
    {/if}
  </h1>
  <p class="subtitle">
    {#if !valid}
      Secure your spot at next year's biggest coding conference.
    {:else}
      We've emailed your ticket to <span>{email}</span> and will send updates in
      the run up to the event.
    {/if}
  </p>

  {#if !valid}
    <!-- Form starts -->
    <form onsubmit={handleSubmit}>
      <p class="label">Upload Avatar</p>

      <div
        tabindex="0"
        role="button"
        onkeydown={handleKeydown}
        class="upload-container"
        class:hidden={avatar}
        onclick={() => avatarInput?.click()}
      >
        <input
          bind:this={avatarInput}
          id="avatar"
          type="file"
          accept="image/png, image/jpeg, image/jpg"
          aria-hidden="true"
          style:display="none"
          onchange={handleUpload}
        />
        <div class="avatar-container">
          <img
            bind:this={imageEl}
            src={defaultUploadImage}
            class:default={!avatar}
            alt="Avatar"
          />
        </div>

        <div class="flex">
          {#if avatar}
            <button type="button" class="btn" onclick={removeImg}>
              Remove image
            </button>
            <button type="button" class="btn" onclick={changeImg}>
              Change image
            </button>
          {:else}
            <p>Drag and drop or click to upload</p>
          {/if}
        </div>
      </div>

      <small>
        <img src="/images/icon-info.svg" alt="" />
        Upload your photo (JPG or PNG, max size: 500KB).
      </small>

      <label for="fullname"> Full Name </label>
      <input bind:value={name} id="fullname" type="text" />

      <label for="email"> Email Address </label>
      <input
        bind:value={email}
        id="email"
        type="email"
        placeholder="example@email.com"
      />

      <label for="github"> GitHub Username </label>
      <input
        bind:value={github}
        id="github"
        type="text"
        placeholder="@yourusername"
      />

      <button type="submit" class="submit-button" onclick={handleSubmit}>
        Generate My Ticket
      </button>
    </form>

    <!-- Form ends -->
  {:else if avatar}
    <!-- Generated tickets starts -->
    <div class="ticket">
      <div class="details">
        <div class="header flex">
          <img src="/images/logo-mark.svg" alt="Logo Mark" />

          <div class="stack">
            <p class="org">Coding Conf</p>
            <p class="details">Jan 31, 2025 / Austin, TX</p>
          </div>
        </div>

        <div class="footer flex">
          <!-- <img src={String(avatar)} alt="Avatar" /> -->
          <div class="avatar-container">
            <img src="/images/image-avatar.jpg" alt="Avatar" />
          </div>

          <div class="stack">
            <p class="name">
              {name}
            </p>
            <p class="github flex">
              <img src="/images/icon-github.svg" alt="github logo" />
              {github}
            </p>
          </div>
        </div>
      </div>

      <div class="tail">
        #{Math.floor(Math.random() * 100000)}
      </div>
    </div>
    <!-- Generated tickets ends -->
  {/if}
</main>

<footer class="attribution">
  Challenge by <a href="https://www.frontendmentor.io?ref=challenge"
    >Frontend Mentor</a
  >. Coded by <a href="https://github.com/Fachi-r">Fachi-r</a>.
</footer>
