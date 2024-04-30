To configure Gitea with OAuth2 settings for Azure Active Directory, you'll need to perform the following steps:

1. **Register Gitea as an Application in Azure AD**:
   - Log in to the Azure portal.
   - Go to Azure Active Directory > App registrations.
   - Click on "New registration" and fill in the required details, such as the name of the application and the redirect URI (typically `https://your-gitea-domain/user/oauth2/callback`).
   - Note down the Application (client) ID and Directory (tenant) ID generated for your application.

2. **Configure OAuth2 Settings in Gitea**:
   - Update your `docker-compose.yml` file to include environment variables for Azure AD OAuth2 settings:

```yaml
services:
  gitea:
    image: gitea/gitea:1.21.10-rootless
    environment:
      # Other environment variables...
      - OAUTH2_PROVIDER=azure
      - OAUTH2_CLIENT_ID=your_azure_client_id
      - OAUTH2_CLIENT_SECRET=your_azure_client_secret
      - OAUTH2_AUTH_URL=https://login.microsoftonline.com/your-directory-id/oauth2/v2.0/authorize
      - OAUTH2_TOKEN_URL=https://login.microsoftonline.com/your-directory-id/oauth2/v2.0/token
      - OAUTH2_USER_API_URL=https://graph.microsoft.com/v1.0/me
      - OAUTH2_SCOPE="openid email profile"
      - OAUTH2_EMAIL_ATTRIBUTE=email
      - OAUTH2_USERNAME_ATTRIBUTE=unique_name
    # Other configurations...
```

Replace `your_azure_client_id`, `your_azure_client_secret`, and `your-directory-id` with the respective values obtained during the Azure AD application registration process.

3. **Restart Gitea Container**:
   - After updating the `docker-compose.yml` file, restart your Gitea container for the changes to take effect:
   
   ```
   docker-compose restart gitea
   ```

4. **Test the OAuth2 Integration**:
   - Access your Gitea instance and navigate to the login page.
   - You should see an option to log in with Azure AD.
   - Click on the Azure AD login option and follow the prompts to authenticate using your Azure AD credentials.
   - Once authenticated, you should be logged in to Gitea using your Azure AD account.

By following these steps, you can configure Gitea to use Azure Active Directory for OAuth2 authentication, allowing users to sign in with their Azure AD credentials.