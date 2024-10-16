<!-- #region Page 1 -->
::page{title="Get things done and solve real problems with GenAI"}

Often I hear that LLMs and AI are overblown and nothing more than glorified chatbots or PDF summarizers. Perhaps you've even dabbled in creating your own chatbot or RAG sytem and questioned if AI is truly _just hype_.

This lab will walk you through various hands-on examples, each adding real value to a [fake] coffee company, Tech Cafe. We will cover various technologies and techniques to help you start moving beyond chatbots and implementing the true value of LLMs.

## Learning Objectives

By the end of this Guided Project, you will be able to:

1. Integrate a Large Language Model (LLM) into a Node.js application
2. Implement multiple LangGraph-based workflow for complex and modular code flows
3. Implement image processing using a Large Language Model (LLM)
4. Set up a cron job to periodically update system state
5. Implement dynamic pricing based on AI-driven insights
6. Implement Human-In-The-Loop (HITL)

Let's start by understanding the key components we'll be working with.

## Understanding LangGraph

LangGraph is a library that allows us to create flexible workflows for language models. It provides a way to structure the flow of data and operations when working with LLMs.

## Large Language Models (LLMs)

Large Language Models are AI models trained on vast amounts of text data. They can generate human-like text based on the input they receive. In our project, we'll mainly be using llama3.2 and granite models provided by watsonx.ai.

TODO: fix this segue
In the next steps, we'll implement these concepts to enhance our Tech Cafe application.
<!-- #endregion -->

