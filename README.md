# Luxury AI Stylist - WhatsApp Concierge

An enterprise-grade, agentic AI stylist built to operate over WhatsApp. This system serves as a highly intelligent, proactive luxury concierge that understands user intent, performs live web searches for product matching, and curates highly personalized, formatted responses using advanced LLM pipelines.

## Features
- **WhatsApp Integration**: Seamlessly communicates via the Twilio WhatsApp API.
- **Multimodal Capable**: Designed to process both text and images.
- **Agentic Search**: Employs an LLM-based pre-processor to convert vague user moods or requests into precise e-commerce search queries.
- **Live Web Scraping**: Integrates with the Tavily Search API to scrape live product data and image URLs directly from the official website.
- **Strict Brand Persona**: Powered by Groq (Llama 3.3 70B), the agent strictly adheres to a luxurious, consultative brand voice while remaining within Twilio's text length constraints.
- **Zero-Timeout Architecture**: Utilizes asynchronous Webhook Responses to prevent third-party platform timeouts during extended search operations.

## Architecture (Make.com)

This system is completely serverless and orchestrated via Make.com using a highly robust 5-step pipeline:

1. **Twilio Webhook**: Listens 24/7 for incoming WhatsApp messages and media.
2. **Webhook Response (200 OK)**: Instantly replies with an empty XML `<Response></Response>` to Twilio, preventing 15-second timeouts while the LLM processes data in the background.
3. **Groq Pre-Processor (Llama-3.3-70b)**: Analyzes the raw, conversational user input and extracts 2-4 highly targeted eCommerce search keywords.
4. **Tavily Search API**: Executes a targeted site-search (`site:us.louisvuitton.com [Keywords]`) to fetch live links, product titles, and active image CDNs.
5. **Groq Curating Engine**: Ingests the raw JSON search results, selects the most optimal product, and curates a luxurious response containing the exact product URL and an unfurled image link.
6. **Twilio Push**: Delivers the final formatted payload back to the user's WhatsApp.

## Deployment Guide

### Prerequisites
- Make.com Account (Free Tier suitable)
- Twilio Account + WhatsApp Sandbox Activated (Free Trial)
- GroqCloud API Key (Free)
- Tavily Search API Key (Free)

### Setup Instructions
1. **Import Blueprint**: Import the `blueprint.json` file into a new Make.com scenario.
2. **Configure Twilio**: 
   - Connect your Twilio webhook in the first module.
   - Ensure the "Webhook Response" module immediately follows it with `text/xml` headers and empty `<Response>` body.
3. **Configure Groq (Pre-Processor)**:
   - Insert your Groq API Key as a Bearer token.
   - Use the `llama-3.3-70b-versatile` model.
4. **Configure Tavily**:
   - Insert your Tavily API Key.
   - Map the `message.content` from the Groq Pre-Processor into the search query.
5. **Configure Groq (Curator)**:
   - Insert your Groq API Key.
   - Map the `results[]` array from Tavily into the system prompt's Live Search Data section. 

## Future Roadmap
- **Persistent Memory**: (In Progress) Connecting to Supabase via Make.com to store chat history and user profiles (phone numbers, past purchases) for long-term contextual awareness.
- **Autonomous Booking**: Integrating scheduling APIs to allow the agent to book in-store appointments autonomously based on real-time availability.
