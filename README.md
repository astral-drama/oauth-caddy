# OAuth Caddy

OAuth Caddy provides a complete OAuth2 authentication solution for Caddy reverse proxy, supporting multiple providers including Google and GitHub.

## Features

- **Pre-configured Templates**: Ready-to-use Caddy and oauth2-proxy configurations
- **Multiple Providers**: Support for Google and GitHub OAuth
- **Easy Setup**: One-command configuration script
- **Systemd Integration**: Automatic service management
- **Template-based**: Environment-agnostic configuration files

## Quick Installation

```bash
# Download the latest release
wget https://github.com/astral-drama/oauth-caddy/releases/latest/download/oauth-caddy.deb

# Install the package
sudo dpkg -i oauth-caddy.deb

# Install dependencies if needed
sudo apt-get install -f
```

## Setup

### 1. Configure OAuth Applications

First, create OAuth applications with your providers:

**Google OAuth App:**
1. Go to [Google Cloud Console](https://console.developers.google.com/)
2. Create a new project or select existing
3. Enable Google+ API
4. Create OAuth 2.0 credentials
5. Add authorized redirect URI: `https://yourdomain.com/oauth2/callback`

**GitHub OAuth App:**
1. Go to [GitHub Developer Settings](https://github.com/settings/developers)
2. Create a new OAuth App
3. Set Authorization callback URL: `https://yourdomain.com/oauth2/callback`

### 2. Run Setup Script

```bash
sudo oauth-caddy-setup \
  --domain yourdomain.com \
  --email admin@yourdomain.com \
  --google-client-id "123.apps.googleusercontent.com" \
  --google-secret "GOCSPX-xyz" \
  --github-client-id "Ov23xyz" \
  --github-secret "abc123"
```

### 3. Configure Your Backend

Make sure your backend application is running on the specified port (default: 8080).

## Building from Source

```bash
# Clone the repository
git clone https://github.com/astral-drama/oauth-caddy.git
cd oauth-caddy

# Build the package
dpkg-deb --build oauth-caddy-1.0.0 oauth-caddy.deb

# Install locally
sudo dpkg -i oauth-caddy.deb
```

## Routes

Once configured, your application will have these routes:

| Route | Description |
|-------|-------------|
| `/login/google` | Redirect to Google OAuth |
| `/login/github` | Redirect to GitHub OAuth |
| `/oauth2/*` | OAuth2 callback and management |
| `/google/*` | Protected routes (Google auth required) |
| `/github/*` | Protected routes (GitHub auth required) |
| `/admin/*` | Admin routes (Google auth required) |
| `/*` | Public routes (no auth) |

## Service Management

OAuth2 proxy services are managed via systemd:

```bash
# Check status
sudo systemctl status oauth2-proxy-google
sudo systemctl status oauth2-proxy-github

# Restart services
sudo systemctl restart oauth2-proxy-google
sudo systemctl restart oauth2-proxy-github
sudo systemctl restart caddy
```

## Customization

### Backend Port

Change the backend port your application runs on:

```bash
oauth-caddy-setup --domain example.com --email admin@example.com --backend-port 3000
```

### Email Restrictions

Edit `/etc/oauth2-proxy/google.cfg` or `/etc/oauth2-proxy/github.cfg`:

```
# Restrict to specific domain
email_domains = ["yourdomain.com"]

# Allow any email
email_domains = ["*"]
```

## Security Best Practices

1. **Use HTTPS**: Always run in production with valid SSL certificates
2. **Secure Secrets**: Protect client secrets and cookie secrets
3. **Email Restrictions**: Limit access to specific domains when possible
4. **Regular Updates**: Keep oauth2-proxy and Caddy updated
5. **Monitor Access**: Review authentication logs regularly

## License

MIT License - See LICENSE file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.