# Adding NVIDIA NIM (DeepSeek Pro) to Directus AI Agents

Directus supports NVIDIA NIM endpoints through its **OpenAI-Compatible** provider configuration. NVIDIA's API is compatible with OpenAI's API format, making it easy to integrate.

## Prerequisites

1. A NVIDIA NGC account with API access
2. Your NVIDIA NIM API key from https://build.nvidia.com/
3. Access to the DeepSeek Pro model (or other models available on NVIDIA NIM)

## Configuration Steps

### Option 1: Using the Directus Admin UI (Recommended)

1. **Navigate to Settings**
   - Go to your Directus instance
   - Click on **Settings** (gear icon)
   - Navigate to **AI** settings

2. **Configure OpenAI-Compatible Provider**
   
   Fill in the following fields:
   
   - **Name**: `NVIDIA NIM` (or any descriptive name)
   - **Base URL**: `https://integrate.api.nvidia.com/v1`
   - **API Key**: Your NVIDIA NIM API key (from https://build.nvidia.com/explore/discover)
   - **Headers** (optional): Leave empty unless you need custom headers
   
3. **Add Custom Models**
   
   In the **Custom Models** section, add your DeepSeek Pro model:
   
   ```json
   [
     {
       "id": "deepseek-ai/deepseek-prover-v2-7b",
       "name": "DeepSeek Prover V2 7B",
       "context": 128000,
       "output": 16000,
       "attachment": false,
       "reasoning": true
     }
   ]
   ```
   
   **Common NVIDIA NIM DeepSeek Models:**
   - `deepseek-ai/deepseek-r1` - DeepSeek R1 (reasoning model)
   - `deepseek-ai/deepseek-v3` - DeepSeek V3
   - `deepseek-ai/deepseek-prover-v2-7b` - DeepSeek Prover V2 7B
   
   Adjust the `context` and `output` limits based on the specific model capabilities.

4. **Save Settings**
   - Click **Save** to apply the configuration

### Option 2: Environment Variables

If you prefer using environment variables, add these to your `.env` file or Docker configuration:

```bash
# NVIDIA NIM Configuration (via OpenAI-Compatible provider)
PUBLIC_AI_OPENAI_COMPATIBLE_NAME="NVIDIA NIM"
AI_OPENAI_COMPATIBLE_BASE_URL="https://integrate.api.nvidia.com/v1"
AI_OPENAI_COMPATIBLE_API_KEY="nvapi-YOUR-API-KEY-HERE"

# Optional: Custom models configuration (JSON format)
AI_OPENAI_COMPATIBLE_MODELS='[{"id":"deepseek-ai/deepseek-r1","name":"DeepSeek R1","context":128000,"output":16000,"attachment":false,"reasoning":true}]'
```

### Option 3: Database Configuration

You can also configure this directly in the database:

```sql
UPDATE directus_settings 
SET 
  ai_openai_compatible_name = 'NVIDIA NIM',
  ai_openai_compatible_base_url = 'https://integrate.api.nvidia.com/v1',
  ai_openai_compatible_api_key = 'nvapi-YOUR-API-KEY-HERE',
  ai_openai_compatible_models = '[{"id":"deepseek-ai/deepseek-r1","name":"DeepSeek R1","context":128000,"output":16000,"attachment":false,"reasoning":true}]'
WHERE id = 1;
```

## Available DeepSeek Models on NVIDIA NIM

Here are some popular DeepSeek models available through NVIDIA NIM:

| Model ID | Name | Context | Output | Reasoning | Notes |
|----------|------|---------|--------|-----------|-------|
| `deepseek-ai/deepseek-r1` | DeepSeek R1 | 128,000 | 16,000 | ✅ | Advanced reasoning |
| `deepseek-ai/deepseek-v3` | DeepSeek V3 | 128,000 | 16,000 | ❌ | General purpose |
| `deepseek-ai/deepseek-coder` | DeepSeek Coder | 128,000 | 16,000 | ❌ | Code generation |
| `deepseek-ai/deepseek-prover-v2-7b` | DeepSeek Prover V2 7B | 128,000 | 16,000 | ✅ | Mathematical proofs |

> **Note**: Model availability may change. Check https://build.nvidia.com/models for the latest available models.

## Testing Your Configuration

After configuring, test your setup:

1. **Go to AI Chat**
   - Navigate to the AI Chat interface in Directus
   - Select your NVIDIA NIM provider from the model dropdown

2. **Send a Test Message**
   - Try a simple prompt like "Hello, what model are you?"
   - Verify you receive a response

3. **Check Logs**
   - Monitor your Directus logs for any errors
   - Look for API connection issues or authentication errors

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   - Verify your API key is correct (should start with `nvapi-`)
   - Ensure the key hasn't expired
   - Check that your NVIDIA NGC account has API access enabled

2. **Connection Errors**
   - Verify the base URL is correct: `https://integrate.api.nvidia.com/v1`
   - Check your network/firewall settings
   - Ensure outbound HTTPS (port 443) is allowed

3. **Model Not Found**
   - Verify the model ID is correct
   - Check that the model is available in your region
   - Ensure you have access to the specific model (some require special access)

4. **Rate Limiting**
   - NVIDIA NIM has rate limits based on your subscription tier
   - Check your usage at https://org.ngc.nvidia.com/usage
   - Consider upgrading your tier if needed

## Advanced Configuration

### Adding Custom Headers

If you need to add custom headers (for proxy configurations, etc.):

```json
[
  {"header": "X-Custom-Header", "value": "custom-value"}
]
```

### Multiple NVIDIA Models

You can configure multiple models from NVIDIA NIM:

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

## Cost Considerations

NVIDIA NIM pricing varies by model. Check current pricing at:
- https://build.nvidia.com/pricing
- https://org.ngc.nvidia.com/usage

Generally, NVIDIA NIM offers competitive pricing compared to other providers, especially for high-volume usage.

## Security Best Practices

1. **Protect Your API Key**
   - Never commit API keys to version control
   - Use environment variables or secrets management
   - Rotate keys periodically

2. **Limit Model Access**
   - Only enable models you actually need
   - Use Directus permissions to control who can use AI features

3. **Monitor Usage**
   - Regularly check your NVIDIA usage dashboard
   - Set up alerts for unusual activity
   - Review logs for potential abuse

## Additional Resources

- [NVIDIA NIM Documentation](https://docs.nvidia.com/nim/)
- [NVIDIA Build Platform](https://build.nvidia.com/)
- [Directus AI Documentation](https://docs.directus.io/guides/headless-cms/working-with-ai.html)
- [Vercel AI SDK](https://sdk.vercel.ai/docs)

## Support

If you encounter issues:
1. Check the Directus logs for error messages
2. Verify your NVIDIA NIM credentials and access
3. Consult the Directus community forums
4. Contact NVIDIA support for API-related issues
