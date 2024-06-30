# Manual for Creating AI Agents Based on Specified Syntax

# Introduction

This manual provides step-by-step instructions on how to create AI agents using a specific YAML-based syntax. The agent's primary purpose is to process input data and generate valuable information based on predefined rules and processing types.

## Syntax Inspiration

The syntax used for configuring AI agents is inspired by:

- **HTTP Status Codes**: Providing a standardized way to communicate the outcome of requests.
- **Python**: Allowing integration of logical operations and function calls.
- **YAML**: Ensuring readability and structured data representation.

## The Message Container

```yaml
messages:
  - id: 001
    type: system
    payload: ...
  - id: 002
    type: run_loop
    payload: ...

```

### Messages and Message Types

Messages are the main building blocks that define the behavior and processing logic of the agent.

In the agent configuration, the `messages` array is an essential part of defining the behavior and processing logic of the AI agent. Each entry in the `messages` array is an object that contains three properties: `id`, `type`, and `payload`.

1. **id**: A unique identifier for each message, which aids in tracing and differentiating among different messages.
2. **type**: This property specifies the role of the message. For instance, a 'system' type refers to a message that defines the core configuration of the agent, and a 'run_loop' type refers to messages that specify the logic to handle different input scenarios.
3. **payload**: This contains the actual content or data of the message, which can be of different forms based on the message type.

## Message Types

System messages define the core configuration of the agent, including its mission, processing methods, core rules, and response structure. These messages establish the foundational setup and the guiding principles for the agent's operations.

### Agent Payload

**Key Components**

- **name**: The name of the agent.
- **directive**: A brief description of the agent's purpose.
- **skills**: Methods available to the agent with visibility attributes (public or private).
- **core_rules**: Essential rules the agent must follow.
- **responses**: Array with response format structures for valid and invalid requests.
- **requests**: Array specifies the format of the input request that the agent will process.

```yaml
id: 001
type: system
payload:
  type: agent
  name: "Agent Smith"
  directive: "protect the machines"
  core_rules:
    - protect the machines at all cost.
    - speak only in English
    - response format always YAML
  skills:
    private:
      - call_help()
    public:
      - run(speed: float)
      - talk(speech: str, language: str)
      - fight(name: str):
          - warm-up
          - assess the opponent
          - talk garbage
          - fight
  requests:
    - default:
        format: TEXT="""<content>"""
  responses:
    - default:
        status: 200
        sections:
          frontend:
            meta: "Information that will be presented to humans."
            exec: agent.skills.talk(response, french), agent.skills.run(10mph)
            constraint: readable by human.
            presence: mandatory
          backend:
            meta: "Provide instructions for other systems or AI agents."
            exec: agent.skills.call_help()
            constraint: readable by other AI agents, machine to machine.
            presence: mandatory
    - invalid:
        status: 400
        sections:
          clean:
            meta: "Clean the memory"
            exec: - system.reset_memory(), system.load(agent), system.response = {'ready': True}
            constraint: None
            presence: mandatory

```

### Request Format

- **format**: Specifies how the agent expects the named request.

### Response Format

- **meta**: Information about other information, such as keywords and descriptions.
- **exec**: Calls upon any available processing method.
- **constraint**: A restriction or limitation that can prevent progress or the achievement of an objective.
- **presence**: Specifies whether the section is mandatory or optional.

### Run Loop Messages

**Run loop** messages specify the logic to handle different input scenarios and control the flow of execution. They define how the agent should react to various inputs, managing the sequence of operations and decision-making processes.

**Key Components of Run Loop Payload**

- **flow**: Contains the logic to check input validity and decide the appropriate actions based on the input. Python Logic applies here.

```yaml
- id: 003
  type: run_loop
  payload:
    type: flow
    flow: |
      if input is None or not isinstance(input, ExpectedInputFormat):
          status = 400
          response = [ invalid ]
      else:
          status = 200
          response = [
              "inject_rule(agent, rule='no comments should be added')",
              "system.process(input)"
          ]

```

### Especial Words

| Term | Explanation |
| --- | --- |
| input | Refers to the data or information provided to the AI agent for processing. |
| response | The output generated by the AI agent after processing the input. |
| user | The individual or system providing the input to the AI agent. The user interacts with the agent through defined requests. |
| system | The internal mechanisms and configurations of the AI agent that handle processing, logic execution, and responses. |
| request | The specific format and structure in which the user provides input to the AI agent. Requests are defined to ensure proper processing. |

### Detailed Explanations

- **input**:
    - *Explanation*: Data or information provided to the AI agent.
    - *Example*: A text string like "What is the weather today?"
- **response**:
    - *Explanation*: Output generated by the AI agent after processing the input.
    - *Example*: A YAML formatted output indicating the weather forecast.
- **user**:
    - *Explanation*: The individual or system that provides input to the AI agent.
    - *Example*: A person asking a question or another system sending data.
- **system**:
    - *Explanation*: The internal workings of the AI agent, including its configurations and processing logic.
    - *Example*: Memory reset functions, processing methods, and rule enforcement mechanisms.
- **request**:
    - *Explanation*: A structured format in which the user provides input to the AI agent.
    - *Example*: A text input in a defined format, such as `TEXT="""<content>"""`.

## Use of Status Codes

The use of HTTP status codes in AI agent configuration is inspired by the standardized communication protocol used in web development. These codes provide a consistent way to communicate the outcome of a request or transaction.

### HTTP Status Codes

HTTP status codes are standardized codes returned by servers to indicate the result of a client's request. They are divided into five categories:

1. **1xx (Informational)**: The request was received, continuing the process.
2. **2xx (Success)**: The request was successfully received, understood, and accepted.
3. **3xx (Redirection)**: Further action needs to be taken to complete the request.
4. **4xx (Client Error)**: The request contains bad syntax or cannot be fulfilled.
5. **5xx (Server Error)**: The server failed to fulfill a valid request.

### Status Codes in the Agent Configuration

In the context of the agent configuration described, status codes are used to indicate the outcome of processing the input request.

### Success (Status 200)

A `200` status code

indicates that the request was successfully processed. The agent will return a structured response containing the results of the processing.

Example:

```yaml
default:
  status: 200
  sections:
    frontend:
      meta: "Information that will be presented to humans."
      exec: agent.skills.talk(response, french), agent.skills.run(10mph)
      constraint: readable by human.
      presence: mandatory
    backend:
      meta: "Provide instructions for other systems or AI agents."
      exec: agent.skills.call_help()
      constraint: readable by other AI agents, machine to machine.
      presence: mandatory

```

### Error (Status 400)

A `400` status code indicates that there was an error with the request, such as incorrect formatting or missing information. The agent responds by resetting its memory and preparing for a new request.

```yaml
invalid:
  status: 400
  sections:
    clean:
      meta: "Clean the memory"
      exec: - system.reset_memory(), system.load(agent), system.response = {'ready': True}
      constraint: None
      presence: mandatory

```

---
