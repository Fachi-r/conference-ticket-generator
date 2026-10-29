<script lang="ts">
  // Form state
  let avatar = $state<string | null>("");
  let name = $state("");
  let email = $state("");
  let github = $state("");

  // UI state
  let isValid = $state(false);
  let avatarInput = $state<HTMLInputElement | null>(null);

  const allowedTypes = ["image/png", "image/jpeg", "image/jpg"];

  // Error state
  let errors = $state({
    name: "",
    email: "",
    github: "",
    avatar: "",
  });

  const defaultUploadImage = "/images/icon-upload.svg";

  function handleUpload(
    event: Event & { currentTarget: EventTarget & HTMLInputElement },
  ) {
    const files = event.currentTarget.files;
    if (!files || files.length === 0) return;

    const file = files[0];

    // 1. Validate Type
    if (!allowedTypes.includes(file.type)) {
      errors.avatar = "Only JPG and PNG images are allowed.";
      return;
    }

    // 2. Validate Size (5MB)
    const MAX_SIZE = 5 * 1024 * 1024;
    if (file.size > MAX_SIZE) {
      errors.avatar = "File too large. Please upload a photo under 5MB.";
      return;
    }

    // Clear error and save file if valid
    errors.avatar = "";

    const reader = new FileReader();
    reader.addEventListener("load", () => {
      avatar = reader.result as string;
    });
    reader.readAsDataURL(file);
  }

  function handleKeydown(
    event: KeyboardEvent & { currentTarget: EventTarget & HTMLDivElement },
  ) {
    if (event.key === "Enter") avatarInput?.click();
    if (event.key === "Escape") removeImg(event);
  }

  function removeImg(event: MouseEvent | KeyboardEvent) {
    event.preventDefault();
    event.stopPropagation();
    avatar = null;
    errors.avatar = "";
    if (avatarInput) avatarInput.value = ""; // Reset input so same file can be re-selected
  }

  function changeImg(event: MouseEvent | KeyboardEvent) {
    event.preventDefault();
    event.stopPropagation();
    avatarInput?.click();
  }

  function validateForm(): boolean {
    // Reset text errors
    errors.name = "";
    errors.email = "";
    errors.github = "";
    let isValid = true;

    // Name validation
    if (!name.trim()) {
      errors.name = "Full Name is required.";
      isValid = false;
    } else if (name.trim().length < 2) {
      errors.name = "Name must be at least 2 characters.";
      isValid = false;
    }

    // Email validation
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!email.trim()) {
      errors.email = "Email Address is required.";
      isValid = false;
    } else if (!emailRegex.test(email.trim())) {
      errors.email = "Please enter a valid email address.";
      isValid = false;
    }

    // GitHub validation (strip optional leading '@' for UX)
    const githubValue = github.trim().replace(/^@/, "");
    const githubRegex = /^[a-zA-Z0-9](?:[a-zA-Z0-9]|-(?=[a-zA-Z0-9])){0,38}$/;
    if (!githubValue) {
      errors.github = "GitHub Username is required.";
      isValid = false;
    } else if (!githubRegex.test(githubValue)) {
      errors.github = "Please provide a valid GitHub username.";
      isValid = false;
    } else {
      github = githubValue
    }

    // Avatar validation (ensure one is actually present at submit time)
    if (!avatar && !errors.avatar) {
      errors.avatar = "Please upload an avatar.";
      isValid = false;
    }

    return isValid;
  }

  function handleSubmit(e: SubmitEvent) {
    e.preventDefault();
    e.stopPropagation();

    if (validateForm()) {
      isValid = true; // Only toggle to ticket view if valid
    }
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
      alt="Background"
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
    <div class="pattern-circle">
      <img src="/images/pattern-circle.svg" alt="" aria-hidden="true" />
    </div>
  </div>
</div>

<main>
  <div class="logo">
    <img src="/images/logo-full.svg" alt="Logo" />
  </div>

  <h1 class="heading">
    {#if !isValid}
      Your Journey to Coding Conf 2025 Starts Here!
    {:else}
      Congrats, <span>{name}</span>! Your ticket is ready.
    {/if}
  </h1>

  <p class="subtitle" class:isValid>
    {#if !isValid}
      Secure your spot at next year's biggest coding conference.
    {:else}
      We've emailed your ticket to <br /> <span>{email}</span> and will send updates
      in the run up to the event.
    {/if}
  </p>

  {#if !isValid}
    <form onsubmit={handleSubmit}>
      <p class="label">Upload Avatar</p>

      <div
        tabindex="0"
        role="button"
        onkeydown={handleKeydown}
        class="upload-container"
        class:hidden={!!avatar}
        class:error-input={!!errors.avatar}
        onclick={() => avatarInput?.click()}
      >
        <input
          bind:this={avatarInput}
          id="avatar"
          type="file"
          accept={allowedTypes.join(", ")}
          aria-hidden="true"
          style:display="none"
          onchange={handleUpload}
        />

        <div class="avatar-container" class:active={!!avatar}>
          <img
            src={avatar || defaultUploadImage}
            class:default={!avatar}
            alt="Avatar"
          />
        </div>

        <div class="flex">
          {#if avatar}
            <button type="button" class="btn" onclick={removeImg}
              >Remove image</button
            >
            <button type="button" class="btn" onclick={changeImg}
              >Change image</button
            >
          {:else}
            <p>Drag and drop or click to upload</p>
          {/if}
        </div>
      </div>
      <small class:error={!!errors.avatar}>
        {errors.avatar
          ? errors.avatar
          : `Upload your photo (JPG or PNG, max size: 5MB).`}
      </small>

      <label for="fullname"> Full Name </label>
      <input
        bind:value={name}
        id="fullname"
        type="text"
        class:error-input={!!errors.name}
      />
      {#if errors.name}
        <small class="error">
          {errors.name}
        </small>
      {/if}

      <label for="email"> Email Address </label>
      <input
        bind:value={email}
        id="email"
        type="email"
        placeholder="example@email.com"
        class:error-input={!!errors.email}
      />
      {#if errors.email}
        <small class="error">
          {errors.email}
        </small>
      {/if}

      <label for="github"> GitHub Username </label>
      <input
        bind:value={github}
        id="github"
        type="text"
        placeholder="@yourusername"
        class:error-input={!!errors.github}
      />
      {#if errors.github}
        <small class="error">
          {errors.github}
        </small>
      {/if}

      <!-- REMOVED onclick={handleSubmit} TO PREVENT DOUBLE FIRING -->
      <button type="submit" class="submit-button"> Generate My Ticket </button>
    </form>
  {:else if avatar}
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
          <div class="avatar-container">
            <img src={avatar || ""} alt="Avatar" />
          </div>
          <div class="stack">
            <p class="name">{name}</p>
            <p class="github flex">
              <img src="/images/icon-github.svg" alt="github logo" />
              @{github}
            </p>
          </div>
        </div>
      </div>
      <div class="tail">#{Math.floor(Math.random() * 100000)}</div>
    </div>
  {/if}
</main>

<footer class="attribution">
  Challenge by <a href="https://www.frontendmentor.io?ref=challenge"
    >Frontend Mentor</a
  >. Coded by <a href="https://github.com/Fachi-r">Fachi-r</a>.
</footer>
