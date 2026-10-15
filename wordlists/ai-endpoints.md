<!--
Version: 1.0
Author: Jiri Krecek
Company: Archer Dynamics LLC (goarcherdynamics.com)
License: MIT, free to use, modify, and distribute with attribution
AI Notice: This content was co-developed with Claude AI (Anthropic); logic,
design, and testing by the author.
-->

# ai-endpoints.txt: User Guide

## What it is

A list of URL paths used by AI/ML infrastructure: model servers, LLM runtimes, experiment trackers, vector databases, notebooks and object storage. Standard wordlists are built around traditional web apps (`/users`, `/admin`) and miss action-style paths like `/predict`, `/v2/models` and `/api/tags`. This list fills that gap.

The file has one path per line, in ASCII sort order, with no comments and no leading slashes. The paths come from public project documentation, and not every entry has been tested against a live service, so expect some 404s.

## Before you start

Only run this against systems you own or have written permission to test. Scanning someone else's network is illegal in many places, even if nothing is exploited.

You also need ffuf installed, a target IP, and a port that is already known to be open. The list works best when pointed at specific AI ports, not at everything.

## Step 1: Find the open ports

The list is only useful against a port that answers, so scan for the common AI ports first.

```bash
nmap -p 5000,6333,6334,8000,8001,8002,8080,8081,8082,8265,8500,8501,8888,9000,9001,11434,19530 -sV TARGET  # AI service ports, with version detection
```

Add `-p-` for a full port scan if you suspect services were moved off their defaults.

## Step 2: Run the wordlist against each open port

Run it once per port, because many paths only exist on one service. A path that returns 404 on port 5000 may be valid on port 8000.

```bash
ffuf -u http://TARGET:PORT/FUZZ -w ai-endpoints.txt -mc 200,204,401,403,405  # basic run: show only paths that exist
ffuf -u http://TARGET:PORT/FUZZ -w ai-endpoints.txt -mc 200,204,401,403,405 -t 10  # same, but 10 threads so a small server isn't flooded
ffuf -u http://TARGET:PORT/FUZZ -w ai-endpoints.txt -mc 200,204,401,403,405 -o results.json -of json  # save results to a file
```

If a server answers 200 to everything (a "soft 404"), add `-ac` and ffuf will calibrate against the junk responses and filter them out.

## Step 3: Read the status codes

The status code tells you whether a path exists, not only whether it worked.

| Code | Meaning |
|---|---|
| 200 | Path exists and answered. Look at the response. |
| 401 or 403 | Path exists but requires authentication. A finding, and a good sign for the defender. |
| 405 | Path exists but wants a different HTTP method (usually POST). Also a useful fingerprint. |
| 500 | Path exists and the server errored. Verbose errors often leak details. |
| 404 | Not there. |

The wordlist sends GET requests only, so POST-only endpoints (such as MLflow's `experiments/search`) show up as 405, not 200.

## Step 4: Follow up on each hit

A hit gives you a path and nothing more. To find out what is behind it, look at the response itself.

```bash
curl -i http://TARGET:PORT/PATH  # -i prints the response headers above the body, which identify the framework
```

From there, fingerprint the service from its headers, JSON structure and error messages, then enumerate what it exposes.

## Notes

Many AI services ship with no authentication, so a 200 on a metadata path usually means the service is open to anyone who can reach the port. Treat each one as a finding to fix, not just to document.