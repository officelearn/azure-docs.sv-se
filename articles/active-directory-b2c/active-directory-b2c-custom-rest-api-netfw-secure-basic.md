---
title: 'Azure Active Directory B2C: Skydda din RESTful-tjänster med hjälp av grundläggande HTTP-autentisering'
description: Skydda din anpassade REST API anspråk utbyte i din Azure AD B2C med hjälp av grundläggande HTTP-autentisering
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.openlocfilehash: 90bbcc865f13a1d048ea12fdccd3ca848cf362ba
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Skydda din RESTful-tjänster med hjälp av grundläggande HTTP-autentisering
I en [relaterade Azure AD B2C-artikeln](active-directory-b2c-custom-rest-api-netfw.md), du skapar en RESTful-tjänst (webb-API) som kan integreras med Azure Active Directory B2C (Azure AD B2C) användaren resor utan autentisering. 

I den här artikeln får du lägger till grundläggande HTTP-autentisering RESTful-tjänst så att endast verifieras användare, inklusive B2C, kan komma åt din API. Grundläggande HTTP-autentisering anger du användarens autentiseringsuppgifter (app-ID och app secret) i en anpassad princip. 

Mer information finns i [grundläggande autentisering i ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [integrera REST API-anspråk utbyte i din Azure AD B2C användaren resa](active-directory-b2c-custom-rest-api-netfw.md) artikel.

## <a name="step-1-add-authentication-support"></a>Steg 1: Lägg till stöd för autentisering

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Steg 1.1: Lägg till tillämpningsinställningar web.config-filen för ditt projekt
1. Öppna Visual Studio-projekt som du skapade tidigare. 

2. Lägg till följande programinställningarna i web.config-filen under den `appSettings` element:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Skapa ett lösenord och ange sedan den `WebApp:ClientSecret` värde.

    Kör följande PowerShell-kod för att generera ett komplext lösenord. Du kan använda ett godtyckligt värde.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Steg 1.2: Installera OWIN-bibliotek
Om du vill börja lägga till OWIN mellanprogram NuGet-paket i projektet med hjälp av Visual Studio Package Manager-konsolen:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Steg 1.3: Lägg till en autentisering mellanprogram klass
Lägg till den `ClientAuthMiddleware.cs` klassen den *App_Start* mapp. Gör så här:

1. Högerklicka på den *App_Start* mapp, Välj **Lägg till**, och välj sedan **klassen**.

   ![Lägg till ClientAuthMiddleware.cs klass i mappen App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. I den **namn** skriver **ClientAuthMiddleware.cs**.

   ![Skapa ny klass C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Öppna den *App_Start\ClientAuthMiddleware.cs* filen och ersätter filen innehåll med följande kod:

    ```csharp
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Steg 1.4: Lägg till en OWIN-startklass
Lägg till en OWIN-startklass med namnet `Startup.cs` -API: et. Gör så här:
1. Högerklicka på projektet, Välj **Lägg till** > **nytt objekt**, och sök sedan efter **OWIN**.

   ![Lägga till en OWIN-startklass](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Öppna den *Startup.cs* filen och ersätter filen innehåll med följande kod:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Steg 1.5: Skydda identitet API-klass
Öppna Controllers\IdentityController.cs och Lägg till den `[Authorize]` tagg i kontrollantklassen. Den här taggen begränsar åtkomsten till att kontrollanten kan användare som uppfyller kraven på tillstånd.

![Lägg till taggen auktorisera till styrenhet](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Steg 2: Publicera till Azure
Om du vill publicera ditt projekt i Solution Explorer högerklickar du på den **Contoso.AADB2C.API** projektet och välj sedan **publicera**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Steg 3: Lägg till app RESTful-tjänster hemlighet ID och app i Azure AD B2C
När tjänsten RESTful skyddas av klient-ID (användarnamn) och hemlighet, måste du lagra autentiseringsuppgifterna i din Azure AD B2C-klient. Den anpassade principen ger autentiseringsuppgifter när du anropar den RESTful-tjänster. 

### <a name="step-31-add-a-restful-services-client-id"></a>Steg 3.1: Lägg till en RESTful-tjänster klient-ID
1. Välj i din Azure AD B2C-klient **B2C inställningar** > **identitet upplevelse Framework**.


2. Välj **princip nycklar** att visa de nycklar som är tillgängliga i din klient.

3. Välj **Lägg till**.

4. För **alternativ**väljer **manuell**.

5. För **namn**, typen **B2cRestClientId**.  
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I den **hemlighet** ange app-ID som du angav tidigare.

7. För **nyckelanvändning**väljer **hemlighet**.

8. Välj **Skapa**.

9. Bekräfta att du har skapat den `B2C_1A_B2cRestClientId` nyckel.

### <a name="step-32-add-a-restful-services-client-secret"></a>Steg 3.2: Lägg till en klienthemlighet RESTful-tjänster
1. Välj i din Azure AD B2C-klient **B2C inställningar** > **identitet upplevelse Framework**.

2. Välj **princip nycklar** att visa nycklarna som är tillgängliga i din klient.

3. Välj **Lägg till**.

4. För **alternativ**väljer **manuell**.

5. För **namn**, typen **B2cRestClientSecret**.  
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I den **hemlighet** ange app-hemlighet som du angav tidigare.

7. För **nyckelanvändning**väljer **hemlighet**.

8. Välj **Skapa**.

9. Bekräfta att du har skapat den `B2C_1A_B2cRestClientSecret` nyckel.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Steg 4: Ändra tekniska profilen för att stödja grundläggande autentisering i din princip för tillägg
1. Öppna filen för princip för tillägg (TrustFrameworkExtensions.xml) i arbetskatalogen.

2. Sök efter den `<TechnicalProfile>` nod som innehåller `Id="REST-API-SignUp"`.

3. Leta upp den `<Metadata>` element.

4. Ändra den *AuthenticationType* till *grundläggande*, enligt följande:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Omedelbart efter avslutningen `<Metadata>` element, Lägg till följande XML-kodstycke: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    När du har lagt till kodavsnittet tekniska profilen bör se ut som följande XML-kod:
    
    ![Lägga till XML-element för grundläggande autentisering](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Steg 5: Överför principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och sedan öppna **Azure AD B2C**.

2. Välj **identitet upplevelse Framework**.

3. Öppna **alla principer**.

4. Välj **överföra princip**.

5. Välj den **skriva över principen om den finns** kryssrutan.

6. Överför den *TrustFrameworkExtensions.xml* filen och sedan kontrollera att den har klarat valideringen.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Steg 6: Testa den anpassade principen genom att använda Kör nu
1. Öppna **Azure AD B2C inställningar**, och välj sedan **identitet upplevelse Framework**.

    >[!NOTE]
    >Kör nu kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.

2. Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du överfört och välj sedan **kör nu**.

3. Testa processen genom att skriva **Test** i den **Förnamn** rutan.  
    Azure AD B2C visar ett felmeddelande visas överst i fönstret.

    ![Testa din identitet API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. I den **Förnamn** Skriv ett namn (andra än ”Test”).  
    Azure AD B2C registrerar sig användaren och skickar sedan en förmåner numret för ditt program. Anteckna numret i det här exemplet:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Hämta fullständig principfiler och kod
* När du har slutfört den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Nästa steg
* [Använda klientcertifikat för att skydda din RESTful-API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

