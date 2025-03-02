# Tools in LLMs  

Tools are essentially external capabilities or functions that allow the AI to extend its abilities beyond just generating text. Think of Tools like special attachments or add-ons that help the LLM interact with the world more dynamically and solve more complex problems.  

## What Are Tools?  

Tools in LLMs are predefined functions or capabilities that allow the AI to:  

1. Perform specific actions  
2. Access external information  
3. Interact with other systems or software  
4. Extend its problem-solving capabilities  

## Examples of Tools  

Some practical examples of Tools include:  

- **Calculator Tool** – Allows the LLM to perform mathematical calculations  
- **Web Search Tool** – Enables the LLM to retrieve current information  
- **Code Execution Tool** – Lets the LLM run and test programming code  
- **Database Query Tool** – Enables searching through structured information  
- **Scheduling Tool** – Interacts with calendar systems  

## Conceptual Example  

### Without a Tool:  
If you ask an LLM, *"What's 15% of 240?"*, it might try to calculate the answer within its text response.  

### With a Calculator Tool:  
The LLM can use a precise calculation function and return the exact result: **36**.  

## Code example
## A tool is a function in this case get ticket price given a city
```bash
ticket_prices = {"london": "$799", "paris": "$899", "tokyo": "$1400", "berlin": "$222",'caracas':"$1200"}

def get_ticket_price(destination_city):
    print(f"Tool get_ticket_price called for {destination_city}")
    city = destination_city.lower()
    return ticket_prices.get(city, "Unknown")
```
## There's a particular dictionary structure that's required to describe our function:
```bash
price_function = {
    "name": "get_ticket_price",
    "description": "Get the price of a flight ticket to the destination city. Call this whenever you need to know the ticket price, for example when a customer asks 'How much is a ticket to this city'",
    "parameters": {
        "type": "object",
        "properties": {
            "destination_city": {
                "type": "string",
                "description": "The city that the customer wants to travel to",
            },
        },
        "required": ["destination_city"],
        "additionalProperties": False
    }
}
```
# And this is included in a list of tools:
```bash
tools = [{"type": "function", "function": price_function},{"type": "function", "function": discount_function}]
```
#Getting OpenAI to use our Tool
```bash
def chat(message, history):
    messages = [{"role": "system", "content": system_message}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(model=MODEL, messages=messages, tools=tools)

    if response.choices[0].finish_reason=="tool_calls":
        message = response.choices[0].message #here we grab the LLM response upto when it needs to call the Tool
        tools_responses, city = handle_tool_call(message) #we pass the LLM response and get back the result of the tool + parameter
        messages.append(message) 
        messages.extend(tools_responses) #here the change in the code: to add tool responses individually 
        # Create final response after tool calls
        response = openai.chat.completions.create(model=MODEL, messages=messages)
    
    return response.choices[0].message.content
```
# We have to write that function handle_tool_call:
```bash
def handle_tool_call(message):
    responses = []

    #iterate through all tool calls in the message
    for tool_call in message.tool_calls:
        tool_name = tool_call.function.name #indentify which tool was called
        arguments = json.loads(tool_call.function.arguments) # unpack the function arguments and load them into arguments
        city = arguments.get('destination_city') #from arguments collect the city and loaded into city

        if tool_name=="get_ticket_price": #handle the price tool
            price = get_ticket_price(city)
            response_content = {"destination_city":city,"price":price}
        
        elif tool_name=="get_ticket_discount":
            discount = get_ticket_discount(city)
            response_content ={"destination_city":city,"discount":discount}
        
        else:
            response_content ={'error':f"Unkown tool: {tool_name}"}

        # Append the tool response
        responses.append({
            "role":"tool",
            "content":json.dumps(response_content),
            "tool_call_id":tool_call.id,
        })
    return responses, city
```
#Putting all into Gradio
```bash
gr.ChatInterface(fn=chat, type="messages").launch()
```
