# GitHub Repository Setup for TradrLab Widget

## Distribution Strategy Options

### Option 1: Public Repository (Recommended)
**Best for**: Maximum reach and easy integration

1. Create a **public** repository: `tradrlab-widget`
2. Anyone can clone/download the widget files
3. API key still required for functionality (protects your service)
4. Easiest for 3rd party developers

### Option 2: Private Repository with Access Control
**Best for**: Controlled distribution to partners

1. Create a **private** repository
2. Add specific users as collaborators
3. More control but requires managing access

### Option 3: GitHub Releases + CDN
**Best for**: Professional distribution

1. Use GitHub Releases for versioning
2. Host minified files on CDN
3. Developers use CDN links (no download needed)

## Setup Instructions

### 1. Create New Repository

```bash
# Navigate to the distribution folder
cd /mnt/c/repos/TradrLabOrg/tradrlab-widget-public

# Initialize git
git init

# Add all files
git add .
git commit -m "Initial release of TradrLab Widget v1.0.0"
```

### 2. Create Repository on GitHub

1. Go to https://github.com/organizations/YOUR_ORG/repositories/new
2. Name: `tradrlab-widget`
3. Description: "Embeddable AI-powered trading strategy chat widget"
4. Visibility: **Public** (recommended) or **Private**
5. Do NOT initialize with README (we have one)

### 3. Push to GitHub

```bash
# Add remote origin (replace YOUR_ORG with your organization)
git remote add origin https://github.com/YOUR_ORG/tradrlab-widget.git

# Push to main branch
git branch -M main
git push -u origin main
```

### 4. Configure Repository Settings

Go to Settings → Options:
- ✅ Disable Wikis (not needed)
- ✅ Disable Issues (or keep for bug reports)
- ✅ Disable Projects (not needed)

### 5. Add Topics for Discoverability

Add topics: `tradrlab`, `trading`, `widget`, `javascript`, `ai`, `chat`

### 6. Create First Release

1. Go to Releases → Create new release
2. Tag: `v1.0.0`
3. Title: "TradrLab Widget v1.0.0"
4. Attach: `dist/widget-core.min.js`
5. Description:
```markdown
## TradrLab Widget v1.0.0

First public release of the TradrLab trading strategy chat widget.

### Installation
Download `widget-core.min.js` or use CDN:
```html
<script src="https://github.com/YOUR_ORG/tradrlab-widget/releases/download/v1.0.0/widget-core.min.js"></script>
```

### Documentation
- [Integration Guide](INTEGRATION_GUIDE.md)
- [Live Example](integration-example.html)
```

## CDN Setup (Optional but Recommended)

### Using jsDelivr (Free CDN)
Once your repo is public, files are automatically available via jsDelivr:

```html
<!-- Latest version -->
<script src="https://cdn.jsdelivr.net/gh/YOUR_ORG/tradrlab-widget@latest/dist/widget-core.min.js"></script>

<!-- Specific version -->
<script src="https://cdn.jsdelivr.net/gh/YOUR_ORG/tradrlab-widget@1.0.0/dist/widget-core.min.js"></script>
```

### Using GitHub Pages
1. Go to Settings → Pages
2. Source: Deploy from branch
3. Branch: main, folder: / (root)
4. Files available at: `https://YOUR_ORG.github.io/tradrlab-widget/`

## Access Control (If Private Repository)

### Add Collaborators
1. Settings → Manage access → Add people
2. Add each 3rd party developer by username
3. Permission: **Read** (can clone/download only)

### Using Deploy Keys
For automated systems:
1. Settings → Deploy keys
2. Add SSH key with read-only access

### Personal Access Tokens
For 3rd parties to access via API:
1. They create PAT in their GitHub settings
2. Clone with: `git clone https://TOKEN@github.com/YOUR_ORG/tradrlab-widget.git`

## Sharing with 3rd Parties

### For Public Repository
Share this URL: `https://github.com/YOUR_ORG/tradrlab-widget`

Instructions for them:
```bash
# Clone
git clone https://github.com/YOUR_ORG/tradrlab-widget.git

# Or download directly
curl -L https://github.com/YOUR_ORG/tradrlab-widget/releases/latest/download/widget-core.min.js -o widget-core.min.js
```

### For Private Repository
1. Add them as collaborator first
2. They clone with credentials:
```bash
git clone https://github.com/YOUR_ORG/tradrlab-widget.git
```

## Security Considerations

✅ **API Key Protection**: Widget requires API key (not in repo)
✅ **No Source Code**: Only distribute minified production build
✅ **Version Control**: Use tags/releases for versions
✅ **Access Logs**: GitHub tracks all access/downloads

## Recommended: API Endpoint Configuration

Since you want to test with dev API first, update the example to use dev endpoint:

```javascript
window.TradrLabWidget.globals({
    apikey: 'YOUR_API_KEY',
    apiBaseUrl: 'https://dev-api.tradrlab.com'  // Dev environment
    // apiBaseUrl: 'https://api.tradrlab.com'   // Production (later)
});
```

## Support Documentation

Create these for your users:
- `CHANGELOG.md` - Version history
- `TROUBLESHOOTING.md` - Common issues
- `API_KEYS.md` - How to obtain API keys

## Next Steps

1. Choose distribution strategy (public recommended)
2. Create GitHub repository
3. Push code
4. Create first release
5. Share with 3rd party developers
6. Monitor usage via API key analytics