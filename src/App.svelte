<script lang="ts">
  function handleSubmit(e: any) {
    console.log(e?.currentTarget?.value);
  }

  // form data bindings
  let name = $state("");
  let email = $state("");
  let github = $state("");
  let avatarInput: HTMLInputElement | null = $state(null);
  let avatar: File | null = $state(null);

  let valid = $state(false);

  // $inspect(avatarInput).with((t, v) => {
  //   if (t !== "init") console.log("avatar", v);
  // });

  $inspect(avatar);

  function handleUpload(
    event: Event & { currentTarget: EventTarget & HTMLInputElement },
  ) {
    let files = event.currentTarget.files;
    if (files) {
      avatar = files[0];
    }
    // console.log(event.currentTarget.value);
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
    <form>
      <p class="label">Upload Avatar</p>

      <button
        type="button"
        class="upload-container"
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
            src={avatar ? avatar.webkitRelativePath : "/images/icon-upload.svg"}
            alt="Avatar"
          />
        </div>
        <p>Drag and drop or click to upload</p>
      </button>

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
  {:else}
    <!-- Generated tickets starts -->
    Coding Conf Jan 31, 2025 / Austin, TX
    <!-- Generated tickets ends -->
  {/if}
</main>

<footer class="attribution">
  Challenge by <a href="https://www.frontendmentor.io?ref=challenge"
    >Frontend Mentor</a
  >. Coded by <a href="https://github.com/Fachi-r">Fachi-r</a>.
</footer>
