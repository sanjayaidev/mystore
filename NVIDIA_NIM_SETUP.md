# Setting Up NVIDIA NIM (DeepSeek Pro) in Directus AI

This guide will help you configure your NVIDIA NIM DeepSeek Pro model as an AI agent in Directus.

## ⚠️ Important: Use OpenAI-Compatible Provider

**Directus does NOT have a native "NVIDIA" or "NIM" provider.** You must use the **OpenAI-Compatible** provider instead, which works with NVIDIA NIM since it follows the OpenAI API format.

When configuring in the UI:
- **DO NOT** select or type "nvidia" as the provider type
- **DO** use the "OpenAI-Compatible" provider section
- The provider type must be exactly: `openai-compatible`

## Configuration Methods

### Method 1: Using the Admin UI (Recommended)

1. **Navigate to Settings → AI** in your Directus admin panel

2. **Scroll to the "OpenAI-Compatible" section** (not a separate NVIDIA section)

3. **Fill in these fields:**
   - **Name** (`ai_openai_compatible_name`): `NVIDIA NIM` (or any name you prefer)
   - **Base URL** (`ai_openai_compatible_base_url`): `https://integrate.api.nvidia.com/v1`
     - ⚠️ Must include `/v1` at the end
   - **API Key** (`ai_openai_compatible_api_key`): Your NVIDIA API key from [NVIDIA Build](https://build.nvidia.com/)

4. **Add Custom Models** in the "Custom Models" field (`ai_openai_compatible_models`):

Click the "+" button to add each model. For DeepSeek R1:

```json
{
  "id": "deepseek-ai/deepseek-r1",
  "name": "DeepSeek R1",
  "context": 128000,
  "output": 16000,
  "attachment": false,
  "reasoning": true
}
```

**Important**: The `id` field must match the exact model ID from NVIDIA NIM.

5. **Save** your settings by clicking the checkmark icon in the top-right

6. **(Optional) Set Allowed Models**:
   - If you want to restrict which models can be used, add model IDs to "OpenAI Compatible Allowed Models"
   - Leave empty to allow all configured custom models

### Method 2: Environment Variables

For Docker deployments, add these to your `.env` or docker-compose.yml:

```bash
# Enable AI features
AI_ENABLED=true

# OpenAI-Compatible provider (for NVIDIA NIM)
AI_OPENAI_COMPATIBLE_NAME="NVIDIA NIM"
AI_OPENAI_COMPATIBLE_BASE_URL="https://integrate.api.nvidia.com/v1"
AI_OPENAI_COMPATIBLE_API_KEY="nvapi-your-nvidia-api-key-here"

# Custom models (JSON array - escape quotes for shell)
AI_OPENAI_COMPATIBLE_MODELS='[{"id":"deepseek-ai/deepseek-r1","name":"DeepSeek R1","context":128000,"output":16000,"attachment":false,"reasoning":true}]'

# Optional: Allow all openai-compatible models (leave empty or omit)
# AI_OPENAI_COMPATIBLE_ALLOWED_MODELS='["deepseek-ai/deepseek-r1"]'
```

**Docker Compose Example:**
```yaml
services:
  directus:
    image: directus/directus:latest
    environment:
      AI_ENABLED: "true"
      AI_OPENAI_COMPATIBLE_NAME: "NVIDIA NIM"
      AI_OPENAI_COMPATIBLE_BASE_URL: "https://integrate.api.nvidia.com/v1"
      AI_OPENAI_COMPATIBLE_API_KEY: "nvapi-your-key-here"
      AI_OPENAI_COMPATIBLE_MODELS: '[{"id":"deepseek-ai/deepseek-r1","name":"DeepSeek R1","context":128000,"output":16000,"attachment":false,"reasoning":true}]'
```

### Method 3: Direct Database Update

Update the `directus_settings` table directly:

```sql
UPDATE directus_settings 
SET 
  ai_openai_compatible_name = 'NVIDIA NIM',
  ai_openai_compatible_base_url = 'https://integrate.api.nvidia.com/v1',
  ai_openai_compatible_api_key = 'nvapi-your-nvidia-api-key-here',
  ai_openai_compatible_models = '[{"id":"deepseek-ai/deepseek-r1","name":"DeepSeek R1","context":128000,"output":16000,"attachment":false,"reasoning":true}]'::jsonb
WHERE id = 1;
```

Then restart Directus.

## Available DeepSeek Models on NVIDIA NIM

| Model ID | Name | Context | Output | Reasoning | Description |
|----------|------|---------|--------|-----------|-------------|
| `deepseek-ai/deepseek-r1` | DeepSeek R1 | 128,000 | 16,000 | ✅ Yes | Latest reasoning model |
| `deepseek-ai/deepseek-v3` | DeepSeek V3 | 128,000 | 16,000 | ❌ No | Standard chat model |
| `deepseek-ai/deepseek-r1-distill-llama-70b` | DeepSeek R1 Distill | 128,000 | 16,000 | ✅ Yes | Distilled version |

### Example Configurations

**DeepSeek R1 (Recommended):**
```json
{
  "id": "deepseek-ai/deepseek-r1",
  "name": "DeepSeek R1",
  "context": 128000,
  "output": 16000,
  "attachment": false,
  "reasoning": true
}
```

**DeepSeek V3:**
```json
{
  "id": "deepseek-ai/deepseek-v3",
  "name": "DeepSeek V3",
  "context": 128000,
  "output": 16000,
  "attachment": false,
  "reasoning": false
}
```

**Multiple Models:**
```json
[
  {
    "id": "deepseek-ai/deepseek-r1",
    "name": "DeepSeek R1",
    "context": 128000,
    "output": 16000,
    "attachment": false,
    "reasoning": true
  },
  {
    "id": "deepseek-ai/deepseek-v3",
    "name": "DeepSeek V3",
    "context": 128000,
    "output": 16000,
    "attachment": false,
    "reasoning": false
  }
]
```

## Getting Your NVIDIA API Key

1. Visit [https://build.nvidia.com/](https://build.nvidia.com/)
2. Sign in or create a free account
3. Search for "DeepSeek" or navigate to the model page
4. Click **"Get API Key"** or go to API Keys section
5. Generate a new API key (starts with `nvapi-`)
6. Copy and save it securely

## Testing Your Configuration

1. **Open AI Chat**: Click the chat icon in the bottom-right corner of Directus
2. **Select Model**: Click the model dropdown at the top of the chat panel
3. **Choose Provider**: You should see "NVIDIA NIM" or "openai-compatible" listed
4. **Select Model**: Choose "DeepSeek R1" (or whichever model you configured)
5. **Send Test Message**: Type "Hello, can you help me with a task?"

## Common Errors & Solutions

### ❌ Error: "invalid NVIDIA NIM provider options"

**Cause**: You're trying to use "nvidia" as a provider type, which doesn't exist.

**Solution**: 
1. Use the **OpenAI-Compatible** provider instead
2. In the UI, fill in the OpenAI-Compatible section fields
3. The provider type will automatically be `openai-compatible`

### ❌ Error: "Model not allowed for this provider"

**Cause**: The model ID isn't in the allowed list.

**Solution**: 
1. Go to Settings → AI
2. Either:
   - Add the model ID to "OpenAI Compatible Allowed Models" field, OR
   - Leave the allowed models field empty (allows all configured models)
3. Save and refresh

### ❌ Error: "No API key configured for LLM provider"

**Cause**: API key is missing or not saved.

**Solution**: 
1. Verify Settings → AI shows your API key (masked with dots)
2. If using environment variables, ensure they're loaded:
   ```bash
   docker exec <container> env | grep AI_OPENAI
   ```
3. Restart Directus after changing environment variables

### ❌ Error: "Failed to fetch" or network errors

**Possible causes:**
- Incorrect Base URL
- Firewall blocking requests
- Invalid API key

**Solution**: Test the API directly:
```bash
curl -X POST https://integrate.api.nvidia.com/v1/chat/completions \
  -H "Authorization: Bearer nvapi-YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek-ai/deepseek-r1",
    "messages": [{"role": "user", "content": "Hello"}],
    "max_tokens": 100
  }'
```

Expected response should contain a `choices` array with a message.

### ❌ Model Not Appearing in Dropdown

**Solution**:
1. Verify the JSON in Custom Models is valid (use a JSON validator)
2. Check the `id` field matches NVIDIA's exact model ID
3. Ensure API key and base URL are saved
4. Clear browser cache and refresh
5. Check browser console for JavaScript errors

### ❌ Provider Shows But No Models Listed

**Solution**:
1. Make sure you added models to the Custom Models field
2. Verify the JSON syntax is correct
3. Check that `ai_openai_compatible_models` contains valid JSON array
4. Try adding just one simple model first to test

## Field Reference

### Custom Models JSON Schema

Each model object supports these fields:

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `id` | string | ✅ Yes | - | Exact model ID from NVIDIA NIM |
| `name` | string | ✅ Yes | - | Display name in Directus UI |
| `context` | number | No | 128000 | Maximum context window (tokens) |
| `output` | number | No | 16000 | Maximum output tokens |
| `attachment` | boolean | No | false | Support for file attachments |
| `reasoning` | boolean | No | false | Support for reasoning/thinking |
| `providerOptions` | object | No | {} | Advanced provider-specific options |

### Custom Headers (Optional)

If NVIDIA requires custom headers:

```json
[
  {
    "header": "X-Custom-Header",
    "value": "custom-value"
  }
]
```

## Security Best Practices

1. **Never commit API keys** to Git or version control
2. **Use environment variables** in production deployments
3. **Rotate API keys** periodically via NVIDIA dashboard
4. **Restrict models** using allowed models lists when possible
5. **Monitor usage** through NVIDIA's API dashboard
6. **Use HTTPS** for all API communications (already enforced by NVIDIA)

## Verification Checklist

Before troubleshooting, verify:

- [ ] AI is enabled (`AI_ENABLED=true` or in Settings)
- [ ] Using OpenAI-Compatible provider (NOT "nvidia")
- [ ] Base URL includes `/v1`: `https://integrate.api.nvidia.com/v1`
- [ ] API key starts with `nvapi-`
- [ ] Custom Models JSON is valid
- [ ] Model `id` matches NVIDIA's exact model ID
- [ ] Browser cache cleared after changes
- [ ] Directus restarted (if using environment variables)

## Additional Resources

- [Directus AI Documentation](https://docs.directus.io/guides/integrations/ai-agents.html)
- [NVIDIA NIM Documentation](https://docs.nvidia.com/nim/)
- [DeepSeek on NVIDIA Build](https://build.nvidia.com/deepseek-ai/deepseek-r1)
- [Vercel AI SDK - OpenAI Compatible](https://sdk.vercel.ai/providers/ai-sdk-providers/openai-compatible)
- [Directus GitHub Issues](https://github.com/directus/directus/issues) (for bugs)

## Need More Help?

If you're still experiencing issues:

1. Check the Directus server logs for detailed error messages
2. Test the NVIDIA API directly with curl (see above)
3. Verify your NVIDIA API key has the necessary permissions
4. Check if your Directus version supports AI features (v11+)
5. Search/create an issue on the Directus GitHub repository
