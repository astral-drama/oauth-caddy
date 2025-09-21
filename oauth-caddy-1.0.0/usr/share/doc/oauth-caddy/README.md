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
# Download and install
wget https://github.com/astral-drama/oauth-caddy/releases/latest/download/oauth-caddy.deb
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

## Configuration Files

After installation, template files are available at:

- `/etc/caddy/Caddyfile.template` - Caddy reverse proxy configuration
- `/etc/oauth2-proxy/google.cfg.template` - Google OAuth configuration
- `/etc/oauth2-proxy/github.cfg.template` - GitHub OAuth configuration

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

# View logs
sudo journalctl -u oauth2-proxy-google -f
sudo journalctl -u oauth2-proxy-github -f
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

### GitHub Organization/Team

Restrict GitHub access to specific org/team in `/etc/oauth2-proxy/github.cfg`:

```
github_org = "your-org"
github_team = "your-team"
```

## Troubleshooting

### Common Issues

1. **OAuth redirect mismatch**: Ensure redirect URLs match exactly in your OAuth app settings
2. **502 errors**: Check that oauth2-proxy services are running and backend is accessible
3. **Cookie errors**: Verify cookie_secure setting matches your HTTPS setup

### Debug Commands

```bash
# Test oauth2-proxy config
sudo oauth2-proxy --config=/etc/oauth2-proxy/google.cfg --test

# Check Caddy config
sudo caddy validate --config /etc/caddy/Caddyfile

# View service logs
sudo journalctl -u caddy -f
sudo journalctl -u oauth2-proxy-google -f
```

## Security Best Practices

1. **Use HTTPS**: Always run in production with valid SSL certificates
2. **Secure Secrets**: Protect client secrets and cookie secrets
3. **Email Restrictions**: Limit access to specific domains when possible
4. **Regular Updates**: Keep oauth2-proxy and Caddy updated
5. **Monitor Access**: Review authentication logs regularly

## Support

For issues and updates, visit: https://github.com/astral-drama/oauth-caddy

## License

MIT License - See LICENSE file for details.