# Harnessing the power of Ollama for LLMS

# Module 1
## Introduction & What will you Learn?
- Ollama
Build Local LLM applications using Ollama:
    - Customize models
    - Use different Ollama models(testing....)
    - Build RAG systems powered by Ollama models
    - Tool/Function calling
    - Build full fledged applications with Ollama models

## Development Environment Setup
One of the best places to go and install python is 
https://kinsta.com/knowledgebase/install-python/


# Module 2
## Ollama Deep Dive
- Ollama is open source tool essentially to simplify the process of running large language models locally.
- Free opensource model usage
- uses cli interface manages everything in backend
- Ollama provides a straightforward way to run, plan and interact with various models without dealing with cloud based services.

## Problem that ollama solves
- document are chopped into chunk we pass to embedding model then query passed and similarity search and data retrieved.

## What problem does it solve?

1. Privacy concerns - running LLMS locally provides a lot of security.
2. Ease of use - Setting up LLMS can be challenging with Ollama, it's an easy process.
3. Cost efficiency- no more cloud based services (which can be costly)
4. Latency reduction - local execution reduces the latency issues.
5. Customization - greater flexibility in customizing our models

## Ollama key Features and Use cases

Key features
- Model Management
- Unified Interface
- Extensibility
- Performance optimization

Use cases
- Development & testing
- Education & Research
- Secure applications

## System Requirements & Ollama Setup- Overview
System Requirements
- OS: Mac, Linux & Windows
- Storage (atleast -10 GB free)
- Processor: modern CPU

Ollama Setup
- ollama.com download it
- ollama run in cli and install the required the models
- /clear - to clear section
- /help
available commands are 
```
- /set                 - set session variables
- /show                - show model information
- /load <model>        - Load a session or model
- /save <model>        - Save your current session
- /clear               - Clear session context
- /bye                 - Exit
-/?, /help             - Help for a command
-/? shortcuts          - Help keyboard shortcuts
Use """ to begin a multi-line message
```

## Ollama Models Page Overview
- Filter by featured, Most popular, Newest.
- Number of pulls, parameters.
- Readme important to read
- Sizes what are the tasks it does 
- ollama list- all models downloaded locally

## Ollama model parameters deep dive
Architecture 
   - lets take example of llama 3.2:3b parameter model
   - architecture is Llama (Large Language Model Meta AI)
   - These models are design to be effective and efficient
   - stronger performance in small sizes compared to Large Language models

Parameter: 3.2B
   - The internal wweights and biases that the model learns during training 
   - *They determine how the model processes input data and generate output.
   - Number of parameters reflects the complexity and capacity of the model as explained.
   - More parameters means more computational resources.
   - Numbers in neural network which adjusts tolearn how to turn inputs into correct outputs.
   - So larger the number more the better the case, but lot of computational resources needed

Context length
   - refers to max number of tokens or pieces of text model can process in a single input.
   - Ideal for processing entire books, lengthy articles, or extensive conversations

Embedding Length
   - The size of the vector representation for each token in the input text.
   - 3072 dimentions in the embedding space- higher the dimensional embeddings can capture more nuanced meaning and relationships between words
   - Larger would increase the computational load

Quantization
   - Quantization - a technique used to reduce the size of neural network model by reducing the precision of its weight.
   - Indicates that the models weights are quantized to 4 bits
   - Translation: we now have a smaller model, and faster processing and lower memory usage.

## Understanding Paramters and Disk Size and computational Resources Needed
- sizes and computational resources are important.
- Though size is there not necessary computational resources.

# Module 3
## Ollama Commands- Pulling and testing a Model
1. ollama list -to list all models
2. ollama rm - to remove model
3. ollama help - some of the commands
4. ollama pull model_name - to pull the model 
5. ollama run model_name -to run a model
6. /bye - to move out

## Pull in Llave Multimodal model and caption an image
Model that deals with images and text
- ollama run llava:7b
- vscode
we gonna open image and ask 
1. create a file ollama 
2. save a file flower.png
3. go to that particular folder where flower is present that is cd ollama
4. what is in this image ./flower_1.png then u get the response
5. to see history capability
6. now write a short poem about that
7. so now we can see it can save the context

## Summarization and sentimental Analysis
1. ollama run llama3.2 
2. tell me what is the sentiment of the following sentence "I am not willing to pay you back"
3. can you please be less verbose
4. Modelfile create
FROM llama 3.2
## set the temperature to 1 [higher is more creative]
PARAMETER temperature 0.3
SYSTEM """
You are James, a very smart assistant who answers questions succinlt and informatively
"""
- ollama create james -f ./Modelfile
on enter
- ollama list
- ollama run james
- what is your name?

## Ollama REST API- Generate and Chat Endpoints
```
curl http://localhost:11434/api/generate -d '{
    "model": "llama3.2",
    "prompt": "Why is the sky blue?",
    "stream": false
}
```

```
curl http://localhost:11434/api/generate -d '{
    "model": "llama3.2",
    "messages": [
        {"role": "user", "content": "tell me a fun fact"}
    ],
    "stream": false
}
```

## Ollama REST API- REQUEST JSON MODE
```
curl http://localhost:11434/api/generate -d '{
    "model": "llama3.2",
    "prompt": "What is the color of the sky at different times of the day? Respond using JSON",
    "format": "json",
    "stream": false
}
```

github.com/ollama/ollama/blob/main/docs/api.md

## Ollama models for different tasks
- Text generation (llama3.x, mistral)
- Code generation (codellama)
- Multimodal applications (text and images)
      - Llava

# Module 4 
## Different Ways to Interact with ollama models- Overview
There are different ways to interact with Ollama and its models:
- CLI- Command line- interface
- UI based interface
- API
- Ollama Python Library

## Ollama Model Running Under Msty App -Front end tool
1. Head on to msty.app and start installation
2. open it 
3. Got ollama 
4. Click and see differnt model u can see
5. select model or chat
6. Attach image and ask tell me about the image
7. Also add document BOI.pdf
8. ask can u tell me issue
9. attach knowledge stack Nomic embed then hit compose
10. tick knowledge base and ask for summary
11. also ask specific questions


