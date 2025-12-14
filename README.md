# Thespis Connected App

This repository contains the Salesforce Connected App metadata for **Thespis**, the Agentforce demonstration platform. The Connected App enables both OAuth-based org discovery and Agent API access through a single configuration.

## Overview

The Thespis Connected App is a unified Connected App that provides:
- **OAuth Org Discovery**: Automatic discovery of Salesforce org details (org ID, my domain, org name)
- **Agent API Access**: Direct integration with Salesforce Agentforce APIs for AI agent interactions

This single Connected App eliminates the need for separate OAuth and Agent API configurations, simplifying the setup process.

## Prerequisites

Before you begin, ensure you have:

1. **Salesforce CLI** installed and configured
   - Download: https://developer.salesforce.com/tools/salesforcecli
   - Verify installation: `sf --version`

2. **Access to a Salesforce org** where you want to deploy the Connected App
   - Org must have Einstein Agent Builder enabled (for Agent API functionality)
   - You must have System Administrator or equivalent permissions

3. **Git** (for cloning this repository)

## Quick Start

### Option 1: Deploy from This Repository (Recommended)

1. **Clone this repository**:
   ```bash
   git clone https://github.com/your-org/agentforce-thespis-sfdc-app.git
   cd agentforce-thespis-sfdc-app
   ```

2. **Authenticate to your Salesforce org**:
   ```bash
   sf org login web --alias thespis-org
   ```

3. **Deploy the Connected App**:
   ```bash
   sf project deploy start --target-org thespis-org
   ```

4. **Update the Callback URL**:
   - Go to **Setup** > **Apps** > **App Manager**
   - Find **Thespis** Connected App
   - Click the dropdown arrow → **View**
   - Click **Edit Policies**
   - Update the **Callback URL** to match your Thespis application URL:
     - Production: `https://your-app.herokuapp.com/api/salesforce/oauth/callback`
     - Local: `http://localhost:5000/api/salesforce/oauth/callback`
   - Click **Save**

5. **Get Your Credentials**:
   - In the Connected App detail page, click **Manage Consumer Details**
   - Click **Continue** on the security warning
   - Copy the **Consumer Key** and **Consumer Secret**
   - ⚠️ **Important**: Copy the Consumer Secret immediately - you'll only see it once!

6. **Configure in Thespis**:
   - Open your Thespis application
   - Navigate to **Get Started/Settings**
   - Enter the Consumer Key and Consumer Secret
   - Click **Connect with Salesforce**

### Option 2: Extract from Your Existing Org

If you already have the Thespis Connected App configured in a Salesforce org and want to extract it:

1. **Authenticate to your source org**:
   ```bash
   sf org login web --alias source-org
   ```

2. **Retrieve the Connected App metadata**:
   ```bash
   sf project retrieve start --metadata ConnectedApp:Thespis --target-org source-org
   ```

3. **Verify the metadata**:
   - Check that `force-app/main/default/connectedApps/Thespis.connectedApp-meta.xml` exists
   - Review the file to ensure all OAuth scopes are included

4. **Deploy to a new org** (if needed):
   ```bash
   sf org login web --alias target-org
   sf project deploy start --target-org target-org
   ```

## Configuration Details

### Required OAuth Scopes

The Connected App must include the following OAuth scopes:

**Org Discovery Scopes:**
- `api` - Full access
- `refresh_token` - Perform requests at any time
- `offline_access` - Perform requests at any time
- `openid` - OpenID Connect support
- `id` - Access identity URL
- `profile` - Access basic profile information
- `email` - Access email address

**Agent API Scopes:**
- `chatbot_api` - Access chatbot services (required for Agentforce API)
- `sfap_api` - Access the Salesforce API Platform (required for Agentforce API)

### OAuth Settings

When creating or configuring the Connected App manually, ensure:

- ✅ **Enable OAuth Settings**: Checked
- ❌ **Require Proof Key for Code Exchange (PKCE)**: Unchecked
- ❌ **Require Secret for Web Server Flow**: Unchecked
- ✅ **Callback URL**: Must match your Thespis application URL exactly

### Callback URL Format

The callback URL must match your Thespis deployment:

