# Scorify

> [!NOTE]
> Real documentation is coming soon, I promise :tm:

## Quick Start

In the meantime here is a quick start...

### 1. Install dependencies

```
# Install Docker
curl https://get.docker.com | sh

# Install Golang (or goto https://go.dev/doc/install)
wget https://go.dev/dl/go1.23.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.23.1.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

### 2. Clone Repo

```
git clone https://github.com/Scorify/Scorify.git
cd Scorify
```

### 3. Run Setup Script

> [!NOTE]
> For `domain` if you want do not want to use ACME certificate you can:
>
> 1. Use your IP to use self-signed certificates
> 2. Use `localhost` to use `http`

```
go run main.go setup
```

### 4. Start Scorify

```
docker compose up -d
```

You can now visit the Scorify webapp at the domain/ip you provided during the setup script

> [!NOTE]
> Default Credentials are `admin:admin`

### 5. Upgrade Scorify

```
git pull
docker compose build
docker compose down
docker compose up -d
```

## Profiling with pprof flame graphs

Scorify exposes an optional Go pprof server so you can capture CPU profiles and view flame graphs.

### Enable the pprof server

1. Set the environment variables (in `.env` or your deployment system):
   - `PPROF_ENABLED=true`
   - `PPROF_PORT=6060` (or any open port you prefer)
2. If you are using Docker Compose, ensure the port is published (the default `docker-compose.yml` now publishes the pprof port).
3. Restart the `scorify` service.

### Capture a CPU profile and open the flame graph

From your workstation (with Go installed), run:

```
go tool pprof -http=:0 http://localhost:6060/debug/pprof/profile?seconds=30
```

This opens the pprof UI in your browser. Use the **Flame Graph** view to analyze hotspots.

> [!NOTE]
> If your browser blocks the UI due to a `:0` redirect, specify an explicit host or port such as
> `-http=127.0.0.1:0` (random port) or `-http=localhost:6061` (fixed port).
>
> If the flame graph view looks empty, ensure the server is under load during the capture window
> (the UI only renders samples that were collected while profiling).

### Other useful profiles

* Heap profile: `go tool pprof -http=:0 http://localhost:6060/debug/pprof/heap`
* Goroutine dump: `curl http://localhost:6060/debug/pprof/goroutine?debug=2`
