---
title: 'Konfigurera daemon-appar som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er (app-konfiguration)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/08/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 2e7a0657949a2addbf895666682feee7fd0eb595
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033367"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app som anropar webb-API: er – kod konfiguration

Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er.

## <a name="msal-libraries-that-support-daemon-apps"></a>MSAL-bibliotek som stöder daemon-appar

Dessa Microsoft-bibliotek stöder daemon-appar:

  MSAL-bibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | .NET Framework-och .NET Core-plattformarna stöds för att skapa daemon-program. (UWP, Xamarin. iOS och Xamarin. Android stöds inte eftersom dessa plattformar används för att bygga offentliga klient program.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Stöd för daemon-program i python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Stöd för daemon-program i Java.

## <a name="configure-the-authority"></a>Konfigurera utfärdaren

Daemon-program använder program behörigheter i stället för delegerade behörigheter. Deras konto typer som stöds får inte vara ett konto i någon organisations katalog eller någon personlig Microsoft-konto (till exempel Skype, Xbox, Outlook.com). Det finns ingen innehavaradministratör för att bevilja medgivande till ett daemon-program för ett personligt Microsoft-konto. Du måste välja *konton i min organisation* eller *konton i alla organisationer*.

Den auktoritet som anges i program konfigurationen ska därför vara klient organisation (som anger ett klient-ID eller ett domän namn som är kopplat till din organisation).

Om du är en ISV och vill tillhandahålla ett verktyg för flera innehavare kan du använda `organizations` . Men tänk på att du även måste förklara för kunderna hur de ska bevilja administrativt medgivande. Mer information finns i [begära medgivande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). Det finns också en begränsning i MSAL: `organizations` tillåts bara när klientautentiseringsuppgifterna är en program hemlighet (inte ett certifikat).

## <a name="configure-and-instantiate-the-application"></a>Konfigurera och instansiera programmet

I MSAL-bibliotek skickas klientens autentiseringsuppgifter (hemlighet eller certifikat) som en parameter för den konfidentiella klient program konstruktionen.

> [!IMPORTANT]
> Även om ditt program är ett konsol program som körs som en tjänst, om det är ett daemon-program, måste det vara ett konfidentiellt klient program.

### <a name="configuration-file"></a>Konfigurationsfil

Konfigurations filen definierar:

- Utfärdaren eller moln instansen och klient-ID: t.
- Det klient-ID som du fick från program registreringen.
- Antingen en klient hemlighet eller ett certifikat.

> [!NOTE]
> .Net-kodfragmenten i resten av artikel referensen [config](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs) från exemplet [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.jspå](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) från daemon-exemplet för [.net Core Console](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Du anger antingen en `ClientSecret` eller en `CertificateName` . De här inställningarna är exklusiva.

# <a name="python"></a>[Python](#tab/python)

När du skapar en konfidentiell klient med klient hemligheter är [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) i konfigurations filen i [python-bakgrunds](https://github.com/Azure-Samples/ms-identity-python-daemon) exemplet följande:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

När du skapar en konfidentiell klient med certifikat, är [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) i konfigurations filen i [python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -exemplet följande:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Instansiera MSAL-programmet

Om du vill instansiera MSAL-programmet måste du lägga till, referera eller importera MSAL-paketet (beroende på språket).

Konstruktion skiljer sig beroende på om du använder klient hemligheter eller certifikat (eller som ett avancerat scenario, signerade intyg).

#### <a name="reference-the-package"></a>Referera till paketet

Referera till MSAL-paketet i program koden.

# <a name="net"></a>[.NET](#tab/dotnet)

Lägg till [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-paketet i ditt program.
I MSAL.NET representeras det konfidentiella klient programmet av `IConfidentialClientApplication` gränssnittet.
Använd namn området MSAL.NET i käll koden.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Instansiera det konfidentiella klient programmet med en klient hemlighet

Här är koden för att instansiera det konfidentiella klient programmet med en klient hemlighet:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Instansiera det konfidentiella klient programmet med ett klient certifikat

Här är koden för att bygga ett program med ett certifikat:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

I MSAL Java finns det två byggprogram för att instansiera det konfidentiella klient programmet med certifikat:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

eller

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Avancerat scenario: instansiera det konfidentiella klient programmet med klient kontroll

# <a name="net"></a>[.NET](#tab/dotnet)

I stället för en klient hemlighet eller ett certifikat kan det konfidentiella klient programmet också bevisa sin identitet genom att använda klientens kontroll.

MSAL.NET har två metoder för att tillhandahålla signerade kontroller till appen konfidentiell klient:

- `.WithClientAssertion()`
- `.WithClientClaims()`

När du använder `WithClientAssertion` måste du ange ett signerat JWT. Det här avancerade scenariot beskrivs i [klient kontroll](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

När du använder `WithClientClaims` kommer MSAL.net att skapa en signerad kontroll som innehåller de anspråk som förväntas av Azure AD, plus ytterligare klient anspråk som du vill skicka.
Den här koden visar hur du gör:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Mer information finns i [klient kontroll](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

I MSAL python kan du ange klient anspråk genom att använda anspråken som ska signeras av den `ConfidentialClientApplication` privata nyckeln.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Mer information finns i referens dokumentationen för MSAL python för [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Nästa steg

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
