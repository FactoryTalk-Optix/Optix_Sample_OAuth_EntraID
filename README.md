# OAuth2 Authentication with Microsoft Entra ID

![Review](https://img.shields.io/badge/Project%20Review-1.0-yellow) ![FTOptix](https://img.shields.io/badge/FTOptix%20Version-1.8.1.27-blue) ![Tested](https://img.shields.io/badge/Tested-Yes-brightgreen)

This project is an example of OAuth 2.0 authentication in FactoryTalk Optix using Microsoft Entra ID as the identity provider. It shows how to integrate a modern authentication mechanism into an industrial automation environment.

## Prerequisites

- FactoryTalk Optix (see the version badge above)
- A Microsoft Entra ID tenant where you can create an app registration

## Setup Instructions

1. **Install the CA certificate**

   The Web Presentation Engine (WPE) `localhost` certificate used by this project (`ProjectFiles/PKI/Own/Certs/localhost.pem`) is issued by the CA included in the project. The OAuth2 redirect URI is `https://localhost/oauth2callback`, so the browser must trust this CA.

   - Navigate to the `ProjectFiles/ca-cert` directory.
   - Locate the CA certificate file (`root_ca.crt`).
   - Install the CA certificate on your operating system:

     - **Windows**:
       - Open the certificate file.
       - Click "Install Certificate" and follow the prompts.

         > **Place the certificate in the Trusted Root Certification Authorities folder.**

       - Alternatively, from PowerShell as Administrator:

         ```powershell
         Import-Certificate -FilePath .\root_ca.crt `
           -CertStoreLocation Cert:\LocalMachine\Root
         ```

     - **Ubuntu**:
       - Copy the certificate file to `/usr/local/share/ca-certificates/`:

         ```sh
         sudo cp root_ca.crt /usr/local/share/ca-certificates/
         ```

       - Update the CA certificates:

         ```sh
         sudo update-ca-certificates
         ```

   > **Demo certificate:** install this CA only on test machines, and remove it when you finish the evaluation.

2. **Set up the Microsoft Entra ID app registration**

   In the [Microsoft Entra admin center](https://entra.microsoft.com), under **App registrations**, create (or open) an application and:
   - Add the redirect URI used by the project: `https://localhost/oauth2callback`.
   - Create a **Client Secret** and copy its value.
   - Under **Expose an API**, expose the application ID.
   - Set the manifest to token version 2.

   For the detailed procedure, see [How to configure Microsoft Entra ID for OAuth2 authentication in FactoryTalk Optix](https://support.rockwellautomation.com/app/answers/answer_view/a_id/1161160/loc/en_US).

3. **Configure the FactoryTalk Optix project**

   Open the project in FactoryTalk Optix and replace the placeholders in the project's **Authentication** settings with the values from your app registration:

   | Setting | Value |
   |---|---|
   | Issuer | `https://login.microsoftonline.com/<your Directory ID>/v2.0` |
   | Authorization path | `/<your Directory ID>/oauth2/v2.0/authorize` |
   | Token path | `/<your Directory ID>/oauth2/v2.0/token` |
   | JWKS path | `/<your Directory ID>/discovery/v2.0/keys` |
   | Additional scopes | `api://<your application id>/<your application expose name>` |
   | Client ID / Client Secret | From your app registration |

4. **Run the project**

   Start the project in emulation mode and sign in using your Entra ID account.
