<script lang="ts">
  import type { PageData } from "./$types.js";
  import { page } from "$app/stores";
  import SuperDebug, { superForm } from "sveltekit-superforms";
  import spinner from "$lib/spinner.svg";

  export let data: PageData;

  const { form, errors, constraints, enhance, delayed, message } = superForm(
    data.form,
    {
      resetForm: false,
    },
  );
</script>

<SuperDebug data={$form} />

<h1>Superforms CRUD</h1>

<h3>Users</h3>
<div class="users">
  {#if $form.id}
    <form action="/users">
      <button>Create new</button>
    </form>
  {/if}
  {#each data.users as user}
    <a href="/users/{user.id}">{user.name}</a>
  {/each}
</div>

{#if $message}
  <h3 class:invalid={$page.status >= 400}>{$message}</h3>
{/if}

<h2>{!$form.id ? "Create" : "Update"} user</h2>

<form method="POST" use:enhance>
  <input type="hidden" name="id" bind:value={$form.id} />

  <label>
    Name<br />
    <input
      name="name"
      aria-invalid={$errors.name ? "true" : undefined}
      bind:value={$form.name}
      {...$constraints.name}
    />
    {#if $errors.name}<span class="invalid">{$errors.name}</span>{/if}
  </label>

  <label>
    E-mail<br />
    <input
      name="email"
      type="email"
      aria-invalid={$errors.email ? "true" : undefined}
      bind:value={$form.email}
      {...$constraints.email}
    />
    {#if $errors.email}<span class="invalid">{$errors.email}</span>{/if}
  </label>

  <div class="buttons">
    <button>Submit</button>
    {#if $form.id}
      <button 
        name="delete"
        class="danger"
        on:click={(e) => !confirm('Are you sure?') && e.preventDefault()}
      >
        Delete user
      </button>
    {/if}
    {#if $delayed}<img src={spinner} alt="Loading...">{/if}
  </div>
  <div><input type="checkbox" name="delay"> Delay response 2s</div>
</form>

<style lang="css">
  .invalid {
    color: red;
  }

  .danger {
    background-color: brown;
  }

  .delay {
    background-color: lightblue;
  }

  form {
    display: flex;
    flex-direction: column;
    gap: 1em;
    align-items: flex-start;
    margin-bottom: 2em;
  }

  hr {
    width: 100%;
    margin-block: 2em;
  }

  .users {
    columns: 3 150px;
  }

  .users > * {
    display: block;
    white-space: nowrap;
    overflow-x: hidden;
  }
</style>
