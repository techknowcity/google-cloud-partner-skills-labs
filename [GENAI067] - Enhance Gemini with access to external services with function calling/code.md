## 🌟 Enhance Gemini with access to external services with function calling: Challenge Lab – Full Code Guide 🌟

This guide provides the full sequence of commands to complete the lab: [GENAI067]: Enhance Gemini with access to external services with function calling.

---

### Code 1 - Code To Use in Vertex AI Workbench.

**PASTE** the below code into the file you renamed as per lab instruction. 

**Note**: You may run whole of the code in One cell. Although, You may have to run the cell a 2nd time for the 2nd Task to get completed. Follow instruction as per youtube video.


```bash
! pip3 install --upgrade --quiet --user google-cloud-aiplatform==1.88.0

# Restart kernel after installs so that your environment can access the new packages
import IPython

app = IPython.Application.instance()
app.kernel.do_shutdown(True)

import vertexai

PROJECT_ID = ! gcloud config get-value project
PROJECT_ID = PROJECT_ID[0]
LOCATION = "us-central1"

print(PROJECT_ID)

vertexai.init(project=PROJECT_ID, location=LOCATION)


import requests
from vertexai.generative_models import (
    Content,
    FunctionDeclaration,
    GenerationConfig,
    GenerativeModel,
    Part,
    Tool,
)

# -------------------------------
# 1) Python functions
# -------------------------------

def add(a, b):
    print("Calling add function")
    return a + b


def multiply(a, b):
    print("Calling multiply function")
    return a * b


# -------------------------------
# 2) Function Declarations
# -------------------------------

add_function = FunctionDeclaration(
    name="add",
    description="Adds two numbers together",
    parameters={
        "type": "object",
        "properties": {
            "a": {"type": "number"},
            "b": {"type": "number"},
        },
        "required": ["a", "b"],
    },
)

multiply_function = FunctionDeclaration(
    name="multiply",
    description="Multiplies two numbers together",
    parameters={
        "type": "object",
        "properties": {
            "a": {"type": "number"},
            "b": {"type": "number"},
        },
        "required": ["a", "b"],
    },
)


# -------------------------------
# 3) Math Tool
# -------------------------------

math_tool = Tool(
    function_declarations=[add_function, multiply_function]
)


# -------------------------------
# 4) Model Configuration
# -------------------------------

generation_config = GenerationConfig(
    temperature=0
)

system_instructions = """
You are an assistant that follows these rules:

- Fulfill the user's instructions, including telling jokes.
- Answer the user's question using the appropriate function tool if available.
- You may call both the 'multiply' and 'add' functions one after the other if needed.
- Use the 'multiply' function only for multiplication-related tasks.
- Use the 'add' function only for addition-related tasks.
- A function tool may only be invoked if its capabilities are an exact match for the described task.
- Avoid speculative or improper use of tools.
- If no suitable tool exists, respond with a generic answer and do NOT perform the mathematical calculation yourself.
"""


# -------------------------------
# 5) Initialize Gemini 2.5 Flash
# -------------------------------

model = GenerativeModel(
    model_name="gemini-2.5-flash",
    tools=[math_tool],
    generation_config=generation_config,
    system_instruction=system_instructions,
)


# -------------------------------
# 6) Start a new chat
# -------------------------------

chat = model.start_chat()


def handle_response(response):

    # If there is a function call then invoke it
    # Otherwise print the response.
    if response.candidates[0].function_calls:
        function_call = response.candidates[0].function_calls[0]
    else:
        print(response.text)
        return

    # ----------------------------
    # Handle ADD function
    # ----------------------------
    if function_call.name == "add":
        # Extract arguments
        args = function_call.args
        a = args["a"]
        b = args["b"]

        # Call Python function
        result = add(a, b)

        # Send result back to the model
        response = chat.send_message(
            Content(
                role="tool",
                parts=[
                    Part.from_function_response(
                        name="add",
                        response={"result": result},
                    )
                ],
            )
        )

        # Recursive call to continue the loop
        handle_response(response)

    # ----------------------------
    # Handle MULTIPLY function
    # ----------------------------
    elif function_call.name == "multiply":
        # Extract arguments
        args = function_call.args
        a = args["a"]
        b = args["b"]

        # Call Python function
        result = multiply(a, b)

        # Send result back to the model
        response = chat.send_message(
            Content(
                role="tool",
                parts=[
                    Part.from_function_response(
                        name="multiply",
                        response={"result": result},
                    )
                ],
            )
        )

        # Recursive call to continue the loop
        handle_response(response)

    else:
        # You shouldn't end up here
        print(function_call)


# Now test with the prompts

# Test 1: Non-math question
print("\nTest 1: Tell me a joke?")
response = chat.send_message("Tell me a joke?")
handle_response(response)

# Test 2: Multiplication
print("\nTest 2: I have 7 pizzas each with 16 slices. How many slices do I have?")
response = chat.send_message("I have 7 pizzas each with 16 slices. How many slices do I have?")
handle_response(response)

# Test 3: Addition
print("\nTest 3: Doug brought 3 pizzas. Andrew brought 4 pizzas. How many pizzas did they bring together?")
response = chat.send_message("Doug brought 3 pizzas. Andrew brought 4 pizzas. How many pizzas did they bring together?")
handle_response(response)

# Test 4: Combined operations
print("\nTest 4: Doug brought 3 pizzas. Andrew brought 4 pizzas. There are 16 slices per pizza. How many slices are there?")
response = chat.send_message("Doug brought 3 pizzas. Andrew brought 4 pizzas. There are 16 slices per pizza. How many slices are there?")
handle_response(response)

# Test 5: Subtraction (not using our functions)
print("\nTest 5: Doug brought 4 pizzas, but Andrew dropped 2 on the ground. How many pizzas are left?")
response = chat.send_message("Doug brought 4 pizzas, but Andrew dropped 2 on the ground. How many pizzas are left?")
handle_response(response)

```

---

