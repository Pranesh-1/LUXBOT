# Agentic Memory Setup: Connecting Supabase to Make.com

To give Laurent a "brain" that remembers users, we will use **Supabase** (a wildly popular, free, open-source alternative to Firebase/PostgreSQL). 

This changes your workflow by adding "Read Memory" at the start, and "Save Memory" at the end.

## Step 1: Create Your Free Supabase Database
1. Go to [supabase.com](https://supabase.com) and sign in.
2. Click **New Project** (it's free). Name it "LV Stylist Memory".
3. Once the database finishes building (takes ~2 minutes), click **Table Editor** on the left menu.
4. Click **Create a new table**.
   - **Name**: `chat_history`
   - **Enable Row Level Security (RLS)**: Uncheck this for now (easier for testing).
5. Add the following columns:
   - `id` (int8, primary key) - *already there by default*
   - `phone_number` (text) - *This will store Twilio's 'From' number*
   - `contact_name` (text, nullable) - *To remember their name*
   - `conversation_summary` (text) - *A running summary of what they like/buy*
6. Click **Save**.

## Step 2: Get Your Supabase API Keys
1. In Supabase, click the **Settings** gear at the bottom left.
2. Click **API**.
3. You need two things from this page:
   - **Project URL**: (e.g., `https://xyz.supabase.co`)
   - **Project API Key (anon/public)**: (A long `ey...` string)

## Step 3: Add the "Read Memory" Module in Make.com
Right after your Twilio Webhook (and Webhook Response), we need to check if this phone number exists in the database.

1. Add a **Supabase** module between `Webhook Response` and `Groq Pre-Processor`.
2. Action: **Search Rows**.
3. Create a connection by pasting your **Project URL** and **API Key**.
4. Set **Table** to `chat_history`.
5. Set **Filter**:
   - `phone_number` = *[Map Twilio's `From` variable here]*
6. Set **Limit**: `1` (we only need their single history file).

*Now, pass this data to Groq!*
Update your Pre-Processor Groq prompt to include:
> "User Memory: [Map Supabase `conversation_summary` here]. Keep this in mind."

## Step 4: Add the "Save Memory" Module
At the very end of your workflow (after Twilio sends the final message to the user), we need to update the database with the new conversation.

1. Add a final **Supabase** module at the very end of your Make.com scenario.
2. Action: **Upsert Row** (Upsert means "Update if exists, Insert if new").
3. Set **Table** to `chat_history`.
4. Fill the columns:
   - `phone_number`: *[Map Twilio's `From` variable]*
   - `conversation_summary`: *[Map the final Groq output message]* (In a more advanced setup, you can use another Groq module just to "summarize" the chat before saving it, but saving the final response works for testing!)

**Result**: You now have a stateful database! If a user texts "+15551234", Make pulls their file, sends it to Groq, Groq recommends a product based on their history, and Make saves the new interaction back into the folder.
