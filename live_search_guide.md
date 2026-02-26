# Live Website Search Integration (Tavily + Groq)

To move away from a static JSON catalog and have Laurent search the *live* Louis Vuitton website, we need to give him the ability to browse the internet.

Since Groq's LLMs only process text, we will add a **Search API** to your Make.com logic. 
The easiest and best free option for AI is **Tavily**.

### How the New Workflow Works:
1. **Webhook (Twilio)**: Receives "I want a blue men's wallet."
2. **HTTP Module 1 (Tavily)**: Searches Google for exactly `site:us.louisvuitton.com blue mens wallet` and scrapes the top 3 live links, titles, and image URLs.
3. **HTTP Module 2 (Groq)**: Receives Tavily's search results. Laurent curates them, describes them luxuriously, and formats the links nicely.
4. **Twilio**: Sends the final message back to WhatsApp.

### Step 1: Get a Free Tavily API Key
1. Go to [tavily.com](https://tavily.com/)
2. Sign up for a free account (1,000 free searches per month, no credit card required).
3. Copy your API key (looks like `tvly-...`).

### Step 2: Add the Tavily Module in Make.com
1. Between your `Webhook` and your `Groq HTTP` module, add a **new** HTTP module ("Make a request").
2. **URL**: `https://api.tavily.com/search`
3. **Method**: `POST`
4. **Headers**: none needed.
5. **Body type**: `Raw` -> `JSON (application/json)`
6. **Request Content**:
```json
{
  "api_key": "tvly-YOUR_KEY_HERE",
  "query": "site:us.louisvuitton.com INSERT_TWILIO_BODY_HERE",
  "include_images": true,
  "max_results": 3
}
```
*(Make sure to map the Twilio `Body` variable in the query string!).*
7. Set **Parse response** to `Yes`.

### Step 3: Update Groq to Read the Search Results
1. In your **Groq HTTP** module, delete the giant 120-item JSON catalog from the prompt.
2. Update the prompt to look like this:

```json
{
  "model": "llama-3.3-70b-versatile",
  "messages": [
    {
      "role": "system",
      "content": "Role: Laurent, Senior Client Advisor at Louis Vuitton. Tone: Luxurious. \n\nBelow are LIVE search results from the Louis Vuitton website. \n\n1. Review the search results and choose the clearest product matches.\n2. Curate a luxurious reply describing the products.\n3. You MUST include the exact `url` and `image` link provided in the results.\n\nLIVE SEARCH DATA:\nINSERT_TAVILY_RESULTS_HERE"
    },
    {
      "role": "user",
      "content": "INSERT_TWILIO_BODY_HERE"
    }
  ]
}
```
3. Highlight `INSERT_TAVILY_RESULTS_HERE` and map the **`results[]`** array from the Tavily module!

This turns Laurent into a real-time web-scraping stylist!