- **Production**: `https://your-app.herokuapp.com/api/salesforce/oauth/callback`
- **Local Development**: `http://localhost:5000/api/salesforce/oauth/callback`

⚠️ **Important**: The URL must match exactly, including:
- Protocol (`https://` or `http://`)
- Domain/hostname
- Path (`/api/salesforce/oauth/callback`)
- No trailing slash

## Getting Your Credentials

After deploying the Connected App:

1. Navigate to **Setup** > **Apps** > **App Manager**
2. Find **Thespis** in the list
3. Click the dropdown arrow (⋮) → **View**
4. Click **Manage Consumer Details**
5. Click **Continue** on the security warning
6. Copy the **Consumer Key** (also called Client ID)
7. Copy the **Consumer Secret** (also called Client Secret)
   - ⚠️ **You can only see the Consumer Secret once!** Copy it immediately.

## Integration with Thespis

Once you have your Consumer Key and Secret:

1. Open your Thespis application
2. Navigate to **Get Started/Settings** (or **Settings** in the navigation)
3. In **Step 1: Connect to Salesforce**:
   - Enter your **OAuth Consumer Key**
   - Enter your **OAuth Consumer Secret**
   - Click **Connect with Salesforce**
4. Complete the OAuth flow in the popup window
5. Your org details will be automatically discovered and saved
6. Continue with the remaining setup steps

## Troubleshooting

### "OAuth Error: OAUTH_APPROVAL_ERROR_GENERIC"

This error typically indicates a configuration issue:

1. **Verify OAuth Scopes**: Ensure all required scopes are selected (both org discovery AND Agent API scopes)
2. **Check Checkboxes**: Ensure "Require PKCE" and "Require Secret for Web Server Flow" are **unchecked**
3. **Callback URL**: Verify the callback URL matches exactly (no trailing slash, correct protocol)
4. **Connected App Status**: Ensure the Connected App is active and not deleted

### "Invalid Consumer Key" or "Invalid Consumer Secret"

1. **Verify Credentials**: Double-check that you copied the Consumer Key and Secret correctly
2. **Check for Spaces**: Ensure there are no leading/trailing spaces
3. **Regenerate Secret**: If you lost the Consumer Secret, you'll need to reset it:
   - Go to Connected App detail page
   - Click **Manage Consumer Details**
   - Click **Reset Consumer Secret**
   - Copy the new secret immediately

### "Callback URL Mismatch"

1. **Exact Match**: The callback URL in Salesforce must match exactly what Thespis is using
2. **Check Thespis URL**: In Thespis settings, verify the displayed callback URL
3. **Update in Salesforce**: Update the Connected App's callback URL to match exactly

### Deployment Fails

1. **Check Permissions**: Ensure you have System Administrator or equivalent permissions
2. **Verify API Version**: Check that your org supports API version 62.0 or later
3. **Review Errors**: Check the deployment status for specific error messages:
   ```bash
   sf project deploy report --target-org thespis-org
   ```

### No Agents Found After Connection

1. **Complete Step 2**: Ensure you've entered the Agent API Consumer Key and Secret
2. **Verify Agent API Connected App**: The Connected App must be connected to your Agent in Salesforce
3. **Check Agent Status**: Ensure your Agent is active in Agent Builder

## File Structure

```
agentforce-thespis-sfdc-app/
├── README.md                          # This file
├── sfdx-project.json                  # Salesforce DX project configuration
├── .gitignore                         # Git ignore patterns
├── .forceignore                       # Salesforce deployment ignore patterns
└── force-app/
    └── main/
        └── default/
            └── connectedApps/
                └── Thespis.connectedApp-meta.xml  # Connected App metadata
```

## Additional Resources

- [Thespis Documentation](https://github.com/your-org/thespis) - Main Thespis application repository
- [Salesforce CLI Documentation](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [Agentforce Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.agentforce_dev.meta/agentforce_dev/)
- [Connected Apps Documentation](https://help.salesforce.com/s/articleView?id=sf.connected_app_overview.htm)

## Support

For issues or questions:
- Open an issue in this repository
- Contact the Thespis development team
- Refer to the [Thespis main repository](https://github.com/your-org/thespis) for application-specific support

## License

[Specify your license here]

