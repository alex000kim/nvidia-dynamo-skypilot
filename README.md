# Nvidia Dynamo + SkyPilot Demo

This demo shows how to serve models using [Nvidia Dynamo](https://github.com/ai-dynamo/dynamo) and [SkyPilot](https://docs.skypilot.co/en/latest/docs/index.html).

## Launch Cluster

```bash
sky launch -c dynamo-serving nvidia-dynamo.sky.yaml
```

## Test Endpoint

```bash
export ENDPOINT=$(sky status --endpoint 8080 dynamo-serving)

curl http://$ENDPOINT/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen3-8B",
    "messages": [
    {
        "role": "user",
        "content": "Hello, how are you?"
    }
    ],
    "stream":false,
    "max_tokens": 300
  }' | jq
...
{
  "id": "chatcmpl-e2b5b2bd-59fb-4321-8afc-3b5bb4a717a7",
  "choices": [
    {
      "index": 0,
      "message": {
        "content": "<think>\nOkay, the user greeted me with \"Hello, how are you?\" I should respond in a friendly and natural way. Let me think about the appropriate response.\n\nFirst, I need to acknowledge their greeting. Maybe start with a cheerful \"Hello!\" to match their tone. Then, I should mention that I'm just a virtual assistant, so I don't have feelings, but I'm here to help. It's important to keep it conversational.\n\nI should make sure to invite them to ask questions or share what they need help with. That way, it's open-ended and encourages further interaction. Also, adding an emoji like 😊 can make the response more friendly and approachable.\n\nWait, should I mention my name again? Maybe not necessary since the user already knows. Just keep it simple and welcoming. Let me check the example response they provided. Yes, it's similar to that. I think that's all. Keep the tone positive and helpful.\n</think>\n\nHello! 😊 I'm just a virtual assistant, so I don't have feelings, but I'm here to help you with whatever you need! What can I assist you with today?",
        "role": "assistant",
        "reasoning_content": null
      },
      "finish_reason": "stop"
    }
  ],
  "created": 1758497220,
  "model": "Qwen/Qwen3-8B",
  "object": "chat.completion",
  "usage": {
    "prompt_tokens": 14,
    "completion_tokens": 235,
    "total_tokens": 249
  }
}
```

## Multi-Node Serving

### Launch Multi-Node Cluster

```bash
sky launch -c dynamo-serving-multi nvidia-dynamo-multinode.sky.yaml
```

### Test Multi-Node Endpoint

```bash
export ENDPOINT=$(sky status --endpoint 8080 dynamo-serving-multi)

curl http://$ENDPOINT/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen3-8B",
    "messages": [
    {
        "role": "user",
        "content": "Hello, how are you?"
    }
    ],
    "stream":false,
    "max_tokens": 300
  }' | jq
```

Example output:
```json
{
  "id": "chatcmpl-5524560e-aecd-4b63-a41b-23d0a787c9b0",
  "choices": [
    {
      "index": 0,
      "message": {
        "content": "<think>\nOkay, the user greeted me with \"Hello, how are you?\" I need to respond appropriately. Let me start by acknowledging their greeting. I should mention that I'm an AI assistant, so I don't have feelings, but I'm here to help.\n\nI should keep the response friendly and open-ended. Maybe ask them how they're doing to encourage a conversation. Let me check if there's anything specific they might need. Oh, maybe they have a question or need assistance with something. I should make sure to invite them to ask for help if needed. Also, keep the tone positive and approachable. Alright, putting it all together now.\n</think>\n\nHello! I'm just a virtual assistant, so I don't have feelings, but I'm here and ready to help! How are you today? 😊 If you have any questions or need assistance, feel free to ask!",
        "role": "assistant",
        "reasoning_content": null
      },
      "finish_reason": "stop"
    }
  ],
  "created": 1758501329,
  "model": "Qwen/Qwen3-8B",
  "object": "chat.completion",
  "usage": {
    "prompt_tokens": 14,
    "completion_tokens": 181,
    "total_tokens": 195
  }
}
```