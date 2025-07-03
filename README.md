# pythonweather
from typing import List

# List of allowed cities
ALLOWED_CITIES = ["Lahore", "Karachi", "Islamabad"]

# Custom tool behavior function
def get_weather_custom(city: str) -> str:
    if city not in ALLOWED_CITIES:
        return f"Sorry, weather data for '{city}' is not available right now."

    # Simulate fetching weather from a fake API
    fake_weather_data = {
        "Lahore": "38°C, Sunny",
        "Karachi": "34°C, Humid",
        "Islamabad": "31°C, Cloudy"
    }

    weather = fake_weather_data.get(city, "No data")
    return f"The current weather in {city} is {weather}."

# Simulate agent calling the tool
print(get_weather_custom("Lahore"))
print(get_weather_custom("New York"))

Result 
The current weather in Lahore is 38°C, Sunny.
Sorry, weather data for 'New York' is not available right now.


 Step 1: Define the Function (tool)

 import openai
import json
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

# Step 1: Define your tool (function) spec
functions = [
    {
        "name": "get_weather",
        "description": "Get the current weather in a city",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {
                    "type": "string",
                    "description": "Name of the city"
                }
            },
            "required": ["city"]
        }
    }
]

Step 2: Custom Tool Behavior Function (logic)

# Whitelisted cities and fake weather data
ALLOWED_CITIES = ["Lahore", "Karachi", "Islamabad"]
FAKE_WEATHER = {
    "Lahore": "38°C, Sunny",
    "Karachi": "34°C, Humid",
    "Islamabad": "31°C, Cloudy"
}

def get_weather_custom(city: str) -> str:
    if city not in ALLOWED_CITIES:
        return f"Sorry, weather data for '{city}' is not available."
    return f"The current weather in {city} is {FAKE_WEATHER.get(city)}."

Step 3: Let the Model Decide When to Use the Tool

# User prompt
user_input = "What's the weather like in Lahore?"

# Step 3: Call OpenAI Chat API with function info
response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[{"role": "user", "content": user_input}],
    functions=functions,
    function_call="auto",  # let GPT decide when to use the function
)

# Step 4: Check if GPT wants to call a function
message = response["choices"][0]["message"]

if message.get("function_call"):
    func_name = message["function_call"]["name"]
    args = json.loads(message["function_call"]["arguments"])
    
    # Call your custom logic
    if func_name == "get_weather":
        result = get_weather_custom(**args)
        
        # Step 5: Send final response to model
        followup = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {"role": "user", "content": user_input},
                message,  # tool call
                {"role": "function", "name": func_name, "content": result}
            ]
        )
        
        print(followup["choices"][0]["message"]["content"])

result 
The current weather in Lahore is 38°C, Sunny.


Step 1: Install LangChain
pip install langchain openai

Step 2: Define the Custom Tool

from langchain.agents import Tool
from langchain.agents import initialize_agent
from langchain.chat_models import ChatOpenAI
from langchain.schema import AgentType

# Simulated weather and whitelist
ALLOWED_CITIES = ["Lahore", "Karachi", "Islamabad"]
FAKE_WEATHER = {
    "Lahore": "38°C, Sunny",
    "Karachi": "34°C, Humid",
    "Islamabad": "31°C, Cloudy"
}

# Custom logic (tool function)
def get_weather(city: str) -> str:
    if city not in ALLOWED_CITIES:
        return f"Sorry, I can't fetch weather for {city} right now."
    return f"The current weather in {city} is {FAKE_WEATHER[city]}."

# Wrap tool for LangChain
weather_tool = Tool(
    name="get_weather",
    func=lambda city: get_weather(city),
    description="Returns the current weather for a Pakistani city like Lahore, Karachi, or Islamabad."
)

Step 3: Create the Agent

# Use OpenAI GPT-4 or GPT-3.5
llm = ChatOpenAI(model="gpt-4", temperature=0)

# Initialize agent with tool
agent = initialize_agent(
    tools=[weather_tool],
    llm=llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True
)

Step 4: Ask a Question (Trigger the Tool)

agent.run("What's the weather in Lahore?")
agent.run("What's the weather in New York?")

result 
The current weather in Lahore is 38°C, Sunny.
Sorry, I can't fetch weather for New York right now.


 
