<!--
Version: 1.0
Author: Jiri Krecek
Company: Archer Dynamics LLC (goarcherdynamics.com)
License: MIT, free to use, modify, and distribute with attribution
AI Notice: This content was co-developed with Claude AI (Anthropic); logic,
design, and testing by the author.
-->

# archer-cyber

Security tooling and reference material from the Archer Dynamics lab.

## wordlists/ai-endpoints.txt

A wordlist of URL paths for finding AI/ML infrastructure with ffuf or similar tools. Standard wordlists are built around traditional web apps (`/users`, `/admin`) and miss the action-style paths AI services use (`/predict`, `/v2/models`, `/api/tags`).

It covers model servers (Triton, TorchServe, TensorFlow Serving), LLM runtimes (Ollama, vLLM, LiteLLM), lifecycle tools (MLflow, Kubeflow, Ray), vector databases (Qdrant, Weaviate, Chroma), Jupyter, MinIO, and the API documentation pages (`/docs`, `/openapi.json`) that expose a full route list.

Paths are compiled from public project documentation. Not every entry has been validated against a live service, so expect some 404s. Entries are in ASCII sort order, one per line, with no comments.

## Usage

Point it at a port you found open. Many paths only exist on one service's port, so run it per port, not only against port 80.

```bash
ffuf -u http://TARGET:PORT/FUZZ -w wordlists/ai-endpoints.txt -mc 200,204,401,403,405
```

A 405 is worth keeping in the match list: it means the path exists but wants a different HTTP method, which is itself a fingerprint.

Use this only against systems you own or have written permission to test.

## License

MIT, see [LICENSE](LICENSE).