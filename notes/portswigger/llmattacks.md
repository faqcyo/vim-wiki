[Back to PortSwigger index](index)

# Web LLM attacks

# What are LLMs?

Large Language Models are AI models that reacts to user input based on previously trained prompts.

LLMs are usually used on:
- Customer Service, such as a virtual assistant
- Translation
- SEO improvement
- Analysis of User Generated content, for example to track the tone of on-page commands.

# LLMs attack and prompt injection

Many web LLM attacks rely on prompt injection. 

Prompt injection happens whenever the attacker refines the input to the LLM to manipulate its output.

This LLM misbehavior caused by prompt injection may produce some incorrect calls to sensitive APIs or returning unexpected content.

# Detecting LLM vulnerabilities

1. Identify what type of inputs the LLM can receive, both direct (such as input prompts) or indirect (such as training data) input.
2. Figure out to which APIs or data the LLM has access to.
3. Investigate further this new attack surface for more vulnerabilities.

# Mapping LLM API attack surface

Whenever an LLM has access to sensitive information (and can be persuaded to provide this info) we say that we are on a situation called "excessive agency".

To get this information, the attacker may just ask to the LLM which APIs it can access.

If the LLM isn't cooperative, we can change the context of the conversation and ask again, for example by impersonating an internal developer that wants to get this info.
