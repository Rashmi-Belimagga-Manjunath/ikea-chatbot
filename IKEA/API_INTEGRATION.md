# API Integration

## Supported Providers

### OpenAI — GPT-4o-mini

- **Endpoint:** `POST https://api.openai.com/v1/chat/completions`
- **Model:** `gpt-4o-mini`
- **Auth:** `Authorization: Bearer {API_KEY}`
- **Temperature:** 0.3
- **Tool calling:** Uses `tools` array with `type: "function"` definitions
- **ReAct loop:** Sends initial user message + tools; on `tool_calls` response, executes each tool, appends `role: "tool"` messages, and sends back for final response

#### Request Format
```json
{
  "model": "gpt-4o-mini",
  "messages": [
    { "role": "system", "content": "..." },
    { "role": "user", "content": "..." }
  ],
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "search_products",
        "description": "...",
        "parameters": {
          "type": "object",
          "properties": {
            "query": { "type": "string", "description": "..." }
          },
          "required": ["query"]
        }
      }
    }
  ],
  "tool_choice": "auto",
  "temperature": 0.3
}
```

#### Tool Response Format
```json
{
  "role": "tool",
  "tool_call_id": "call_abc123",
  "content": "{\"results\": [...], \"total\": 5}"
}
```

### Google — Gemini 2.0 Flash

- **Endpoint:** `POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key={API_KEY}`
- **Auth:** API key passed as query parameter
- **Temperature:** 0.3
- **Tool calling:** Uses `tools` array with `function_declarations`

#### Request Format
```json
{
  "system_instruction": {
    "parts": [{ "text": "..." }]
  },
  "contents": [
    { "role": "user", "parts": [{ "text": "..." }] }
  ],
  "tools": [
    {
      "function_declarations": [
        {
          "name": "search_products",
          "description": "...",
          "parameters": { ... }
        }
      ]
    }
  ],
  "generationConfig": { "temperature": 0.3 }
}
```

#### Tool Response Format
```json
{
  "role": "function",
  "parts": [{
    "functionResponse": {
      "name": "search_products",
      "response": { "content": { "results": [...], "total": 5 } }
    }
  }]
}
```

## Key Configuration

| Setting     | Value       |
|-------------|-------------|
| Temperature | 0.3         |
| Max history | 20 messages |
| System prompt | IKEA Ireland AI Shopping Assistant |

## API Key Storage

- **localStorage key:** `ikea_key`
- **Type storage key:** `ikea_type` (values: `"openai"` or `"gemini"`)
- **Key format:** `sk-...` (OpenAI) or `AIza...` (Gemini)
- **Persistence:** Survives page reloads via localStorage

## Setup Modal

The setup modal allows users to:
1. Select provider (OpenAI / Gemini)
2. Enter API key
3. Connect (saves to localStorage)
4. Start Demo mode (uses built-in offline logic)

## Conversation History

- Stored in memory as `msgs[]` array
- Trimmed to 20 most recent messages
- Used in full for OpenAI tool-call rounds and Gemini function-call rounds
