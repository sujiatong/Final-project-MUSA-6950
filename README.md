

# Final project- MUSA 6950

## Table of Contents

<details>

   <summary>Contents</summary>

1. [What is LLMs?](#what-is-llms)
1. [Project summary](#project-summary)
1. [Objectives](#objectives)
1. [Obtain API key](#obtain-api-key)
   1. [Limitation](#limitation)
1. [Task 1: Sentiment Analysis](#task-1-sentiment-analysis)
   1. [Function to generate summary and sentiment analysis](#function-to-generate-summary-and-sentiment-analysis)
      1. [Example usage](#example-usage)
   1. [Use the Sentiment Score in Your Code](#use-the-sentiment-score-in-your-code)
1. [Image-Related Reasoning](#image-related-reasoning)
   1. [Load Images from URLs](#load-images-from-urls)
   1. [Ask the model to describe what it sees](#ask-the-model-to-describe-what-it-sees)

</details>




- [Final project- MUSA 6950](#final-project--musa-6950)
  - [Table of Contents](#table-of-contents)
  - [What is LLMs?](#what-is-llms)
  - [Project summary](#project-summary)
  - [Objectives](#objectives)
  - [Obtain API key](#obtain-api-key)
    - [Limitation](#limitation)
  - [Task 1: Sentiment Analysis](#task-1-sentiment-analysis)
    - [Function to generate summary and sentiment analysis](#function-to-generate-summary-and-sentiment-analysis)
      - [Example usage](#example-usage)
    - [Use the Sentiment Score in Your Code](#use-the-sentiment-score-in-your-code)
  - [Image-Related Reasoning](#image-related-reasoning)
    - [Load Images from URLs](#load-images-from-urls)
    - [Ask the model to describe what it sees](#ask-the-model-to-describe-what-it-sees)
- [Conclusion](#conclusion)

</details>


## What is LLMs?

Large Language Models (LLMs) are advanced artificial intelligence systems designed to understand, generate, and manipulate human language on a large scale.

 <img src="https://pixelplex.io/wp-content/uploads/2024/01/10-most-popular-applications-of-large-language-models.jpg" width="450" height="300"/>

--- 
## Project summary
This project mainly following those following website instruction, utilizing a Large Language Model(LLM) for Sentiment Analyzing and image reasoning.

Using LLMs, we can analyze how people feel about different street views in New York City, linking emotions to urban space design.

- [Use ChatGPT API for Sentiment Analysis in Python](https://medium.com/@financial_python/use-chatgpt-api-for-sentiment-analysis-in-python-5a152ddb3238)
- [Using OpenAI GPT-4V model for image reasoning](https://docs.llamaindex.ai/en/stable/examples/multi_modal/openai_multi_modal/) 

The project aims to create a clean, lightweight prototype that highlights the potential of LLMs in both text-based and image-related reasoning tasks.

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a1/Pearl_Street_and_Wall_Street%2C_Manhattan%2C_New_York.jpg/640px-Pearl_Street_and_Wall_Street%2C_Manhattan%2C_New_York.jpg" width="220" height="220"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/At_New_York_City_2023_033.jpg/640px-At_New_York_City_2023_033.jpg" width="220" height="220"/>
</p>


## Objectives
- Build a lightweight system using an LLM (ChatGPT)
- Implement two key functionalities:
  - Sentiment analysis of text inputs
  - Image-based reasoning through textual description


##  Obtain API key

To obtain the API key, navigate to https://platform.openai.com/account/api-keys.

### Limitation
- The free usage limit of the OpenAI API was not sufficient for this project, so I spent around $20 to access additional API credits and complete the assignment.
- Access to the OpenAI API may be blocked on certain restricted networks (e.g., school Wi-Fi), requiring workarounds like VPNs or mobile hotspots.

---

##  Task 1: Sentiment Analysis

This section analyze the sentiment of given text inputs via using GPT-3.5-turbo.
- Instead of processing Street View image directly, publicly available image URLs are used.
- Text prompts describing the Street view image URLs are fed into the LLM.
- The model generates descriptive text based on the imagined content of the images, simulating a simple form of visual reasoning.

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

Writing about my feelings toward the first image, I think it makes me feel stressed. Then, we can see the sentiment description of the image.

```python
input_text = "This street feels narrow, busy, a bit stressful with tall buildings surrounding the pedestrians"
result = generate_summary_and_sentiment(input_text, api_key)
print(result)

```

`
{'summary': 'The street is narrow and busy with tall buildings creating a stressful environment for pedestrians.', 'sentiment': 'Overall, the sentiment of the text is negative. Words like "narrow," "busy," "stressful," and "surrounding" evoke a sense of discomfort and unease. The mention of tall buildings further adds to the feeling of being'}
`



### Use the Sentiment Score in Your Code

- The system takes a text input about description of street view images from the user.
- The text is sent to the LLM with a carefully designed prompt asking the model to analyze its sentiment.
- The model returns a result classifying the input as positive, negative, or neutral, along with a brief summary.
  
Using the second image to express my positive feelings, the output shows `Positive 😊`.

```
text =  "This avenue feels open, bright, and lively with wide sidewalks and beautiful views of the sky."

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
`Positive
😊`


## Image-Related Reasoning

In this section, utilizing LLMs performs image related reasoning by the NY street view image.

The model generates descriptive texts for the street view images that summarize the main visual elements, highlighting the potential of LLMs in assisting simple visual understanding tasks through language.

- Instead of processing raw image data directly, publicly available image URLs are used.
- Text prompts describing the image URLs are fed into the LLM.
- The model generates descriptive text based on the imagined content of the images, simulating a simple form of visual reasoning.


--- 

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a1/Pearl_Street_and_Wall_Street%2C_Manhattan%2C_New_York.jpg/640px-Pearl_Street_and_Wall_Street%2C_Manhattan%2C_New_York.jpg" width="220" height="220"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/At_New_York_City_2023_033.jpg/640px-At_New_York_City_2023_033.jpg" width="220" height="220"/>
</p>

### Load Images from URLs
```Python
from llama_index.llms.openai import OpenAI

image_urls = [
    "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a1/Pearl_Street_and_Wall_Street%2C_Manhattan%2C_New_York.jpg/640px-Pearl_Street_and_Wall_Street%2C_Manhattan%2C_New_York.jpg",
    "https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/At_New_York_City_2023_033.jpg/640px-At_New_York_City_2023_033.jpg",
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

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/At_New_York_City_2023_033.jpg/640px-At_New_York_City_2023_033.jpg" width="200" height="200"/>



### Ask the model to describe what it sees

The model generates descriptive text based on the imagined content.

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
async_resp = await openai_llm.astream_chat(messages=[msg])
async for delta in async_resp:
    print(delta.delta, end="")
```

`1. A narrow city street lined with tall buildings on both sides. The road has "STOP" painted on it, and there are a few pedestrians walking on the sidewalk. Several cars are parked along the street, and American flags are visible on the right side.`

`2. A wide city street with tall brick buildings on both sides. The street is busy with cars and pedestrians, and the sky is overcast with clouds. The buildings have multiple stories, and the street is lined with trees and traffic lights.`

# Conclusion
By utilizing Large Language Models (LLMs) for sentiment analysis and image reasoning, this project demonstrates the potential of AI to interpret human emotional responses to urban environments. Through analyzing street views in New York City, we can better understand how different urban designs influence public sentiment. These insights offer valuable guidance for creating more emotionally engaging and human-centered urban spaces.