<!-- #region Introducing Tech Cafe and Our Challenges -->
::page{title="Introducing Tech Cafe and Our Challenges”}

## Welcome to Tech Cafe

Tech Cafe is a bustling coffee chain with multiple locations across the city. Renowned for our artisanal brews and cozy atmosphere, we’ve become a favorite spot for coffee enthusiasts and casual visitors alike. As we continue to grow, we’re encountering new challenges that require innovative solutions.

## The Challenges We’re Facing

	1.	Personalized Customer Experience: With an ever-increasing customer base, providing a personalized touch to each order has become a significant challenge.
	2.	Dynamic Pricing Strategy: Adjusting prices in real-time based on factors like cafe occupancy and demand is complex but essential for maximizing profitability.
	3.	Efficient Order Processing: Managing high volumes of orders without compromising service quality requires a more streamlined system.
	4.	Adaptive Marketing: Staying ahead in a competitive market means continuously adapting our offerings and promotions based on customer preferences and trends.

## Our Solution: An AI-Powered Application

To tackle these challenges, we’re developing an AI-enhanced application that revolutionizes how we handle coffee sales. This app represents a real-life sale at Tech Cafe, integrating advanced AI features to:

	- Enhance Personalization: Use AI to generate personalized messages and recommendations for customers.
	-	Implement Dynamic Pricing: Adjust prices based on real-time data like cafe occupancy.
	-	Streamline Operations: Automate order processing to improve efficiency and reduce errors.
	-	Enable Human-in-the-Loop: Allow human oversight in AI decision-making processes for better control and accuracy.

<<Figure: The user interface of the Tech Cafe application, showcasing the seamless ordering experience.>>

In the next section, we’ll set up the project by cloning the repository and running the base application. Let’s get started!

<!-- #endregion -->

<!-- #region Setting Up the Tech Cafe Application -->
::page{title="Setting Up the Tech Cafe Application”}

In this section, we’ll set up the Tech Cafe application in your development environment. By the end of this step, you’ll have the base application running and ready to start integrating AI-powered features.

## Get the project code

To begin, we’ll clone the Tech Cafe application repository from GitHub. This repository contains all the source code we’ll be working with throughout this project as well as snapshots of various parts within the project.

Instructions:

Run the following command in the terminal to clone the repository:

```bash
# Clone the repository
git clone https://github.com/ibm-developer-skills-network/pqrkh-adapting_to_rapid_advances_in_llms.git

# Change your current directory to the cloned repository
cd pqrkh-adapting_to_rapid_advances_in_llms
```

## Explore the Project Structure

Before we dive into coding, let’s take a moment to understand the structure of the project. Familiarizing yourself with the project layout will make it easier to navigate and modify the code.


TODO: Double check with actual structure and update respectively

Project Structure Overview:

	•	src/: Contains the source code of the application.
	•	controllers/: Holds the controller files responsible for handling HTTP requests and responses.
	•	routes/: Defines the URL mappings to the corresponding controllers.
	•	public/: Contains static assets like HTML, CSS, and JavaScript files that are served directly to the client.
	•	css/: Stylesheets for the application.
	•	js/: Client-side JavaScript scripts.
	•	images/: Images used in the application.
	•	views/: (Placeholder) Where template files would reside if using a templating engine.
	•	models/: (Placeholder) For data models and database interaction.
	•	package.json: Lists the project dependencies and scripts.
	•	tsconfig.json: Configuration file for TypeScript compiler options.
	•	README.md: Provides information about the project.

Note: Some folders like models/ and views/ are placeholders and may be empty or populated in later steps.

## Install Dependencies

The application relies on several Node.js packages. We need to install these dependencies before running the app.

```bash
npm install
```

This command reads the package.json file and installs all the packages listed under dependencies and devDependencies.
What This Does:
-	Downloads the necessary packages like Express, TypeScript, and other utilities.
-	Prepares the environment to run and build the application.

## Run the Base Application

Now that we’ve installed the dependencies, we’re ready to run the application and see it in action.

```bash
npm start
```

What This Does:
-	Compiles the TypeScript code into JavaScript using the tsc compiler.
-	Starts the Express server defined in the src/server.ts file.
-	Listens for incoming HTTP requests on a specified port (default is 3000).

Since we’re running the application in a Cloud IDE environment, we need to expose the port to access it via a browser.
Click the button below to open your app:

::startApplication{port="3000” display="standalone" name="Open Tech Cafe App” route=”/”}

##	Explore the base application
You should now see the Tech Cafe application running in your browser. Feel free to navigate through the app to understand its current functionality.

TODO: Add image

Features to Notice:
	•	Coffee Menu: Displays a list of available coffee options.
	•	Customization Options: Allows you to customize your coffee (e.g., add sugar, milk).
	•	Order Submission: Lets you place an order and receive a confirmation.


Now that you have the base application running and understand its structure, you’re all set to start enhancing it with AI-powered features in the upcoming steps.

Let’s continue our journey to transform Tech Cafe into an intelligent, AI-enhanced application!
<!-- #endregion -->

<!-- #region Setting Up the LangGraph Workflow - Part 1 -->
::page{title="Setting Up the LangGraph Workflow - Part 1"}

## Implementing the LangGraph Workflow

Let's start by creating our LangGraph workflow. We'll do this step-by-step.

1. First, create a new file called `graph.ts` in the `src` directory:

```bash
touch src/graph.ts
```

2. Open the `graph.ts` file:

::openFile{path="src/graph.ts"}

3. Let's start by importing the necessary modules and defining our interfaces:

```typescript
import { Annotation, StateGraph, START, END } from '@langchain/langgraph';
import { generateMessage } from './llm';

interface CoffeeCustomizations {
    creams: number,
    milks: number,
    sugars: number,
    sweeteners: number,
    whippedCream: boolean,
}
```

Let's break this down:

- We import `Annotation`, `StateGraph`, `START`, and `END` from LangGraph. These are the building blocks we'll use to create our workflow.
- We import `generateMessage` from a file called `llm.ts` that we'll create later. This function will handle the interaction with our LLM.
- We define an interface `CoffeeCustomizations` that describes the structure of our coffee customization options.

4. Next, let's define our annotation structure:

```typescript
const CoffeeAnnotation = Annotation.Root({
    customerName: Annotation<string>,
    coffeeName: Annotation<string>,
    customizations: Annotation<CoffeeCustomizations>,
    message: Annotation<string>,
});
```

This `CoffeeAnnotation` defines the structure of the data that will flow through our graph. It includes:
- `customerName`: The name of the customer
- `coffeeName`: The type of coffee ordered
- `customizations`: The customizations for the coffee (using our `CoffeeCustomizations` interface)
- `message`: The AI-generated message (which will be populated later in the workflow)

In the next part, we'll continue building our LangGraph workflow by defining the steps and creating the graph itself.

<!-- #endregion -->

<!-- #region Setting Up the LangGraph Workflow - Part 2 -->
::page{title="Setting Up the LangGraph Workflow - Part 2"}

Now that we have our annotation structure, let's define the step in our workflow and create the graph.

5. Add the following code to `graph.ts`:

```typescript
const stepGenerateMessage = async (state: typeof CoffeeAnnotation.State) => {
    let coffeeOptions = {
        coffeeName: state.coffeeName,
        customizations: state.customizations
    }

    let message = await generateMessage(state.customerName, coffeeOptions)
    state.message = message;

    return state;
}
```

This `stepGenerateMessage` function is a crucial part of our workflow. Let's break it down:

- It takes a `state` parameter of type `CoffeeAnnotation.State`, which contains all the information about the coffee order.
- It creates a `coffeeOptions` object with the coffee name and customizations.
- It calls the `generateMessage` function (which we'll implement later) with the customer name and coffee options.
- It updates the `state` with the generated message and returns the updated state.

6. Now, let's create and export our graph:

```typescript
const coffeeMessageGraph = new StateGraph(CoffeeAnnotation)
    .addNode("generateMessage", stepGenerateMessage)
    .addEdge(START, "generateMessage")
    .addEdge("generateMessage", END)
    .compile();

export const runCoffeeMessageGraph = async (initialInput: typeof CoffeeAnnotation.State) => {
    return coffeeMessageGraph.invoke(initialInput);
}
```

Let's examine this code:

- We create a new `StateGraph` using our `CoffeeAnnotation`.
- We add a node to the graph called "generateMessage" which uses our `stepGenerateMessage` function.
- We add edges to connect the START of the graph to our "generateMessage" node, and from there to the END of the graph.
- We compile the graph to finalize its structure.
- We export a `runCoffeeMessageGraph` function that takes an initial input and invokes our graph with that input.

This completes our LangGraph workflow setup. In the next step, we'll implement the LLM integration to generate our personalized messages.

<!-- #endregion -->

<!-- #region Integrating the Large Language Model - Part 1 -->
::page{title="Integrating the Large Language Model - Part 1"}

## Integrating the Large Language Model

Now that we have our LangGraph workflow set up, let's implement the LLM integration to generate our personalized messages.

1. Create a new file called `llm.ts` in the `src` directory:

```bash
touch src/llm.ts
```

2. Open the `llm.ts` file:

::openFile{path="src/llm.ts"}

3. Let's start by importing the necessary modules and setting up our LLM:

```typescript
import { PromptTemplate } from '@langchain/core/prompts';
import { WatsonxAI } from "@langchain/community/llms/watsonx_ai";

export const PROJECT_ID = "5eddaf93-70b7-4ff3-8b30-583d1ad188fe"
export const IBMCLOUD_API_KEY = process.env.IBMCLOUD_API_KEY

const model = new WatsonxAI({
  ibmCloudApiKey: IBMCLOUD_API_KEY,
  projectId: PROJECT_ID,
  modelId: "meta-llama/llama-3-2-90b-vision-instruct",
  modelParameters: {
    max_new_tokens: 200,
    min_new_tokens: 0,
    stop_sequences: [],
    repetition_penalty: 1,
  },
});
```

Let's break this down:

- We import `PromptTemplate` from LangChain, which we'll use to create our prompt for the LLM.
- We import `WatsonxAI`, which is the specific LLM we'll be using.
- We define our `PROJECT_ID` and get the `IBMCLOUD_API_KEY` from environment variables.
- We create a new `WatsonxAI` model instance with our API key, project ID, and some model parameters.

4. Next, let's define our prompt template:

```typescript
const template = `
<|begin_of_text|>
<|start_header_id|>system<|end_header_id|>
You are a talented and fun barista working at Tech Cafe.

Your main task is to create 1-3 sentences to write on the coffee.

This is a fun thing we do to ensure our customers have a great experiance.

Make sure to reference the coffee they are ordering, their name, and their customizations [indirectly, don't list the customizations].

Do NOT describe the coffee, instead be creative and poetic.

ONLY respond with the message to write on the coffee NOTHING else.
<|eot_id|>
<|start_header_id|>user<|end_header_id|>
A customer just purchased the following coffee:
- Customer Name: {name}
- Type of Coffee: {coffeeName}
- Number of Creams: {creams}
- Number of Milks: {milk}
- Number of Sugars: {sugars}
- Number of Sweeteners: {sweeteners}
- Number of Whipped Cream: {whippedCream}
<|eot_id|>
<|start_header_id|>assistant<|end_header_id|>
`;

const prompt = new PromptTemplate({
  template,
  inputVariables: ['name', 'coffeeName', 'creams', 'milk', 'sugars', 'sweeteners', 'whippedCream'],
});
```

This prompt template is crucial for guiding the LLM to generate appropriate messages. It includes:

- A system message that sets the context and gives instructions to the AI.
- A user message that provides the specific details of the coffee order.
- Placeholders for various input variables that we'll fill in with actual order details.

In the next part, we'll implement the function to generate messages using this prompt and our LLM.

<!-- #endregion -->

<!-- #region Integrating the Large Language Model - Part 2 -->
::page{title="Integrating the Large Language Model - Part 2"}

Now that we have our LLM set up and our prompt template defined, let's implement the function to generate personalized messages.

5. Add the following function to `llm.ts`:

```typescript
export async function generateMessage(name: string, order: any): Promise<string> {
  const promptText = await prompt.format({
    name: name,
    coffeeName: order.coffeeName,
    creams: order.customizations.creams,
    milk: order.customizations.milk,
    sugars: order.customizations.sugars,
    sweeteners: order.customizations.sweeteners,
    whippedCream: order.customizations.whippedCream ? 'Yes' : 'No',
  });

  const response = await model.invoke(promptText);
  return response;
}
```

Let's break down this `generateMessage` function:

- It takes two parameters: `name` (the customer's name) and `order` (an object containing the coffee order details).
- It uses the `prompt.format()` method to fill in the placeholders in our prompt template with the actual order details.
- It then invokes our LLM model with the formatted prompt.
- Finally, it returns the response from the LLM, which will be our personalized message.

This function is what we'll call from our LangGraph workflow to generate the personalized message for each order.

With this, we've completed the setup of our LLM integration. Now, let's update our backend to use this new AI-powered functionality.

<!-- #endregion -->

<!-- #region Updating the Backend - Part 1 -->
::page{title="Updating the Backend - Part 1"}

## Updating the Backend

Now that we have our LangGraph workflow and LLM integration set up, we need to update our backend to use these new features.

1. Open the `src/controllers/coffeeController.ts` file:

::openFile{path="src/controllers/coffeeController.ts"}

2. Update the imports at the top of the file:

```typescript
import { Request, Response } from 'express';
import { runCoffeeMessageGraph } from '../graph';
```

We're importing the `runCoffeeMessageGraph` function that we created earlier.

3. Now, let's update the `processOrder` function to use our new AI-powered workflow:

```typescript
export const processOrder = async (req: Request, res: Response) => {
  const order = req.body;

  console.log('Received Order:', order);

  const graphOutput = await runCoffeeMessageGraph(order);

  console.log('OUTPUT:', graphOutput);

  res.status(200).json({
    message: graphOutput.message,
    orderId: Math.floor(Math.random() * 1000000),
  });
};
```

Let's break down the changes:

- We've made the function `async` because we'll be using `await` inside it.
- We run our `runCoffeeMessageGraph` function with the order data and await its result.
- We log the output from the graph for debugging purposes.
- In the response, we now include the `message` from the graph output, which is our AI-generated personalized message.

These changes allow our backend to process orders through our AI workflow and return personalized messages to the frontend.

In the next part, we'll update the frontend to display these personalized messages.

<!-- #endregion -->

<!-- #region Updating the Frontend -->
::page{title="Updating the Frontend"}

## Updating the Frontend

The final step in our AI enhancement is to update the frontend to display the personalized messages generated by our LLM.

1. Open the `src/public/js/main.js` file:

::openFile{path="src/public/js/main.js"}

2. Find the `sendOrderToBackend` function and update it to handle the new response format:

```javascript
function sendOrderToBackend(orderData) {
  return fetch('/api/coffees/orders', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(orderData)
  });
}
```

3. Now, update the part of the code that handles the response from the backend:

```javascript
sendOrderToBackend(window.currentOrder)
  .then(response => {
    if (response.ok) {
      return response.json();
    } else {
      throw new Error('Failed to process the order.');
    }
  })
  .then(data => {
    console.log('Order processed successfully:', data);
    const customizationModal = document.getElementById('customization-modal');
    closeModal(customizationModal);

    const message = data.message;

    openPurchaseModal(message);
  })
  .catch(error => {
    console.error('Error processing order:', error);
    alert('There was an error processing your order. Please try again.');
  });
```

The key change here is that we're now using `data.message` as the message to display, which is our AI-generated personalized message.

4. Finally, update the `openPurchaseModal` function to display this message:

```javascript
function openPurchaseModal(message) {
  const purchaseModal = document.getElementById('purchase-modal');

  document.getElementById('user-name').textContent = window.currentOrder.customerName;
  document.getElementById('coffee-message').textContent = message;

  const coffeeImage = document.getElementById('coffee-image');
  coffeeImage.src = getCoffeeImage(window.currentOrder.coffeeId);
  coffeeImage.alt = `${window.currentOrder.coffeeName} Image`;

  displayCustomizations();

  purchaseModal.style.display = 'block';
}
```

With these changes, our frontend will now display the AI-generated personalized message for each order.

Congratulations! You've successfully enhanced Tech Cafe with AI-powered personalization. Users will now receive unique, personalized messages with each coffee order, creating a more engaging and memorable experience.




<!-- #endregion -->

<!-- #region Enhancing Tech Cafe with Advanced AI Features -->
::page{title="Enhancing Tech Cafe with Advanced AI Features"}

Welcome back to our exciting Guided Project! In this section, we'll be taking Tech Cafe to the next level by incorporating more advanced AI features. We'll be working with images, creating more complex graphs, and implementing a dynamic pricing system based on cafe occupancy. Let's dive in!

Let's start by enhancing our LangGraph workflow to include image processing capabilities.

<!-- #endregion -->

<!-- #region Implementing Image Processing with LLM -->
::page{title="Implementing Image Processing with LLM"}

Our first task is to implement an image processing feature that will analyze images of the cafe to determine occupancy. This information will be used to dynamically adjust coffee prices.

1. Open the `src/graph.ts` file:

::openFile{path="src/graph.ts"}

2. Add the following imports at the top of the file:

```typescript
import { imageToBase64 } from './utils';
import { scanImage } from './llm';
```

3. Now, let's create a new annotation for our image processing workflow:

```typescript
const ImageAnnotation = Annotation.Root({
    imageURI: Annotation<string>,
    numPeople: Annotation<number>,
});
```

4. Next, we'll create two steps for our image processing workflow:

```typescript
const stepGetLatestImage = async (state: typeof ImageAnnotation.State) => {
    const filePath = '/path/to/your/image.png';
    const base64ImageWithPrefix = imageToBase64(filePath);
    state.imageURI = base64ImageWithPrefix;
    return state;
}

const stepScanImage = async (state: typeof ImageAnnotation.State) => {
    console.log('stepScanImage');
    console.log(state);
    const scanResults = await scanImage(state.imageURI);
    state.numPeople = scanResults;
    return state;
}
```

5. Now, let's create our image processing graph:

```typescript
const scanImageGraph = new StateGraph(ImageAnnotation)
    .addNode("stepGetLatestImage", stepGetLatestImage)
    .addNode("stepScanImage", stepScanImage)
    .addEdge(START, "stepGetLatestImage")
    .addEdge("stepGetLatestImage", "stepScanImage")
    .addEdge("stepScanImage", END)
    .compile();

export const runScanImageGraph = async (initialInput: Partial<typeof ImageAnnotation.State> = {}) => {
    return scanImageGraph.invoke(initialInput);
};
```

This new graph will get the latest image of the cafe, convert it to a base64 string, and then use our LLM to analyze the image and count the number of people.

In the next step, we'll implement the `scanImage` function in our `llm.ts` file.

<!-- #endregion -->

<!-- #region Implementing the scanImage Function -->
::page{title="Implementing the scanImage Function"}

Now that we've set up our image processing graph, we need to implement the `scanImage` function that will use our LLM to analyze the image.

1. Open the `src/llm.ts` file:

::openFile{path="src/llm.ts"}

2. Add the following imports at the top of the file:

```typescript
import axios from 'axios';
import qs from 'qs';
```

3. Now, let's implement the `scanImage` function:

```typescript
export async function scanImage(imageURI: string): Promise<number> {
  console.log('STARTING SCAN');

  const systemPrompt = `Return how many people you see in the image. Only return the number with no punctuation or quotes and be as accurate as possible.`;

  const getToken = async () => {
    const data = qs.stringify({
      'grant_type': 'urn:ibm:params:oauth:grant-type:apikey',
      'apikey': IBMCLOUD_API_KEY
    });

    const config = {
      method: 'post',
      url: 'https://iam.cloud.ibm.com/identity/token',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      data: data
    };

    try {
      const response = await axios(config);
      return response?.data?.access_token;
    } catch (error) {
      console.error('Error fetching token:', error);
    }
  };

  const accessToken = await getToken();

  const url = "https://us-south.ml.cloud.ibm.com/ml/v1/text/chat?version=2023-05-29";
  const headers = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "Authorization": `Bearer ${accessToken}`
  };
  
  const body = {
    messages: [
      {
        role: "system",
        content: [
          {
            type: "text",
            text: systemPrompt
          }
        ]
      },
      {
        role: "user",
        content: [
          {
            type: "image_url",
            image_url: {
              url: imageURI
            }
          }
        ]
      }
    ],
    project_id: PROJECT_ID,
    model_id: "meta-llama/llama-3-2-90b-vision-instruct",
    decoding_method: "greedy",
    min_new_tokens: 0,
    max_tokens: 3
  };

  try {
    const response = await axios.post(url, body, { headers });
    const resultNumber = response.data?.choices[0]?.message?.content;
    return parseInt(resultNumber);
  } catch (error: any) {
    console.error('Error fetching data:', error);
    throw new Error("Non-200 response");
  }
}
```

This function sends a request to the WatsonX API with our image and prompts it to count the number of people in the image. It then returns this number.

In the next step, we'll set up a cron job to periodically run our image processing workflow and update our coffee prices based on the results.

<!-- #endregion -->

<!-- #region Setting Up a Cron Job for Dynamic Pricing -->
::page{title="Setting Up a Cron Job for Dynamic Pricing"}

Now that we have our image processing workflow set up, let's create a cron job that will periodically run this workflow and update our coffee prices based on the cafe's occupancy.

1. Open the `src/server.ts` file:

::openFile{path="src/server.ts"}

2. Add the following imports at the top of the file:

```typescript
import cron from 'node-cron';
import { runScanImageGraph } from './graph';
import { setCoffeePrice } from './controllers/coffeeController';
```

3. At the bottom of the file, add the following cron job:

```typescript
cron.schedule('* * * * *', async () => {
    console.log('Task is running every minute');
    const results = await runScanImageGraph();

    console.log("RESULTS ARE");
    console.log(results);

    if (results.numPeople > 20) {
      console.log("More than 20 people found - initiating sale");
      setCoffeePrice(0.7);
    }
});
```

This cron job will run every minute. It will scan the cafe image, and if there are more than 20 people in the cafe, it will initiate a 30% off sale by calling `setCoffeePrice(0.7)`.

4. Now, let's implement the `setCoffeePrice` function. Open the `src/controllers/coffeeController.ts` file:

::openFile{path="src/controllers/coffeeController.ts"}

5. Add the following function to the file:

```typescript
export const setCoffeePrice = (amount = 1) => {
  coffees[0].price = 3 * amount;
  coffees[1].price = 4 * amount;
  coffees[2].price = 4.5 * amount;
  coffees[3].price = 3.5 * amount;
};
```

This function will adjust the prices of all coffees by the given amount. An amount of 1 keeps the prices the same, while 0.7 applies a 30% discount.

With these changes, our Tech Cafe application now uses advanced AI features to dynamically adjust prices based on the cafe's occupancy. This creates a more realistic and responsive pricing model that can adapt to real-world conditions.

In the next section, we'll update our frontend to reflect these dynamic price changes.

<!-- #endregion -->

<!-- #region Updating the Frontend for Dynamic Pricing -->
::page{title="Updating the Frontend for Dynamic Pricing"}

Now that we have implemented dynamic pricing on the backend, we need to update our frontend to reflect these price changes in real-time.

1. Open the `src/public/js/main.js` file:

::openFile{path="src/public/js/main.js"}

2. Modify the `DOMContentLoaded` event listener to periodically fetch the latest coffee prices:

```javascript
document.addEventListener('DOMContentLoaded', () => {
  function fetchAndDisplayCoffees() {
    fetch('/api/coffees')
      .then(response => response.json())
      .then(data => {
        const container = document.getElementById('coffees-container');
        container.innerHTML = ''; // Clear existing content
        data.forEach(coffee => {
          const coffeeDiv = document.createElement('div');
          coffeeDiv.classList.add('coffee-item');

          coffeeDiv.innerHTML = `
            <h3>${coffee.name}</h3>
            <p>Price: $<span class="coffee-price">${coffee.price.toFixed(2)}</span></p>
            <button class="purchase-button" data-coffee-name="${coffee.name}" data-coffee-id="${coffee.id}">Purchase</button>
          `;

          container.appendChild(coffeeDiv);
        });

        // Re-attach event listeners to new buttons
        const purchaseButtons = document.querySelectorAll('.purchase-button');
        purchaseButtons.forEach(button => {
          button.addEventListener('click', handlePurchase);
        });
      })
      .catch(error => {
        console.error('Error fetching coffees:', error);
      });
  }

  // Initial fetch
  fetchAndDisplayCoffees();

  // Fetch prices every 30 seconds
  setInterval(fetchAndDisplayCoffees, 30000);
});
```

This modification will fetch the latest coffee prices every 30 seconds and update the display accordingly.

3. To make the price changes more noticeable, let's add a simple animation when prices change. Add the following CSS to `src/public/css/styles.css`:

::openFile{path="src/public/css/styles.css"}

```css
@keyframes priceChange {
  0% { color: inherit; }
  50% { color: #ff0000; }
  100% { color: inherit; }
}

.price-changed {
  animation: priceChange 1s;
}
```

4. Now, let's modify our `fetchAndDisplayCoffees` function to apply this animation when prices change:

```javascript
function fetchAndDisplayCoffees() {
  fetch('/api/coffees')
    .then(response => response.json())
    .then(data => {
      const container = document.getElementById('coffees-container');
      data.forEach((coffee, index) => {
        const coffeeDiv = container.children[index];
        if (coffeeDiv) {
          const priceSpan = coffeeDiv.querySelector('.coffee-price');
          const currentPrice = parseFloat(priceSpan.textContent);
          const newPrice = coffee.price;
          
          if (currentPrice !== newPrice) {
            priceSpan.textContent = newPrice.toFixed(2);
            priceSpan.classList.add('price-changed');
            setTimeout(() => priceSpan.classList.remove('price-changed'), 1000);
          }
        } else {
          // If the coffee item doesn't exist, create it (same as before)
        }
      });
    })
    .catch(error => {
      console.error('Error fetching coffees:', error);
    });
}
```

These changes will make the price updates more visible to users, creating a more dynamic and engaging experience.

Congratulations! You've successfully implemented advanced AI features in Tech Cafe, including image processing for occupancy detection and dynamic pricing based on real-time data. This creates a more realistic and responsive application that can adapt to changing conditions.

In the next section, we'll summarize what we've learned and discuss potential future enhancements.

<!-- #endregion -->

<!-- #region Summary and Future Enhancements -->
::page{title="Summary and Future Enhancements"}

Congratulations on completing this advanced section of the Guided Project! Let's recap what we've accomplished and discuss some potential future enhancements.

## Summary of Achievements

In this part of the project, we've:

1. Implemented image processing using a Large Language Model (LLM) to detect cafe occupancy.
2. Created a more complex LangGraph workflow that includes image analysis.
3. Set up a cron job to periodically update the system state based on AI-driven insights.
4. Implemented a dynamic pricing system that adjusts coffee prices based on cafe occupancy.
5. Updated the frontend to reflect real-time price changes with animations.

These enhancements have transformed Tech Cafe from a simple ordering system into a more sophisticated application that can adapt to real-world conditions.

## Potential Future Enhancements

While we've added significant functionality to Tech Cafe, there are always opportunities for further improvement. Here are some ideas for future enhancements:

1. **More Complex Pricing Algorithm**: Instead of a simple threshold for price changes, implement a more nuanced algorithm that takes into account factors like time of day, day of the week, and historical data.

2. **User Authentication**: Implement a user authentication system to allow for personalized experiences and order history.

3. **Loyalty Program**: Create a loyalty program that rewards frequent customers with discounts or free items.

4. **Mobile App**: Develop a mobile app version of Tech Cafe for iOS and Android platforms.

5. **Integration with Real Payment Systems**: Implement actual payment processing using a service like Stripe or PayPal.

6. **Order Queueing System**: Develop a system to manage order queues and estimated wait times.

7. **AI-Powered Recommendations**: Use the LLM to provide personalized coffee recommendations based on user preferences and order history.

## Call to Action

Now that you've seen the power of AI in enhancing web applications, why not explore more AI services on the IBM Cloud? Visit [IBM Watson Services](https://www.ibm.com/watson) to discover a wide range of AI and machine learning tools that you can integrate into your own projects.

Thank you for participating in this Guided Project. We hope you've gained valuable insights into integrating AI features into web applications. Keep exploring, learning, and building amazing things!


Here are the next 5 pages for the Guided Project, focusing on implementing Human in the Loop functionality:

<!-- #endregion -->

<!-- #region Understanding Human in the Loop -->
::page{title="Understanding Human in the Loop"}

## What is Human in the Loop?

Human in the Loop (HITL) is an AI approach that combines machine learning algorithms with human intervention. In this model, humans play a crucial role in training, tuning, and testing AI systems to improve their accuracy and reliability.

Key aspects of HITL:

1. Machine learning algorithms process data and make initial predictions or decisions
2. Human experts review and correct the AI's output when needed
3. The AI system learns from these corrections, continuously improving its performance

## Why Use Human in the Loop?

HITL offers several advantages:

1. Improved accuracy: Human expertise helps refine AI predictions
2. Handling edge cases: Humans can manage complex scenarios the AI may struggle with
3. Building trust: Human oversight increases confidence in AI systems
4. Continuous learning: The AI improves over time through human feedback

## Implementing HITL in Tech Cafe

We'll enhance our Tech Cafe app by adding HITL for the dynamic pricing feature:

1. The AI will suggest price adjustments based on cafe occupancy
2. A human administrator will review and approve/modify these suggestions
3. The system will learn from admin decisions to improve future pricing recommendations

Let's start by modifying our backend to support this new workflow.

<!-- #endregion -->

<!-- #region Modifying the Backend for HITL -->
::page{title="Modifying the Backend for HITL"}

First, we'll update our `graph.ts` file to support the HITL workflow:

::openFile{path="src/graph.ts"}

Add the following code to the existing `graph.ts` file:

```typescript
// ... existing imports and code ...

const ImageAnnotation = Annotation.Root({
    imageURI: Annotation<string>,
    numPeople: Annotation<number>,
    salePercentage: Annotation<number>,
    adminApproved: Annotation<boolean>,
});

// ... existing stepGetLatestImage and stepScanImage functions ...

const stepSuggestSale = async (state: typeof ImageAnnotation.State) => {
    if (state.numPeople > 40) {
        state.salePercentage = 30;
    } else if (state.numPeople > 20) {
        state.salePercentage = 15;
    } else {
        state.salePercentage = 0;
    }
    state.adminApproved = false;
    return state;
}

const stepWaitForAdminApproval = async (state: typeof ImageAnnotation.State) => {
    // This step will be handled by the admin interface
    return state;
}

const stepApplySale = async (state: typeof ImageAnnotation.State) => {
    if (state.adminApproved) {
        setCoffeePrice(state.salePercentage);
    }
    return state;
}

const scanImageGraph = new StateGraph(ImageAnnotation)
    .addNode("stepGetLatestImage", stepGetLatestImage)
    .addNode("stepScanImage", stepScanImage)
    .addNode("stepSuggestSale", stepSuggestSale)
    .addNode("stepWaitForAdminApproval", stepWaitForAdminApproval)
    .addNode("stepApplySale", stepApplySale)
    .addEdge(START, "stepGetLatestImage")
    .addEdge("stepGetLatestImage", "stepScanImage")
    .addEdge("stepScanImage", "stepSuggestSale")
    .addEdge("stepSuggestSale", "stepWaitForAdminApproval")
    .addEdge("stepWaitForAdminApproval", "stepApplySale")
    .addEdge("stepApplySale", END)
    .compile();

// ... existing export statements ...
```

This modification introduces a new step in our workflow where the AI suggests a sale percentage, but waits for admin approval before applying it.

<!-- #endregion -->

<!-- #region Creating the Admin Interface -->
::page{title="Creating the Admin Interface"}

Now, let's create an admin interface to review and approve sale suggestions:

1. Create a new file `src/public/admin.html`:

::openFile{path="src/public/admin.html"}

Add the following content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tech Cafe Admin</title>
    <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; padding: 20px; }
        h1 { color: #333; }
        #saleInfo { margin-top: 20px; padding: 10px; border: 1px solid #ddd; }
        button { margin-top: 10px; padding: 5px 10px; }
    </style>
</head>
<body>
    <h1>Tech Cafe Admin Dashboard</h1>
    <div id="saleInfo">
        <h2>Current Sale Suggestion</h2>
        <p>Number of people: <span id="numPeople"></span></p>
        <p>Suggested sale percentage: <span id="salePercentage"></span>%</p>
        <button id="approveBtn">Approve</button>
        <button id="modifyBtn">Modify</button>
        <button id="rejectBtn">Reject</button>
    </div>
    <script src="/js/admin.js"></script>
</body>
</html>
```

2. Create a new file `src/public/js/admin.js`:

::openFile{path="src/public/js/admin.js"}

Add the following content:

```javascript
document.addEventListener('DOMContentLoaded', () => {
    fetchSaleInfo();

    document.getElementById('approveBtn').addEventListener('click', () => approveSale());
    document.getElementById('modifyBtn').addEventListener('click', () => modifySale());
    document.getElementById('rejectBtn').addEventListener('click', () => rejectSale());
});

function fetchSaleInfo() {
    fetch('/api/admin/sale-info')
        .then(response => response.json())
        .then(data => {
            document.getElementById('numPeople').textContent = data.numPeople;
            document.getElementById('salePercentage').textContent = data.salePercentage;
        })
        .catch(error => console.error('Error fetching sale info:', error));
}

function approveSale() {
    fetch('/api/admin/approve-sale', { method: 'POST' })
        .then(() => alert('Sale approved!'))
        .then(() => fetchSaleInfo())
        .catch(error => console.error('Error approving sale:', error));
}

function modifySale() {
    const newPercentage = prompt('Enter new sale percentage:');
    if (newPercentage !== null) {
        fetch('/api/admin/modify-sale', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ salePercentage: parseInt(newPercentage) })
        })
        .then(() => alert('Sale modified!'))
        .then(() => fetchSaleInfo())
        .catch(error => console.error('Error modifying sale:', error));
    }
}

function rejectSale() {
    fetch('/api/admin/reject-sale', { method: 'POST' })
        .then(() => alert('Sale rejected!'))
        .then(() => fetchSaleInfo())
        .catch(error => console.error('Error rejecting sale:', error));
}
```

<!-- #endregion -->

<!-- #region Implementing Admin Routes -->
::page{title="Implementing Admin Routes"}

Now, let's create the backend routes to handle admin actions:

1. Create a new file `src/routes/adminRoutes.ts`:

::openFile{path="src/routes/adminRoutes.ts"}

Add the following content:

```typescript
import express from 'express';
import { getSaleInfo, approveSale, modifySale, rejectSale } from '../controllers/adminController';

const router = express.Router();

router.get('/sale-info', getSaleInfo);
router.post('/approve-sale', approveSale);
router.post('/modify-sale', modifySale);
router.post('/reject-sale', rejectSale);

export default router;
```

2. Create a new file `src/controllers/adminController.ts`:

::openFile{path="src/controllers/adminController.ts"}

Add the following content:

```typescript
import { Request, Response } from 'express';
import { getScanImageGraphState, updateScanImageGraphState, runScanImageGraphState } from '../graph';

export const getSaleInfo = async (req: Request, res: Response) => {
    const state = await getScanImageGraphState();
    res.json({
        numPeople: state.values.numPeople,
        salePercentage: state.values.salePercentage
    });
};

export const approveSale = async (req: Request, res: Response) => {
    await updateScanImageGraphState({ adminApproved: true });
    await runScanImageGraphState();
    res.sendStatus(200);
};

export const modifySale = async (req: Request, res: Response) => {
    const { salePercentage } = req.body;
    await updateScanImageGraphState({ salePercentage, adminApproved: true });
    await runScanImageGraphState();
    res.sendStatus(200);
};

export const rejectSale = async (req: Request, res: Response) => {
    await updateScanImageGraphState({ salePercentage: 0, adminApproved: false });
    await runScanImageGraphState();
    res.sendStatus(200);
};
```

3. Update `src/server.ts` to include the new admin routes:

::openFile{path="src/server.ts"}

Add the following lines:

```typescript
import adminRoutes from './routes/adminRoutes';

// ... existing code ...

app.use('/api/admin', adminRoutes);

// ... rest of the existing code ...
```

<!-- #endregion -->

<!-- #region Testing the HITL Implementation -->
::page{title="Testing the HITL Implementation"}

Now that we have implemented the Human in the Loop functionality, let's test it:

1. Start your server:

```bash
npm start
```

2. Open a web browser and navigate to `http://localhost:3000/admin.html`

3. You should see the Admin Dashboard with the current sale suggestion.

4. Try approving, modifying, and rejecting sales. Verify that the actions are reflected in the system.

5. Open another browser tab and go to `http://localhost:3000`. Check if the coffee prices are updated when you approve a sale.

6. Monitor your server console to see the HITL process in action.

Congratulations! You have successfully implemented Human in the Loop functionality in Tech Cafe. This addition allows for more accurate and controlled dynamic pricing, combining the power of AI with human expertise.
<!-- #endregion -->

