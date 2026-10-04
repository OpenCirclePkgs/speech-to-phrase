# Speech-to-Phrase (standalone Docker image)

Unofficial multi-arch Docker image (`linux/amd64`, `linux/arm64`) of the [Speech-to-Phrase add-on](https://github.com/OHF-Voice/apps/tree/main/speech-to-phrase) from OHF-Voice, for use without Home Assistant OS or the Supervisor.

Speech-to-Phrase is a fast, local speech-to-text service for Home Assistant (Wyoming protocol). It only recognizes the commands, entity names and automation phrases you enable.

A GitHub Action checks upstream daily. When the add-on version changes, it builds both architectures natively, merges them into one image, and pushes it tagged with the version and `latest`.

Not affiliated with the upstream project. See [OHF-Voice/apps](https://github.com/OHF-Voice/apps) for its license.

## Image

```
ghcr.io/opencirclepkgs/speech-to-phrase:<version>
```

## Compose

```yaml
speech-to-phrase:
  image: ghcr.io/opencirclepkgs/speech-to-phrase:2.2.0
  working_dir: /usr/src
  entrypoint: ["python3", "src/app.py"]
  command: ["--data", "/data", "--models-dir", "/data/models", "--language", "de",
            "--backend", "citrinet", "--wyoming-uri", "tcp://0.0.0.0:10400",
            "--host", "0.0.0.0", "--port", "8099"]
  environment:
    HASS_API: "http://homeassistant:8123/api"
    SUPERVISOR_TOKEN: "${HA_LONG_LIVED_TOKEN}"   # HA long-lived access token
  volumes:
    - ./speech-to-phrase/data:/data
  ports:
    - "127.0.0.1:8099:8099"                      # web UI, no auth: use an SSH tunnel
  restart: unless-stopped
  mem_limit: 4g
```

Add the Wyoming integration in Home Assistant manually (host: container name, port: `10400`).
