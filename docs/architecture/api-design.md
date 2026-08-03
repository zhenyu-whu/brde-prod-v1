# API Design Principles

## Execute API

Example:

```
POST /decision/{flowId}/execute
```

Response contains:

- decision result
- trace identifier

Errors use unified error code and message structure.
