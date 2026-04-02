# TechBot AI - A Smart Sales & Support Assistant

## Overview

**TechBot AI** is a smart sales and support assistant for a premium computer hardware & electronics store called **TechStore BD**. This project connects an automated **n8n backend** with a custom-developed **frontend** interface to provide a seamless conversational experience for online customers. I tested this system using the **Groq LLaMA 3.3-70B model** to handle customer inquiries, negotiate, and take orders naturally. Behind the scenes, the chatbot instantly writes all chat histories and confirmed orders to **Google Sheets**, ensuring the store owners can easily track everything in real time.

## Video Demo

Watch this short demonstration to see how **TechBot AI** handles inquiries!

<video src="./readme-media/TechBot AI Demo.mp4" controls="controls" style="max-width: 100%;">
  Your browser does not support the video tag.
</video>

## Technologies Utilized

* **n8n**: The powerful workflow automation tool that acts as my main backend logic engine.
* **HTML, CSS, JavaScript**: Used to create a beautiful, dynamic, and fully responsive custom frontend.
* **Google Sheets OAuth**: Handles secure database connections to log conversations and capture orders.
* **OpenAI-Compatible APIs**: For processing natural language. Tested using Groq's extremely fast inference APIs.

## Project Structure

```text
TechStore BD/
├── assets/   
│   ├── AI Chatbot.svg               # Chatbot avatar graphic
│   ├── Animated AI Chatbot.gif      # Animated illustration for interface
│   └── TechStore BD.svg             # Store logo used in the interface
├── frontend/
│   └── techstore-chatbot.html       # The main interactive chat UI
├── n8n/
│   └── AI Chatbot - TechStore BD.json # Downloadable n8n workflow file
├── readme-media/                    # Contains all README images and videos
├── TechBot AI Inquiry Sheet.xlsx    # Ready-to-upload spreadsheet template
└── README.md
```

## How TechBot AI Works

Whenever a customer types a message on the frontend, it is immediately sent to the **n8n Automated Backend**. The backend identifies the user using a unique `Session ID` and retrieves their previous chat history. All of this context is bundled with a strict system prompt (telling the AI it is a TechStore BD assistant handling retail electronics) and sent to the **AI Model** (e.g., LLaMA 3.3). The AI processes the request, formulates a natural response, assigns an intent tag (like `[INTENT: order]`), and streams the answer back to the user within seconds. Concurrently, the backend automatically logs the entire chat history into **Sheet1**, and extracts only the confirmed orders and call bookings into **Sheet2** for instant fulfillment tracking!

### Backend Architecture (n8n Workflow)

To make this happen, the n8n backend processes the data through a series of specialized nodes:

![n8n Workflow](./readme-media/n8n%20Workflow.png)

**Step-by-step logic:**

1. A **Webhook** receives the user's message from the frontend.
2. The **Code Node** (`Build Messages`) builds the message history and formats the system prompt.
3. An **HTTP Request Node** (`OpenAI Chat`) contacts the AI API asynchronously.
4. A **Code Node** (`Parse & Clean Output`) followed by a **Switch Node** (`Route by Intent`) clears out background data and routes the conversation based on the intent tag (`order`, `booking`, `cancel`, `inquiry`).
5. **Google Sheets Nodes** (e.g., `Chat History Data` & `Confirmed Inquiry Data`) instantly capture the data. Everything gets dumped into a master chat log, and specific actions (like confirmed orders) get isolated and saved into a special tracker sheet.

## Frontend Interface

The chatbot UI was designed from scratch to be immersive and responsive to any device screen size.

**How it connects to n8n:**
The frontend communicates directly with the n8n backend using a RESTful POST request. When a user sends a message, a background JavaScript function captures the text and transmits it as JSON payload to your secure n8n Webhook URL. It then waits for the AI's response, dynamically parsing the returned text and rendering the markdown into beautiful HTML chat bubbles instantly!

**Desktop View:** The spacious chat view for PC displays.

![Desktop View](./readme-media/TechBot%20AI%20(Desktop%20View).png)

**Tablet View:** Optimized spacing and padding for tablet navigation.

![Tablet View](./readme-media/TechBot%20AI%20(Tablet%20View).png)

**Mobile View:** A tightly compacted interface perfect for smaller phone screens.

![Mobile View](./readme-media/TechBot%20AI%20(Mobile%20View).png)

## Google Sheets Database Mapping

The system automatically tracks the entire customer conversations instantly and stores data on Google Sheets. The standard headers used are:

* **`Timestamp`**: The exact date and time the message was processed.
* **`Session ID`**: A unique ID linking all messages sent by a single user in one session.
* **`User Message`**: What the customer specifically typed or requested.
* **`AI Reply`**: The exact response returned by the AI Chatbot.
* **`Intent`**: The AI-classified action for the interaction (e.g., `inquiry`, `order`, `booking`, or `cancel`).
* **`Status`**: Tracks if an order or booking is `pending`, `confirmed`, or `cancelled`.
* **`Order Data`**: Formatted summary text containing the customer's delivery info, product names, and pricing.

**Sheet 1: Master Chatbot History**

Sheet1 records the step-by-step history of every interaction so administrators can review the full context of a conversation at any time.
![Sheet1 - Chatbot History](./readme-media/Google%20Sheets%20View%20(Sheet1%20-%20Chatbot%20History).png)

**Sheet 2: Confirmed Inquiries**

Sheet2 is the clean, filtered list that isolated final, confirmed customer interactions (like when a customer clicks "YES" to confirm their order or booked a call).
![Sheet2 - Confirmed Inquiry](./readme-media/Google%20Sheets%20View%20(Sheet2%20-%20Confirmed%20Inquiry).png)

*(**Note:** I have attached `TechBot AI Inquiry Sheet.xlsx` to the repository. You can use this template to skip formatting your own spreadsheet! I also left some of my previous test conversations inside it to show exactly how the logs are supposed to look.)*

## Quick Setup & Deployment Guide

For security, sensitive keys and webhooks have been anonymized in this public repo. Follow these steps to spin the bot up locally:

**1. Configure the Frontend**

* Open `frontend/techstore-chatbot.html`.
* Search for the javascript variable `webhookUrl`.
* Replace `http://localhost:5678/webhook/chatbot` with the live production URL of your active n8n webhook.

**2. Configure Google Sheets**

* Take the `TechBot AI Inquiry Sheet.xlsx` file and upload it into your Google Drive.
* Open it and save it natively as a Google Sheet.
* Copy the unique Sheet ID from the URL (the mix of random letters/numbers).

**3. Configure the n8n Workflow**

* Import `n8n/AI Chatbot - TechStore BD.json` into your n8n workspace.
* Check the HTTP Request node and replace:
  * `YOUR_OPENAI_COMPATIBLE_API_URL_HERE` with your provider's chat completions endpoint.
  * `YOUR_API_KEY_HERE` with your proper Authorization token (Bearer).
  * `YOUR_MODEL_NAME_HERE` with your chosen model identifier.
* Check all the **Google Sheets Nodes**. Connect your Google Sheets OAuth Credentials, and replace all instances of `YOUR_GOOGLE_SHEET_ID_HERE` with the Sheet ID you generated in Step 2.

## Author

* **Md. Tausif Jafar**
* **Email** : [mdtausifjafar@gmail.com](mailto:mdtausifjafar@gmail.com)
