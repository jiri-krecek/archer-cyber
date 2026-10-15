<!--
Version: 1.1
Author: Jiri Krecek
Company: Archer Dynamics LLC (goarcherdynamics.com)
License: MIT, free to use, modify, and distribute with attribution
AI Notice: This content was co-developed with Claude AI (Anthropic); logic,
design, and testing by the author.
-->

# archer-cyber

Security tooling and reference material from the Archer Dynamics lab.

### Contents

- `wordlists/` - URL path wordlists for finding AI/ML infrastructure with ffuf and similar tools. Currently holds `ai-endpoints.txt`, covering model servers, LLM runtimes, MLflow, Kubeflow, Ray, vector databases, Jupyter, MinIO and API documentation pages. Paths come from public project documentation and not every entry has been tested against a live service.

Use only against systems you own or have written permission to test.

### License

MIT, see [LICENSE](LICENSE).
