## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:

### DESIGN STEPS:
### STEP 1:
Import Necessary Libraries: Import os, openai, json, and dotenv to handle API key management, function calling, and JSON data parsing.

### STEP 2:
Load Environment Variables: Use dotenv to securely load the OpenAI API key from the environment.

### STEP 3:
Define the calculate_cylinder_volume Function: Create a function named calculate_cylinder_volume(radius, height) to calculate the volume of a cylinder. The function takes the radius and height as inputs, computes the volume using the cylinder volume formula, and returns the radius, height, and volume in JSON format.

### STEP 4:
Define the Function Schema: Create a function definition in the functions list that describes:

Function name: calculate_cylinder_volume Purpose: Calculate the volume of a cylinder Parameters: radius (number) – Radius of the cylinder height (number) – Height of the cylinder Mark both parameters as required to ensure valid input.

### STEP 5:
Provide User Input: Create a user message containing a natural language request, such as: "Find the volume of a cylinder with radius 7 and height 10."

This message is added to the messages list and serves as the input for the OpenAI model to determine whether the function should be called.

### STEP 6:
Call OpenAI API: The program calls openai.ChatCompletion.create with: The model "gpt-3.5-turbo". The messages list. The function definition in functions. function_call="auto" so OpenAI automatically decides when to invoke the function.

### STEP 7:
Extract Arguments and Compute Result: Extract the function arguments from the API's function call response. Pass arguments to the calculate() function to compute the result.

### STEP 8:
Print the Results: Print the response from the function and the final result.
### PROGRAM:
```
import os
import json
import math
from dotenv import load_dotenv, find_dotenv
from openai import OpenAI

print("Name: Dharani A")
print("Reg No: 212225040067")

load_dotenv(find_dotenv())
client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])

def calculate_cylinder_volume(radius, height):
    volume = math.pi * radius * radius * height
    return json.dumps({
        "radius": radius,
        "height": height,
        "volume": volume
    })

tools = [
    {
        "type": "function",
        "name": "calculate_cylinder_volume",
        "description": "Calculate the volume of a cylinder",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {
                    "type": "number",
                    "description": "Radius of the cylinder"
                },
                "height": {
                    "type": "number",
                    "description": "Height of the cylinder"
                }
            },
            "required": ["radius", "height"],
            "additionalProperties": False
        },
        "strict": True
    }
]

question = "Find the volume of a cylinder with radius 7 and height 10"

response = client.responses.create(
    model="gpt-4.1-mini",
    input=question,
    tools=tools
)

function_call = next(
    item for item in response.output
    if item.type == "function_call"
)

args = json.loads(function_call.arguments)
observation = calculate_cylinder_volume(**args)

print("\nFunction Output:")
print(observation)

final_response = client.responses.create(
    model="gpt-4.1-mini",
    input=[
        {"role": "user", "content": question},
        *response.output,
        {
            "type": "function_call_output",
            "call_id": function_call.call_id,
            "output": observation
        }
    ],
    tools=tools
)

print("\nFinal Answer:")
print(final_response.output_text)
```
### OUTPUT:
<img width="1257" height="440" alt="image" src="https://github.com/user-attachments/assets/a7df57c4-f1ea-4526-a880-8221896ad9f4" />

### RESULT:
Thus, to build a Python program that performs basic mathematical operations (addition, subtraction, multiplication, division) using the OpenAI function calling capability, where the model decides the function to call based on the user's natural language query is successfully executed.
