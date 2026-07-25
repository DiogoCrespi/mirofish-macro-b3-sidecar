# MiroFish sidecar for Macro B3

This checkout contains the reproducible local sidecar integration used by
`macro_b3_bot`.

## Versioned fixes

- The image is built locally from the checked-in `Dockerfile` instead of
  silently pulling a mutable `latest` image.
- The service joins the external `mirofish_llm` Docker network so the backend
  can reach an Ollama container by service name.
- Zep nodes returned without custom labels are retained as real generic
  `Entity` nodes. No domain label is inferred; nodes are only promoted to
  simulation profiles when the sidecar's normal reader sees them.

## Local setup

Create the shared network once:

```powershell
docker network create mirofish_llm
```

Set `ZEP_API_KEY` and the Ollama-compatible values in a local `.env` file.
Never commit `.env`; it is ignored by this repository. A safe template is in
`.env.example`.

Build and start the sidecar:

```powershell
docker compose build --no-cache mirofish
docker compose up -d mirofish
```

The macro application should use `http://localhost:5001` for the sidecar. The
structured scenario extraction remains a separate, checksum-tracked step in
the macro repository; this sidecar only produces the real graph, simulation,
and report.

## Verification

```powershell
docker compose ps
Invoke-RestMethod http://localhost:5001/api/graph/project/list
```

The repository does not contain API keys, generated uploads, logs, or Docker
state.
