﻿# Semantic Kernel - Assistants

[![Build & Test](https://github.com/kbeaugrand/SemanticKernel.Assistants/actions/workflows/build_tests.yml/badge.svg)](https://github.com/kbeaugrand/SemanticKernel.Assistants/actions/workflows/build_test.yml)
[![Create Release](https://github.com/kbeaugrand/SemanticKernel.Assistants/actions/workflows/publish.yml/badge.svg)](https://github.com/kbeaugrand/SemanticKernel.Assistants/actions/workflows/publish.yml)
[![Version](https://img.shields.io/github/v/release/kbeaugrand/SemanticKernel.Assistants)](https://img.shields.io/github/v/release/kbeaugrand/SemanticKernel.Assistants)
[![License](https://img.shields.io/github/license/kbeaugrand/SemanticKernel.Assistants)](https://img.shields.io/github/v/release/kbeaugrand/SemanticKernel.Assistants)

This is assistant proposal for the [Semantic Kernel](https://aka.ms/semantic-kernel).

This enables the usage of assistants for the Semantic Kernel **without relying on OpenAI Assistant APIs**.
It runs locally planners and plugins for the assistants.

It provides different scenarios for the usage of assistants such as:
- **Assistant with Semantic Kernel plugins**
- **Multi-Assistant conversation**

As the assistants are using the Semantic Kernel, you can use your own model for the assistants and host them locally (see: [Bring you own model](#bring-you-own-model-) for more details.).

## About Semantic Kernel

**Semantic Kernel (SK)** is a lightweight SDK enabling integration of AI Large
Language Models (LLMs) with conventional programming languages. The SK
extensible programming model combines natural language **semantic functions**,
traditional code **native functions**, and **embeddings-based memory** unlocking
new potential and adding value to applications with AI.

Semantic Kernel incorporates cutting-edge design patterns from the latest in AI
research. This enables developers to augment their applications with advanced
capabilities, such as prompt engineering, prompt chaining, retrieval-augmented
generation, contextual and long-term vectorized memory, embeddings,
summarization, zero or few-shot learning, semantic indexing, recursive
reasoning, intelligent planning, and access to external knowledge stores and
proprietary data.

### Getting Started with Semantic Kernel⚡

- Learn more at the [documentation site](https://aka.ms/SK-Docs).
- Join the [Discord community](https://aka.ms/SKDiscord).
- Follow the team on [Semantic Kernel blog](https://aka.ms/sk/blog).
- Check out the [GitHub repository](https://github.com/microsoft/semantic-kernel) for the latest updates.

## Installation

To install the assistant Framework, you need to add the required nuget package to your project:

```dotnetcli
dotnet add package SemanticKernel.Assistants --version 1.2.0-preview
```

## Usage

1. Create you agent description file in yaml: 
    ```yaml
    name: Mathematician
    description: A mathematician that resolves given maths problems.
    instructions: |
      You are a mathematician.
      Given a math problem, you must answer it with the best calculation formula.
      No need to show your work, just give the answer to the math problem.
      Use calculation results.
    input_parameter: 
      default_value: ""
      description: |
          The word mathematics problem to solve in 2-3 sentences.
          Make sure to include all the input variables needed along with their values and units otherwise the math function will not be able to solve it.
    execution_settings:
      planner: Handlebars
      prompt_settings: 
        temperature: 0.0
        top_p: 1
        max_tokens: 2000
    ```
2. Instanciate your assistant in your code: 
   ```csharp
    string azureOpenAIChatCompletionDeployment = configuration["AzureOpenAIDeploymentName"]!;
    string azureOpenAIEndpoint = configuration["AzureOpenAIEndpoint"]!;
    string azureOpenAIKey = configuration["AzureOpenAIAPIKey"]!;
 
    var mathKernel = Kernel.CreateBuilder()
                                        .AddAzureOpenAIChatCompletion(azureOpenAIChatCompletionDeployment, azureOpenAIEndpoint, azureOpenAIKey)
                                        .Build();

    mathKernel.ImportPluginFromObject(new MathPlugin());

    var mathematician = AssistantBuilder.FromTemplate("./Assistants/Mathematician.yaml")
                                        .WithKernel(mathKernel)
                                        .Build();
   ```
3. Create a new conversation thread with your assistant.
   ```csharp
   var thread = mathematician.CreateThread();
   await thread.InvokeAsync("Your ask to the assistant.");
   ```

## Bring you own model ?

As the assistants are using the Semantic Kernel, you can use your own model for the assistants.
For example, you can use the Ollama model for the assistants.

This could be achieved by using the [Ollama connector for the Semantic Kernel](https://github.com/BLaZeKiLL/Codeblaze.SemanticKernel): 

```csharp
using Codeblaze.SemanticKernel.Connectors.Ollama;

string ollamaEndpoint = configuration["OllamaEndpoint"]!;

var butlerKernel = Kernel.CreateBuilder()
                    .AddOllamaChatCompletion("phi:latest", ollamaEndpoint)
                    .Build();

assistant = AssistantBuilder.FromTemplate("./Assistants/Butler.yaml")
        .WithKernel(butlerKernel)
        .Build();
```

## License

This project is licensed under the [MIT License](LICENSE).
