# Deploy and Host Rustrak on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/rustrak?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=rustrak)

[Rustrak](https://github.com/rustrak/rustrak) is a self-hosted error tracker that speaks the Sentry protocol. The Sentry SDK already in your app works with it: change the DSN and your errors, stack traces, logs, transactions and release health go to your own server instead of sentry.io. It's a single Rust process with SQLite and a built-in dashboard, so there's no Postgres, Redis, Kafka or ClickHouse to run.

## About Hosting Rustrak

It's one service and a Railway volume.

- **Admin from the first boot.** You enter your email when you deploy, and the admin account is created on first boot with a generated password. Sign-up is invite-only, so nobody else can register.
- **DSNs that work.** `PUBLIC_URL` is set to your Railway domain, so the DSN each project shows in the dashboard is ready to paste.
- **Private-network ingest.** Rustrak listens on IPv6 too, so apps in the same Railway project can send their errors over the private network.
- **Survives redeploys.** The SQLite database and uploaded source maps live on the volume.

## Common Use Cases

- Error tracking for the apps you already run on Railway, without per-event pricing or sampling
- A drop-in replacement for sentry.io when the bill or the data location is the problem
- Logs, performance traces and release health (crash-free sessions) for small teams, in one dashboard

## Dependencies for Rustrak Hosting

- Nothing else: the database is SQLite on the service's volume

### Deployment Dependencies

- [Rustrak documentation](https://rustrak.github.io/rustrak/)
- [Rustrak on GitHub](https://github.com/rustrak/rustrak)
- [Template source on GitHub](https://github.com/nomideusz/rustrak-railway)

### Implementation Details

**Sign in.** Open the service's Railway domain and sign in with the `RUSTRAK_ADMIN_EMAIL` and `RUSTRAK_ADMIN_PASSWORD` values from the Variables tab. They are only used to create the admin on first boot. To change your password later, use the dashboard; editing the variable won't change it.

**Send your first error.** Create a project in the dashboard and copy its DSN into your Sentry SDK setup, for example in Python:

```python
import sentry_sdk
sentry_sdk.init(dsn="https://<key>@<your-app>.up.railway.app/1")
```

Every official Sentry SDK works the same way (JavaScript, Python, Go, Ruby, PHP, Java, .NET, Rust and more).

**Report over the private network.** For an app in the same Railway project, swap the DSN's host for the private one and use plain http on port 8080: `http://<key>@${{Rustrak.RAILWAY_PRIVATE_DOMAIN}}:8080/1`. That traffic never leaves Railway, and it's not billed as egress.

**Teammates.** Settings → Team → invite, then copy the invite link and send it yourself. No email server is needed.

**Alerts.** Each project can alert on new or regressed issues through a webhook or Slack. Email alerts need `SMTP_HOST`, `SMTP_PORT`, `SMTP_USERNAME`, `SMTP_PASSWORD` and `SMTP_FROM`; Railway allows outbound SMTP on the Pro plan only.

**Custom domain.** After adding one, set `PUBLIC_URL` to `https://your.domain` and redeploy, so new DSNs use it.

**Memory.** Rustrak idles at about 30 MB. It accepts events by writing them to disk first and processing them in the background, so a burst of errors doesn't slow your app down.

**Telemetry.** Upstream Rustrak sends anonymous usage telemetry. Add `RUSTRAK_TELEMETRY=off` to turn it off.

## Why Deploy Rustrak on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying Rustrak on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
