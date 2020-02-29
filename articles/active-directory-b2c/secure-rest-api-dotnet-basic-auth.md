---
title: Skydda en RESTful-tjänst med hjälp av HTTP Basic-autentisering
titleSuffix: Azure AD B2C
description: Skydda dina anpassade REST API Claims-utbyten i Azure AD B2C med hjälp av HTTP Basic-autentisering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 505d92e33606dac327778cae179df44efbfdf853
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183864"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Skydda dina RESTful-tjänster med hjälp av HTTP Basic-autentisering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I en [relaterad Azure AD B2C-artikel](rest-api-claims-exchange-dotnet.md)skapar du en RESTful-tjänst (webb-API) som integreras med Azure Active Directory B2C (Azure AD B2C) användar resa utan autentisering.

I den här artikeln lägger du till HTTP Basic-autentisering till din RESTful-tjänst så att endast verifierade användare, inklusive B2C, har åtkomst till ditt API. Med HTTP Basic-autentisering anger du användarautentiseringsuppgifter (app-ID och app Secret) i den anpassade principen.

Mer information finns [i grundläggande autentisering i ASP.net Web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i artikeln [integrera REST API anspråk i Azure AD B2C användar resa](rest-api-claims-exchange-dotnet.md) .

## <a name="step-1-add-authentication-support"></a>Steg 1: Lägg till stöd för autentisering

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Steg 1,1: Lägg till program inställningar till projektets Web. config-fil

1. Öppna Visual Studio-projektet som du skapade tidigare.

2. Lägg till följande program inställningar till filen Web. config under `appSettings`-elementet:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Skapa ett lösen ord och ange sedan `WebApp:ClientSecret`-värdet.

    Kör följande PowerShell-kod om du vill generera ett komplext lösen ord. Du kan använda valfritt godtyckligt värde.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Steg 1,2: installera OWIN-bibliotek

Börja genom att lägga till NuGet-paketen OWIN mellan program i projektet med hjälp av Visual Studio Package Manager-konsolen:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Steg 1,3: Lägg till en klass för mellanliggande autentisering

Lägg till `ClientAuthMiddleware.cs`-klassen under mappen *App_Start* . Gör så här:

1. Högerklicka på mappen *App_Start* , Välj **Lägg till**och välj sedan **klass**.

   ![Lägg till klassen ClientAuthMiddleware.cs i mappen App_Start](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Skriv **ClientAuthMiddleware.cs**i rutan **namn** .

   ![Skapa en ny C# klass i dialog rutan Lägg till nytt objekt i Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Öppna filen *App_Start \clientauthmiddleware.cs* och ersätt fil innehållet med följande kod:

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

### <a name="step-14-add-an-owin-startup-class"></a>Steg 1,4: lägga till en OWIN start klass

Lägg till en OWIN-startklass med namnet `Startup.cs` till API: et. Gör så här:
1. Högerklicka på projektet, Välj **Lägg till** > **nytt objekt**och Sök sedan efter **OWIN**.

   ![Skapa OWIN start klass i dialog rutan Lägg till nytt objekt i Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Öppna filen *startup.cs* och ersätt fil innehållet med följande kod:

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

### <a name="step-15-protect-the-identity-api-class"></a>Steg 1,5: skydda identitets-API-klassen

Öppna Controllers\IdentityController.cs och Lägg till taggen `[Authorize]` i kontroll enhets klassen. Den här taggen begränsar åtkomsten till kontrollanten till användare som uppfyller auktorisations kravet.

![Lägg till auktorisera-taggen i kontrollanten](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Steg 2: publicera till Azure

Om du vill publicera projektet högerklickar du på projektet **contoso. AADB2C. API** i Solution Explorer och väljer sedan **publicera**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Steg 3: Lägg till RESTful Services-appens-ID och appens hemlighet till Azure AD B2C

När din RESTful-tjänst skyddas av klient-ID: t (username) och hemligheten måste du lagra autentiseringsuppgifterna i Azure AD B2C-klienten. Den anpassade principen tillhandahåller autentiseringsuppgifterna när den anropar dina RESTful-tjänster.

### <a name="step-31-add-a-restful-services-client-id"></a>Steg 3,1: Lägg till ett klient-ID för RESTful Services

1. I Azure AD B2C klient väljer du **B2C inställningar** > **Identity Experience Framework**.


2. Välj **princip nycklar** för att visa de nycklar som är tillgängliga i din klient organisation.

3. Välj **Lägg till**.

4. För **alternativ**väljer du **manuell**.

5. I **namn**skriver du **B2cRestClientId**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I rutan **hemlighet** anger du det app-ID som du definierade tidigare.

7. För **nyckel användning**väljer du **signatur**.

8. Välj **Skapa**.

9. Bekräfta att du har skapat den `B2C_1A_B2cRestClientId` nyckeln.

### <a name="step-32-add-a-restful-services-client-secret"></a>Steg 3,2: Lägg till en klient hemlighet för RESTful Services

1. I Azure AD B2C klient väljer du **B2C inställningar** > **Identity Experience Framework**.

2. Välj **princip nycklar** för att visa de nycklar som är tillgängliga i din klient organisation.

3. Välj **Lägg till**.

4. För **alternativ**väljer du **manuell**.

5. I **namn**skriver du **B2cRestClientSecret**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I rutan **hemlighet** anger du appens hemlighet som du definierade tidigare.

7. För **nyckel användning**väljer du **signatur**.

8. Välj **Skapa**.

9. Bekräfta att du har skapat den `B2C_1A_B2cRestClientSecret` nyckeln.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Steg 4: ändra den tekniska profilen så att den stöder grundläggande autentisering i principen för tillägg

1. Öppna tilläggs princip filen (TrustFrameworkExtensions. xml) i arbets katalogen.

2. Sök efter `<TechnicalProfile>`-noden som innehåller `Id="REST-API-SignUp"`.

3. Leta upp `<Metadata>`-elementet.

4. Ändra *AuthenticationType* till *Basic*enligt följande:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Lägg till följande XML-kodfragment direkt efter stängnings `<Metadata>` element:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    När du har lagt till kodfragmentet bör din tekniska profil se ut som följande XML-kod:

    ![Lägg till XML-element för grundläggande autentisering i TechnicalProfile](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Steg 5: Ladda upp principen till din klient organisation

1. I [Azure Portal](https://portal.azure.com)väljer du ikonen **katalog + prenumeration** i portalens verktygsfält och väljer sedan den katalog som innehåller Azure AD B2C-klienten.

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.

1. Välj **ramverk för identitets upplevelse**.

1. Öppna **alla principer**.

1. Välj **Ladda upp princip**.

1. Markera kryss rutan **Skriv över principen om den finns** .

1. Ladda upp filen *TrustFrameworkExtensions. XML* och kontrol lera att den klarar verifieringen.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Steg 6: testa den anpassade principen med hjälp av kör nu

1. Öppna **Azure AD B2C inställningar**och välj sedan **Identity Experience Framework**.

    >[!NOTE]
    >Körning kräver nu att minst ett program är förregistrerade på klienten. Information om hur du registrerar program finns i artikeln Azure AD B2C [komma igång](tutorial-create-tenant.md) eller i [program registrering](tutorial-register-applications.md) .

2. Öppna **B2C_1A_signup_signin**, den förlitande parten (RP) anpassad princip som du överförde och välj sedan **Kör nu**.

3. Testa processen genom att skriva **test** i rutan **namn** .
    Azure AD B2C visar ett fel meddelande överst i fönstret.

    ![Testa det angivna namnet på ingångs verifiering i ditt identitets-API](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-test.png)

4. I rutan **namn** anger du ett namn (annat än "test").
    Azure AD B2C registrerar användaren och skickar sedan ett förmåns nummer till ditt program. Observera talet i det här exemplet:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Valfritt Ladda ned fullständiga principfiler och kod

* När du har slutfört guiden [komma igång med anpassade principer](custom-policy-get-started.md) rekommenderar vi att du skapar ditt scenario genom att använda dina egna anpassade principfiler. Vi har angett [exempel på principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)för din referens.
* Du kan ladda ned den fullständiga koden från [exempel Visual Studio-lösningen för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Nästa steg

* [Använda klient certifikat för att skydda ditt RESTful-API](secure-rest-api-dotnet-certificate-auth.md)
