# runpod

The Docker image for the RunPod Serverless worker that serves an Ollama model behind RunPod's job
API. The web app in `chargingthefuture/chargingthefuture` talks to it through
`lib/chatbot/ollama.ts` when `OLLAMA_BASE_URL` is the endpoint URL (`https://api.runpod.ai/v2/<id>`)
and `OLLAMA_API_KEY` is the RunPod API key.

The repository holds one file, `Dockerfile`. Its comments are the documentation: the handler is
written inline so the build does not depend on RunPod's build context, the base image is pinned by
digest, and the model is baked into the image so a cold start does not also pay a download.

## Changing the model

The default is `qwen2.5:32b`, sized for a 24 GB GPU. Override at build time:

```bash
docker build --build-arg OLLAMA_MODEL=llama3.3 .
```

Bump the base image tag and its digest together when adopting a newer Ollama.

## Where the rest is documented

- `ctf/docs/developer/OLLAMA.md` in the product repository: sizing, provisioning the endpoint,
  failure modes.
- The `render.yaml` note in the product repository records why the earlier Render CPU service
  was removed (2026-06-14) in favor of this endpoint.
