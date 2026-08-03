# Execution Design

## Flow

```
API Request
 -> Executor
 -> Load Artifact
 -> Execute Nodes
 -> Generate Trace
 -> Response
```

Execution contains:

- Request context
- Node execution result
- Trace information
- Final response
