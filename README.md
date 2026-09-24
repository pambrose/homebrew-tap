# pambrose/homebrew-tap

Homebrew formulae for [prometheus-proxy](https://github.com/pambrose/prometheus-proxy), which lets Prometheus
scrape endpoints behind a firewall. The proxy runs outside the firewall next to Prometheus; the agent runs inside
it next to the services it scrapes.

## Install

```bash
brew install pambrose/tap/prometheus-proxy   # outside the firewall
brew install pambrose/tap/prometheus-agent   # inside the firewall
```

Each formula runs the release JAR on `openjdk@25`, which Homebrew installs as a dependency, and works on macOS
and Linux.

## Run

In the foreground, until Ctrl+C:

```bash
prometheus-proxy
prometheus-agent --proxy proxy-host.example.com --config agent.conf
```

In the background with `brew services`, which also starts them again at login:

```bash
brew services start prometheus-proxy
brew services start prometheus-agent
```

| Formula            | Config the service reads                     | Service log                                     |
|--------------------|----------------------------------------------|-------------------------------------------------|
| `prometheus-proxy` | `$(brew --prefix)/etc/prometheus-proxy.conf` | `$(brew --prefix)/var/log/prometheus-proxy.log` |
| `prometheus-agent` | `$(brew --prefix)/etc/prometheus-agent.conf` | `$(brew --prefix)/var/log/prometheus-agent.log` |

Each formula installs a starting config there, and an upgrade keeps your edited copy. Set the agent's proxy and
paths before starting its service, and set up agent authentication on the proxy before exposing its agent port
(50051). The [Quick Start](https://pambrose.github.io/prometheus-proxy/getting-started/) covers both in detail.

## Updating the formulae

Don't edit `Formula/` here. The formulae's sources are in
[`etc/homebrew/`](https://github.com/pambrose/prometheus-proxy/tree/master/etc/homebrew) in prometheus-proxy,
and after each release `make homebrew-formulae` there writes them into a clone of this repository with the new
version and checksums (step 9 of
[`docs/RELEASE.md`](https://github.com/pambrose/prometheus-proxy/blob/master/docs/RELEASE.md)).

For every push and pull request, [CI](.github/workflows/ci.yml) runs `brew style` and `brew audit` on both
formulae, installs and tests them on macOS and Linux, and scrapes a metrics endpoint through the installed proxy
and agent.
