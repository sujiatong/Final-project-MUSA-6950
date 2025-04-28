# Final project- MUSA 6950
This project mainly following those following website instruction, utilizing a Large Language Model(LLM) for Sentiment Analyzing and image reasoning.

- [Use ChatGPT API for Sentiment Analysis in Python](https://medium.com/@financial_python/use-chatgpt-api-for-sentiment-analysis-in-python-5a152ddb3238)
- [Using OpenAI GPT-4V model for image reasoning](https://medium.com/@financial_python/use-chatgpt-api-for-sentiment-analysis-in-python-5a152ddb3238) 


The system is designed to demonstrate how LLMs can handle fundamental natural language processing tasks and assist in simple visual interpretation through text.  All functions are implemented through Python in a Jupyter Notebook environment, utilizing the OpenAI API for model interactions.

The project aims to create a clean, lightweight prototype that highlights the potential of LLMs in both text-based and image-related reasoning tasks.

## **Step1:** Obtain API key

To obtain the API key, navigate to https://platform.openai.com/account/api-keys.

- The free usage limit of the OpenAI API was not sufficient for this project, so I spent around $20 to access additional API credits and complete the assignment.


## Sentiment Analysis

### Send a message to the GPT-3.5-turbo model.
```python
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "Hi"}
    ]
)

print(response.choices[0].message.content)

```
### Receive and print the model’s reply.

```text
Hello! How can I assist you today?
```
### Other example
``` Python
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "How are you"}
    ]
)

print(response.choices[0].message.content)
```
```
I don't have feelings, but I'm here to assist you. How can I help you today?
```

### Function to generate summary and sentiment analysis

```Python
def generate_summary_and_sentiment(input_text, api_key, max_tokens=50):
    # Create the summarization prompt
    summarization_prompt = {
        "role": "user",
        "content": f"Summarize the following text: '{input_text}'"
    }
    
    # Create the sentiment analysis prompt
    sentiment_prompt = {
        "role": "user",
        "content": f"Analyze the sentiment of the following text: '{input_text}'"
    }
    
    # Request the summarization using ChatGPT
    summarization_response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[summarization_prompt],
        max_tokens=max_tokens
    )
    
    # Request sentiment analysis using ChatGPT
    sentiment_response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[sentiment_prompt],
        max_tokens=max_tokens
    )

    # Extract and return the summary and sentiment analysis
    summary = summarization_response.choices[0].message.content
    sentiment = sentiment_response.choices[0].message.content
    
    return {'summary': summary, 'sentiment': sentiment}
```
## image-related reasoning

![Sample Cat Image](https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg)

``` Python
from llama_index.core.llms import (
    ChatMessage,
    ImageBlock,
    TextBlock,
    MessageRole,
)

msg = ChatMessage(
    role=MessageRole.USER,
    blocks=[
        TextBlock(text="Describe the images as an alternative text"),
        ImageBlock(url=image_urls[0]),
        ImageBlock(url=image_urls[1]),
    ],
)

response = openai_llm.chat(messages=[msg])
```
```Python
print(response)
```
