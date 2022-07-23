<script>
  import { supabase } from "$lib/supabaseClient";
  import { onMount } from "svelte";
  import { user } from "$lib/sessionStore";
  import { allUsers } from "$lib/allUsers.js";

  let loading = false;
  let email = "userone@inter.net";
  let password = "secret1234";
  let authenticated;

  onMount(async () => {
    user.set(supabase.auth.user());
    authenticated = supabase.auth.user();
    supabase.auth.onAuthStateChange((event, session) => {
      user.set(session?.user);
      authenticated = event !== "SIGNED_OUT";
      console.log(
        `[Index] onMount user auth changed. Authenticated: ${authenticated}`,
        session?.user
      );
    });

    const {data: auth, error} = await supabase.rpc('uid_test')
    console.log('[Index] onMount check if logged in or not', auth, error)

    // get all users
    try {
      const { data: returnedUsers, error } = await supabase.rpc(
        "get_all_users"
      );
      if (error) throw error;
      console.log("[Index] onMount got allUsers", returnedUsers);
      allUsers.set(returnedUsers);
    } catch (error) {
      console.log("[Index] onMount error getting users", error);
    }
  });

  const logout = async () => {
    try {
      loading = true;
      const { error } = await supabase.auth.signOut();
      if (error) throw error;
      allUsers.set([]);
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  };

  const handleLogin = async () => {
    try {
      loading = true;
      const { error } = await supabase.auth.signIn({
        email,
        password,
      });
      if (error) throw error;
      // get all users
      try {
        const { data: returnedUsers, error } = await supabase.rpc(
          "get_all_users"
        );
        if (error) throw error;
        console.log("[Index] onMount got allUsers", returnedUsers);
        allUsers.set(returnedUsers);
      } catch (error) {
        console.log("[Index] onMount error getting users", error);
      }
    } catch (error) {
      alert(error.error_description || error.message);
    } finally {
      loading = false;
    }
  };
</script>

<div class="page">
  <div class="container">
    <form class="row flex flex-center" on:submit|preventDefault={handleLogin}>
      <div>
        <h1 class="header">Supabase + Svelte</h1>
        <p class="description">Please sign in</p>
        <div>
          <input
            class="inputField"
            type="email"
            placeholder="Your email"
            bind:value={email}
          />
        </div>
        <div>
          <input
            class="inputField"
            type="password"
            placeholder="Your password"
            bind:value={password}
          />
        </div>
        <div>
          <input
            type="submit"
            class="button block"
            value={loading ? "Loading" : "Submit"}
            disabled={loading || authenticated}
          />
        </div>
      </div>
    </form>
    <div class="user-info">
      <h1>Logged in User</h1>
      <div class="inputField">id: {$user?.id}</div>
      <div class="inputField">email: {$user?.email}</div>
      <div class="inputField">name: {$user?.user_metadata?.name}</div>
      <div class="inputField">role: {$user?.app_metadata?.role}</div>
      <button on:click={logout} disabled={loading || !authenticated}
        >Logout</button
      >
    </div>
  </div>
  <hr />
  <div class="title">
    <h2>All users</h2>
    <span>(only visible when an admin is logged in)</span>
  </div>
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th>Role</th>
      </tr>
    </thead>
    <tbody>
      {#each $allUsers as user}
        <tr>
          <td>{user.user_name}</td>
          <td>{user.user_email}</td>
          <td>{user.user_role}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>

<style>
  table {
    margin-top: 10px;
    width: 100%;
  }
  .title {
    text-align: center;
  }
  .title h2 {
    margin-bottom: 0;
  }
  .page {
    width: 600px;
    margin: 20px auto;
  }
  .container {
    display: flex;
    flex-wrap: nowrap;
    justify-content: space-between;
  }
  .inputField {
    margin: 5px 0;
  }
  .user-info {
    display: flex;
    flex-direction: column;
  }
</style>
