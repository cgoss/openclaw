# API Key Rotation in Model Fallback - Implementation Plan

**GitHub Issue:** https://github.com/cgoss/openclaw/issues/1

## Summary

Add API key rotation to the model fallback system so that when one key hits a rate limit, it tries the next key for the same provider before moving to the next model in the fallback chain.

## Problem

Currently, the model fallback system works at the provider/model level but only uses ONE key per provider. When key1 hits a rate limit, it immediately moves to the next model in the fallback chain, even if additional keys are available for the same provider.

## Desired Behavior

```
Agent Config:
  primary: "openai/gpt-5"
  fallbacks:
    - "opencode/gpt-5"
    - "anthropic/claude-3.5"

Execution:
1. openai/gpt-5: key1 -> rate limit -> key2 -> rate limit -> key3 -> exhausted
2. opencode/gpt-5: key1 -> rate limit -> key2 -> exhausted
3. anthropic/claude-3.5: key1 -> success!
```

This respects:
- The agent's configured fallback chain
- Key rotation within each provider
- Same model on different providers (via fallback config)
- Different models on different providers

## Configuration

Uses existing environment variables - no new config needed:

- `<PROVIDER>_API_KEY` (primary key)
- `<PROVIDER>_API_KEYS` (comma/semicolon separated list)
- `<PROVIDER>_API_KEY_1`, `<PROVIDER>_API_KEY_2`, etc. (numbered keys)
- `OPENCLAW_LIVE_<PROVIDER>_KEY` (single override, highest priority)

## Files Modified

- `src/agents/model-fallback.ts` - Added key rotation loop in the main fallback function

## Technical Approach

1. Import `collectProviderApiKeys` from `src/agents/live-auth-keys.ts` (already exists)
2. In the main fallback loop, before executing each candidate model:
   - Get all available keys for that provider
   - Loop through keys, retry on rate limit
   - Only move to next model when all keys exhausted
3. Reuse existing rate-limit detection from `classifyFailoverReason()`

## Backward Compatibility

✅ **Fully backward compatible**
- If only one key is configured, behavior unchanged
- If no keys configured, fails with auth error as before
- Existing fallback chain behavior preserved
- Key rotation only activates when multiple keys are available

## Related

- Existing key rotation: `src/agents/api-key-rotation.ts` (works for embeddings and media only)
