# Sveltekit app to demonstrate that you can query users from the auth.users table with the anon Supabase key
<br>
Normally this is not possible,you need the secret Supabase service key do anything with users. Supabase recommends you create a second user table in the public name pace and use triggers to copy every time a new user is created or updated.  

<br>
I'm not a fan of this. Triggers don't show errors when something goes wrong (at least I could not find them) so sooner or later this will get out of sync.
   
<br>
 So, I created a function (stored procedure) that can query the auth.users table.
 Right now it just returns all users but you can easily create simular functions to query by email, by role or by tenantId (if you are using a multi tenant setup).
   
 <br>
I also added some functions that deal with custom claims. This is probably something you will use a lot in a multi tenant SaaS application
   
<br>
-- Custom claims   

-- see: https://dev.to/supabase/supabase-custom-claims-34l2
-- and https://github.com/supabase-community/supabase-custom-claims

<br>
To get started run `npm install` and then `npm run dev`
Also run the sql functions file in Supabase's SQL Editor in their dashboard.

