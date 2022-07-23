<script>
  /*
    Create some users first by navigating to /seed

    Then you need to manually set the roles in the SQL editor of Supabase Studio (the online dashboard).
    You could add code to do so but you need to manually set at least one admin
    for example: select set_claim('<uid of user>', 'role', '"admin"');
    You also need to set the claims admin role manually for at least one user.
    select set_claim('<uid of admin user>', 'claims_admin', 'true');
  */
  import { createClient } from "@supabase/supabase-js";

  const supabaseUrl = import.meta.env.VITE_SUPABASE_URL
  const supabaseServiceKey = import.meta.env.VITE_SUPABASE_SERVICE_ROLE

  const supabase = createClient(supabaseUrl, supabaseServiceKey);

  const createUsers = async () => {
    for await (var newUser of someUsers) {
      newUser.email_confirm = true;
      const { data: user, error } = await supabase.auth.api.createUser(newUser);
      if (error) {
        console.log("Error while seeding users", error);
      }
      console.log('[seed.js] user created', user)
    }
  };

  // Create a few auth users
  const someUsers = [
    {
      email: "userone@inter.net",
      password: "secret1234",
      user_metadata: {
        name: "User One",
      },
    },
    {
      email: "usertwo@inter.net",
      password: "secret1234",
      user_metadata: {
        name: "User Two",
      },
    },
    {
      email: "userthree@inter.net",
      password: "secret1234",
      user_metadata: {
        name: "User Three",
      },
    },
  ];

  createUsers();
</script>
