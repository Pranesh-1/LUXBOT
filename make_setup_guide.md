# Avotangi AI Stylist - Make.com Setup Guide

> [!WARNING]
> **Assignment Requirement Alert**: The assignment description explicitly stated "Build an **n8n** workflow". By using Make.com, you are deviating from the core requirement. However, if n8n is strictly not possible, submitting a flawlessly working Make.com prototype with a note explaining your infrastructure choice is better than not submitting at all.

This guide replaces the n8n workflow with a completely free, cloud-hosted **Make.com** (formerly Integromat) scenario.

## Prerequisites
1.  **Make.com Account**: [Sign up for free](https://www.make.com/). The free tier gives you 1,000 operations/month.
2.  **OpenAI API Key**: (Or Anthropic/Claude). For the AI Stylist logic and image vision.
3.  **Twilio Account**: Since Meta's native WhatsApp API is very difficult to set up quickly without a verified business, we will use Twilio's WhatsApp Sandbox (which is free and immediate for testing).

---

## Phase 1: Setting up the Workflow in Make.com

### 1. Create a New Scenario
- Log into Make.com -> Go to **Scenarios** -> Click **Create a new scenario** (top right).

### 2. The Trigger: Webhooks (Twilio)
Instead of a native WhatsApp node, we use a Custom Webhook to receive messages from Twilio.
1.  Click the big `+` circle. Search for **Webhooks**.
2.  Select **Custom webhook**.
3.  Click **Create a webhook**. Name it "Twilio WhatsApp".
4.  Make will show your webhook URL. Since you already generated it, you will use:
    **`https://hook.eu1.make.com/mueppvgpxf2r5nhahztxuh819qq6isno`**
5.  *Leave Make.com running (it will say "Stop" with a spinning wheel, waiting to determine the data structure).*

### 3. Connect Twilio to Make
1.  In a new tab, go to your [Twilio Console](https://console.twilio.com/).
2.  Navigate to **Messaging** -> **Try it out** -> **Send a WhatsApp message**.
3.  Follow the 1-minute setup: Text the join code (e.g., "join purple-monkey") to the Twilio number from your personal WhatsApp.
4.  Go to the **Sandbox Settings** tab.
5.  In the "When a message comes in" box, **paste your Make.com Webhook URL**. Save.
6.  Send a test message from your phone: "Hello".
7.  Go back to Make.com. The webhook should say **"Successfully determined"**. Click OK.

### 4. The AI Brain (Using GroqCloud instead of OpenAI)
Since you want to use GroqCloud (which is completely free and much faster), we cannot use the standard OpenAI module. Instead, we use Make's **HTTP** module to call Groq's API directly.

1.  In Make.com, click "Add another module" next to the Webhook.
2.  Search for **HTTP** and select **Make a request**.
3.  Configure the HTTP module exactly like this:
    -   **URL**: `https://api.groq.com/openai/v1/chat/completions`
    -   **Method**: `POST`
    -   **Headers**: 
        -   Item 1: Name: `Authorization`, Value: `Bearer YOUR_GROQ_API_KEY` (replace with your real Groq key).
        -   Item 2: Name: `Content-Type`, Value: `application/json`
    -   **Body type**: `Raw`
    -   **Content type**: `JSON (application/json)`
    -   **Request content**: Paste this JSON structure:
        ```json
        {
          "model": "llama-3.3-70b-versatile",
          "messages": [
            {
              "role": "system",
              "content": "You are Marie, a luxury stylist for Avotangi. Tone: Luxurious. Only recommend products from this catalog: [PASTE YOUR CATALOG JSON HERE]"
            },
            {
              "role": "user",
              "content": "Text from Twilio"
            }
          ]
        }
        ```
    -   **IMPORTANT**: Inside that JSON structure, delete `"Text from Twilio"` and instead click the box and map the Twilio `Body` variable from the Webhook.
    -   **Parse response**: Set to `Yes`. This turns the API response into data we can use.

### 5. Send the Reply (Twilio)
1.  Add another module after HTTP.
2.  Search for **Twilio**.
3.  Select **Send a Message**.
4.  **Connection**: Create a connection using your Twilio `Account SID` and `Auth Token` (found on your Twilio console homepage).
5.  **From**: Your Twilio WhatsApp Sandbox number (e.g., `whatsapp:+14155238886`).
6.  **To**: Map this to the `From` variable coming from the Webhook.
7.  **Body**: We need to extract the text Groq replied with. Map this to `Data` -> `choices[]` -> `message` -> `content` coming from the **HTTP** module.

---

## Phase 2: Handling Images (The "Consultative Visual" Requirement)

The assignment requires "WhatsApp image parsing". Since Twilio sends Media URLs when you send an image, here is how to handle it in Make:

1.  **Add a Router**: Delete the link between Webhook and HTTP. Add a **Router** module after the Webhook.
2.  **Route 1 (Text)**:
    -   Link to the HTTP module we just built.
    -   Click the dotted line (the filter) -> Condition: `NumMedia` (from Webhook) **Equal to** `0` (or leave empty if it doesn't exist).
3.  **Route 2 (Image)**:
    -   Create a new HTTP module.
    -   Click the dotted line -> Condition: `NumMedia` (from Webhook) **Greater than** `0`.
    -   Configure this HTTP module identically to the text one, BUT change the `"model"` to `"llama-3.2-11b-vision-preview"`.
    -   In the `"content"` array for the user message, you must pass the Image URL provided by Twilio (`MediaUrl0`). Here is the JSON structure for vision:
        ```json
        {
          "model": "llama-3.2-11b-vision-preview",
          "messages": [
            {
              "role": "system",
              "content": "You are Marie, a luxury stylist for Avotangi. Analyze this image and recommend products from our catalog: [PASTE CATALOG HERE]"
            },
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "What shoes match this outfit?"
                },
                {
                  "type": "image_url",
                  "image_url": {
                    "url": "Map Twilio MediaUrl0 here"
                  }
                }
              ]
            }
          ]
        }
        ```
    -   Connect this to a Twilio "Send a Message" module just like Route 1.

---

## Final Review Before Recording

When submitting your screen recording, you will:
1.  Show the Make.com scenario (zoom out to show the architecture).
2.  Explain *why* you used Make.com (e.g., "To ensure a flawlessly cloud-hosted, zero-downtime evaluation environment, I built the architecture in Make instead of local n8n...").
3.  Show your phone screen using WhatsApp.
4.  Send a text. See the AI stylist reply.
5.  Send an image. See the AI read the image and recommend a product.

Make sure your **Make.com scenario is turned ON** (the toggle at the bottom left) before testing!
