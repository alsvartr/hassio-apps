# Home Assistant App: ntfy

Push notifications to your phone or desktop using PUT/POST (self-hosted) ([ntfy.sh](https://ntfy.sh)) with Home Assistant ingress support and optional HTTPS via the HA Let's Encrypt add-on.

## Features

- **Home Assistant Ingress**: Access the ntfy web UI directly from the Home Assistant sidebar
- **Reverse proxy support**: Proxy via external nginx/caddy
- **SSL support**: Use certificates issued by the [Home Assistant Let's Encrypt add-on](https://github.com/home-assistant/addons/tree/master/letsencrypt)
- **Persistent storage**: Message cache, user database, and attachments survive restarts

## Installation

1. Add this repository to your Home Assistant add-on store
2. Install the **ntfy** add-on
3. Configure the add-on options (see below)
4. Start the add-on

## Configuration

### Minimal (ingress only, no external access)

No configuration needed. Start the add-on and access ntfy through the Home Assistant sidebar.

### With SSL (recommended for external access)

SSL certificates are managed by the [Home Assistant Let's Encrypt add-on](https://github.com/home-assistant/addons/tree/master/letsencrypt). The LE add-on issues the certificate and copies it to the shared `/ssl/` directory that ntfy reads from. Both add-ons must be configured with matching filenames.

**Step 1** — Configure the HA Let's Encrypt add-on to issue a cert for your ntfy domain and copy it to `/ssl/` with a unique filename:

```yaml
# HA Let's Encrypt add-on configuration
domains:
  - ntfy.example.com
certfile: ntfy.pem
keyfile: ntfy.key
```

**Step 2** — Configure ntfy to use those same filenames:

```yaml
# ntfy add-on configuration
domain: "ntfy.example.com"
ssl: true
certfile: "ntfy.pem"
keyfile: "ntfy.key"
auth_default_access: "deny-all"
```

**Step 3** — Run the HA Let's Encrypt add-on to issue/renew the certificate, then start ntfy.

Ensure port 443 is forwarded from your router to your Home Assistant host for external HTTPS access. The LE add-on handles renewal automatically; restart ntfy after each renewal to pick up the new certificate.

### Users

**Step 1** — Enable signup option

**Step 2** — Open ntfy web page and create user with `sign-up` link

**Step 3** — Add ACL and restart app

## Options

| Option | Default | Description |
|--------|---------|-------------|
| `domain` | `""` | Public domain name for external access |
| `ssl` | `false` | Enable HTTPS using certificates from `/ssl/` |
| `certfile` | `fullchain.pem` | SSL certificate filename (relative to `/ssl/`) |
| `keyfile` | `privkey.pem` | SSL private key filename (relative to `/ssl/`) |
| `enable_login` | `true` | Login support |
| `require_login` | `true` | Disable anonymous login |
| `enable_signup` | `true` | Enable signup on login page |
| `unifiedpush` | `true` | Enable UnifiedPush |
| `acl` | `[]` | ACL list (for example: `username:topic:rw` for private topic access, `username:up*:rw` for UP access) |
| `auth_default_access` | `deny-all` | Default access: `read-write`, `read-only`, `write-only`, `deny-all` |
| `upstream_base_url` | `https://ntfy.sh` | Upstream server for iOS push notifications |
| `log_level` | `info` | Log verbosity: `trace`, `debug`, `info`, `warn`, `error` |

## Ports

| Port | Purpose |
|------|---------|
| 2586 | ntfy HTTP API |
| 443 | ntfy HTTPS API (optional, requires `ssl: true`) |

## Usage

### Send a notification

```bash
curl -d "Hello from Home Assistant" https://ntfy.example.com/my-topic
```

### Subscribe in the ntfy app

Point the ntfy Android/iOS app at `https://ntfy.example.com` and subscribe to your topics.

### Home Assistant automations

Use the [REST command](https://www.home-assistant.io/integrations/rest_command/) integration:

```yaml
rest_command:
  ntfy_notify:
    url: "https://ntfy.example.com/my-topic"
    method: POST
    content_type: "application/json"
    payload: '{"message": "{{ message }}", "title": "{{ title }}"}'
```

## More information

- [ntfy documentation](https://docs.ntfy.sh/)
- [ntfy GitHub](https://github.com/binwiederhier/ntfy)
- [Home Assistant Let's Encrypt add-on](https://github.com/home-assistant/addons/tree/master/letsencrypt)
