+++
author = "Binwei Wu"
title = "Langchain - Getting Started"
date = "2026-03-13"
description = "Langchain - Getting Started"
featured = true
tags = [
    "ai"
]
categories = [
    "Engineering",
]
series = "2026"
aliases = ["migrate-from-jekyl"]

+++

This blog outlines the step-by-step process for getting started with a Langchain demo.

# LLM Creation

Go to the Azure portal and create an Azure OpenAI service. Within that service, navigate to the Foundry portal and deploy a cost-effective gpt-4o-minimodel (use version: 2024-07-18).

# Simple Chat

Create chat.py

```
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from langchain_openai import AzureChatOpenAI
from langchain_core.messages import SystemMessage, HumanMessage


def create_client(model_name: str = "gpt-4o-mini") -> AzureChatOpenAI:
    """Create Azure OpenAI client using Azure AD authentication

    Args:
        model_name: Model to use. Options: "gpt-4o-mini"
    """
    token_provider = get_bearer_token_provider(
        DefaultAzureCredential(exclude_powershell_credential=True),
        "https://cognitiveservices.azure.com/.default",
    )

    return AzureChatOpenAI(
        model=model_name,
        azure_endpoint="https://testazureopenairesource11.openai.azure.com/",
        azure_ad_token_provider=token_provider,
        api_version="2024-12-01-preview",
    )


def test_connection() -> bool:
    """Test Azure OpenAI connection"""
    try:
        client = create_client()
        messages = [
            SystemMessage(content="You are a helpful assistant."),
            HumanMessage(content="Reply with exactly: CONNECTION_OK"),
        ]
        response = client.invoke(messages)
        return "CONNECTION_OK" in response.content
    except Exception as e:
        print(f"❌ Azure OpenAI connection failed: {e}")
        return False


SYSTEM_MESSAGE = SystemMessage(content="You are a helpful assistant.")


def send_message(prompt: str, client: AzureChatOpenAI | None = None) -> str:
    client = client or create_client()
    messages = [SYSTEM_MESSAGE, HumanMessage(content=prompt)]
    response = client.invoke(messages)
    return response.content


def interactive_chat():
    client = create_client()
    print("Type `exit` to quit.")
    while True:
        prompt = input("You: ").strip()
        if not prompt or prompt.lower() in ("exit", "quit"):
            break
        reply = send_message(prompt, client)
        print("Assistant:", reply)


def main():
    ok = test_connection()
    print("✅ CONNECTION_OK" if ok else "❌ connection failed")

    reply = send_message("Give me a 2-line summary of the Python logging module.")
    print("Assistant:", reply)

    interactive_chat()


if __name__ == "__main__":
    main()

```

# Agent

Create a new Python file named create_agent.py. This file will demonstrate how a tool is triggered within an agent.

```
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from langchain.agents import create_agent
from langchain_openai import AzureChatOpenAI


def get_weather(city: str) -> str:
    """Get weather for a given city."""
    return f"It's always sunny in {city}!"


def create_model(model_name: str = "gpt-4o-mini") -> AzureChatOpenAI:
    """Create a LangChain Azure OpenAI chat model using Azure AD auth."""
    token_provider = get_bearer_token_provider(
        DefaultAzureCredential(exclude_powershell_credential=True),
        "https://cognitiveservices.azure.com/.default",
    )

    return AzureChatOpenAI(
        model=model_name,
        azure_endpoint="https://testazureopenairesource11.openai.azure.com/",
        azure_ad_token_provider=token_provider,
        api_version="2024-12-01-preview",
    )


def run_agent(user_query: str) -> str:
    model = create_model()
    agent = create_agent(
        model=model,
        tools=[get_weather],
        system_prompt="You are a helpful assistant.",
    )

    result = agent.invoke(
        {"messages": [{"role": "user", "content": user_query}]}
    )
    return result["messages"][-1].content


if __name__ == "__main__":
    prompt = "What is the weather in San Francisco?"
    print(f"User: {prompt}")
    print(f"Assistant: {run_agent(prompt)}")

```

*Written by Binwei@Shanghai*