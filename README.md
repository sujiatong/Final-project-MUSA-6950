# Final project- MUSA 6950
This project mainly following those following website instruction, utilizing a Large Language Model(LLM) for Sentiment Analyzing and image reasoning.

- [Use ChatGPT API for Sentiment Analysis in Python](https://medium.com/@financial_python/use-chatgpt-api-for-sentiment-analysis-in-python-5a152ddb3238)
- [Using OpenAI GPT-4V model for image reasoning](https://medium.com/@financial_python/use-chatgpt-api-for-sentiment-analysis-in-python-5a152ddb3238) 


The system is designed to demonstrate how LLMs can handle fundamental natural language processing tasks and assist in simple visual interpretation through text.  All functions are implemented through Python in a Jupyter Notebook environment, utilizing the OpenAI API for model interactions.

The project aims to create a clean, lightweight prototype that highlights the potential of LLMs in both text-based and image-related reasoning tasks.

## **Step1:** Obtain API key

To obtain the API key, navigate to https://platform.openai.com/account/api-keys.

### Limitation
- The free usage limit of the OpenAI API was not sufficient for this project, so I spent around $20 to access additional API credits and complete the assignment.
- Access to the OpenAI API may be blocked on certain restricted networks (e.g., school Wi-Fi), requiring workarounds like VPNs or mobile hotspots.


## Sentiment Analysis
This section analyze the sentiment of given text inputs via using GPT-3.5-turbo.

### Send a message to the GPT-3.5-turbo model.
#### Say **Hi** 
```python
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "Hi"}
    ]
)

print(response.choices[0].message.content)

```

`
Hello! How can I assist you today?
`

Say **How are you**

``` Python
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "I love U"}
    ]
)

print(response.choices[0].message.content)
```
`
Thank you! I appreciate the kind words.
`

Feel free to try input other text to Chat the model 😊 ~


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
#### Example usage
```Python
input_text = "The weather today is quite pleasant. It's sunny with a gentle breeze, perfect for a walk in the park."
result = generate_summary_and_sentiment(input_text, api_key)
print(result)
```
`{'summary': 'The weather today is pleasant with sun and a gentle breeze, ideal for a stroll in the park.', 'sentiment': 'The sentiment of the text is positive. The author describes the weather as pleasant, sunny, and perfect for a walk in the park. There is a sense of enjoyment and contentment conveyed in the text. Overall, the sentiment is optimistic and happy.'}
`

### Use the Sentiment Score in Your Code¶

When you put **damm final week**:
```Python

text = "damm final week"

prompt = {
    "role": "user",
    "content": f"Sentiment analysis of the following text: '{text}'\n\nSentiment score: "
}

# Use the updated ChatCompletion API
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[prompt],
    temperature=0,
    max_tokens=2
)

sentiment = response.choices[0].message.content
print(sentiment)
if sentiment == "Positive":
    print("😊")
elif sentiment == "Negative":
    print("😔")
else:
    print("😐")

```

`Negative 😐`


When you input the text **happy graduate**
```Python

text = "happy graduate"

sentiment = response.choices[0].message.content
print(sentiment)
if sentiment == "Positive":
    print("😊")
elif sentiment == "Negative":
    print("😔")
else:
    print("😐")

```
`Positive
😊`

## Image-related Reasoning

In this section, a Large Language Model (LLM) is applied to perform basic image-related reasoning.  
The model generates descriptive texts for the images that summarize the main visual elements, highlighting the potential of LLMs in assisting simple visual understanding tasks through language.

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg" width="120" height="120"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/a/a3/June_odd-eyed-cat.jpg" width="120" height="120"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg" width="120" height="120"/>
</p>

### Load Images from URLs
```Python
from llama_index.llms.openai import OpenAI

image_urls = [
    "https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg",
    "https://upload.wikimedia.org/wikipedia/commons/a/a3/June_odd-eyed-cat.jpg",
    "https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg",
]

openai_llm = OpenAI(model="gpt-4o", max_new_tokens=300)

from PIL import Image
import requests
from io import BytesIO
import matplotlib.pyplot as plt

img_response = requests.get(image_urls[0])
print(image_urls[0])
img = Image.open(BytesIO(img_response.content))
plt.imshow(img)
```

<img src="https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg" width="200" height="200"/>


### Ask the model to describe what it sees

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
`assistant: 1. A close-up of an orange tabby cat with striking amber eyes, looking directly at the camera. The background is blurred, with a hint of a red object.`
`2. A white cat with heterochromia, featuring one yellow eye and one blue eye. The cat is lying on a textured blanket with blue and beige colors, and the background is softly blurred.`

#### We can also stream the model response asynchronously
```Python
async_resp = await openai_llm.astream_chat(messages=[msg])
async for delta in async_resp:
    print(delta.delta, end="")
```

`1. A close-up of an orange tabby cat with striking amber eyes, looking directly at the camera. The background is blurred, highlighting the cat's face and whiskers.`

`2. A white cat with heterochromia, featuring one yellow eye and one blue eye. The cat is lying on a soft, multicolored blanket, with a focused and curious expression.`
