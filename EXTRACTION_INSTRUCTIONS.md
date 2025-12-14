# Extracting Thespis Connected App Metadata

Since Connected Apps cannot be retrieved via the standard Metadata API, use one of these methods:

## Method 1: VS Code Salesforce Extension (Recommended)

1. Open this folder in VS Code
2. Ensure Salesforce Extension Pack is installed
3. Authorize your org: `sf org login web --alias storm-org` (if not already done)
4. In VS Code, open the Command Palette (Cmd+Shift+P)
5. Run: "SFDX: Retrieve Source from Org"
6. Select "ConnectedApp" as the metadata type
7. Select "Thespis" as the specific component
8. The metadata should be retrieved to `force-app/main/default/connectedApps/Thespis.connectedApp-meta.xml`

## Method 2: Manual Configuration

If VS Code doesn't work, you can manually view the Connected App in Setup and ensure the metadata file matches:

1. Go to Setup > Apps > App Manager
2. Find "Thespis" Connected App
3. Click dropdown → View
4. Verify these settings match the XML file:
   - OAuth Scopes (should include: api, refresh_token, offline_access, openid, id, profile, email, chatbot_api, sfap_api)
   - Callback URL (will be org-specific, users update after deployment)
   - OAuth Policies
   - Contact Email
   - Description

## Method 3: Update Template Manually

The current template in the repository has the correct structure. You can:
1. Update the `contactEmail` field with your actual email
2. Update the `description` if needed
3. The `callbackUrl` and `consumerKey` will be generated/updated when users deploy to their orgs

After updating, commit and push:
```bash
git add force-app/main/default/connectedApps/Thespis.connectedApp-meta.xml
git commit -m "Update Connected App metadata with actual org configuration"
git push
```
