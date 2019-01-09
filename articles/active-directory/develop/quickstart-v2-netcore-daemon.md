---
title: Azure AD v2 .NET Core-daemon | Microsoft Docs
description: Lär dig hur en .NET Core-process kan hämta en åtkomsttoken och anropa ett API skyddat av en Azure Active Directory v2.0-slutpunkt med hjälp av appens egen identitet
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/28/2018
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3e5e364e9c3327e9d666a9a3096573267d0e1983
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727616"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-apps-identity"></a>Snabbstart: Hämta en token och anropa Microsoft Graph API från en konsolapp med hjälp av appens identitet

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

I den här snabbstarten lär dig hur du skriver en .NET Core-app som kan hämta en åtkomsttoken med hjälp av appens egen identitet och sedan anropa Microsoft Graph API för att visa en [lista över användare](https://docs.microsoft.com/graph/api/user-list) i katalogen. Det här scenariot är användbart för situationer där ett fjärradministrerat, obevakat jobb eller en Windows-tjänst måste köras med en programidentitet, istället för en användares identitet.

![Så fungerar den exempelapp som genereras med den här snabbstarten](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten kräver [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen

> [!div renderon="docs" class="sxs-lookup"]
>
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure-portalen – Programregistrering (förhandsversion)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)** > **Ny registrering**.
> 1. I avsnittet **Namn** anger du ett beskrivande namn som visas för användare av appen, till exempel `Daemon-console`, och välj sedan **Registrera** för att skapa appen.
> 1. När den har registrerats väljer du menyn **Certifikat och hemligheter**.
> 1. Under **Klienthemligheter** väljer du **+ Ny klienthemlighet**. Ge den ett namn och välj **Lägg till**. Kopiera hemligheten på en säker plats. Du behöver den för att använda i din kod.
> 1. Välj nu menyn **API Behörigheter**, välj **+ Lägg till en behörighet** och välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Under noden **Användare** väljer du **User.Read.All** och väljer sedan **Lägg till behörigheter**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hämta och konfigurera din app för Snabbstart
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app på Azure Portal
> För att kodexemplet för den här snabbstarten ska fungera måste du skapa en klienthemlighet och lägga till Graph-API:ts programbehörighet **User.Read.All**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-windows-desktop/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

[Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt

1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
1. Öppna lösningen i Visual Studio – **daemon-console.sln** (valfritt).
1. Redigera **appsettings.json** och ersätt värdena för fälten `ClientId`, `Tenant` och `ClientSecret` med följande:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Generera en ny klienthemlighet]()
    
    > [!div renderon="docs"]
    >> Där:
    >> * `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.
    >> * `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
    >> * `Enter_the_Client_Secret_Here` – ersätt det här värdet med klienthemligheten som skapades i steg 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och går du till appens **översiktssida** i Azure-portalen. Generera en ny nyckel genom att gå till sidan **Certifikat och hemligheter**.
    
#### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Alla *appspecifika behörigheter* kräver godkännande av administratör, vilket betyder att de kräver att en global administratör för din katalog ger sitt medgivande till din app. Välj ett av alternativen nedan, beroende på din roll:

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global klientadministratör går du till sidan **API-behörigheter** i Programregistrering (förhandsversion) i Azure-portalen och väljer **Bevilja administratörsmedgivande för {klientnamn}** (där {klientnamn} är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **Bevilja administratörsmedgivande för Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är standardanvändare i klientorganisationen måste du be en global administratör att bevilja administratörsmedgivande för din app. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Där:
>> * `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.

> [!NOTE]
> Du kan se felet *”AADSTS50011: Ingen svarsadress registrerad för programmet”* när du har beviljat åtkomst till appen med föregående URL. Det här händer eftersom den här appen och URL:en inte har en omdirigerings-URI – ignorera felet.

#### <a name="step-5-run-the-application"></a>Steg 5: Köra programmet

Om du använder Visual Studio trycker du på **F5** för att köra appen. Annars kör du appen via kommandotolken eller konsolen:

```console
cd {ProjectFolder}\daemon-console
dotnet run
```

> Där:
> * *{ProjectFolder}* är den mapp där du har extraherat zip-filen. Exempel **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Du bör se en lista över användare i Azure AD-katalogen som resultat.

## <a name="more-information"></a>Mer information

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära token som används för åtkomst till ett API som skyddas av Microsoft Azure Active Directory (Azure AD). Som beskrivs begär den här snabbstarten token med hjälp av appens egen identitet istället för delegerade behörigheter. Autentiseringsflödet som används i det här fallet kallas för *[oauth-flöde för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)*. Mer information om hur du använder MSAL.NET med flöde för klientautentiseringsuppgifter finns i [den här artikeln](https://aka.ms/msal-net-client-credentials).

 Du kan installera MSAL.NET genom att köra följande kommando i **Package Manager-konsolen** i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Initiera sedan MSAL med hjälp av följande kod:

```csharp
ClientCredential clientCredentials = new ClientCredential(secret: config.ClientSecret);

var app = new ConfidentialClientApplication(
    clientId: config.ClientId, 
    authority: config.Authority, 
    redirectUri: "https://daemon", 
    clientCredential: clientCredentials, 
    userTokenCache: null, 
    appTokenCache: new TokenCache()
);
```

> | Där: ||
> |---------|---------|
> | `secret` | Är klienthemligheten som skapats för appen i Azure-portalen. |
> | `clientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |
> | `Authority`    | (Valfritt) STS-slutpunkten för autentisering av användaren. Vanligtvis https://login.microsoftonline.com/{tenant} för offentligt moln, där {klient} är namnet på klientorganisationen eller klient-ID:t.|
> | `redirectUri`  | URL dit användarna skickas efter autentisering. I det här fallet används inte den här parametern eftersom det är en konsolapp/icke interaktiv app |
> | `clientCredentials`  | Objektet för klientautentiseringsuppgifter, som innehåller hemligheten eller certifikatet |
> | `userTokenCache`  | Instans av en tokencache för användaren. I det här fallet är värdet null, eftersom appen körs i kontexten för appen och inte för användaren|
> | `appTokenCache`  | Instans av en tokencache för appen|

Mer information finns i [referensdokumentationen för `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.-ctor?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication__ctor_System_String_System_String_System_String_Microsoft_Identity_Client_ClientCredential_Microsoft_Identity_Client_TokenCache_Microsoft_Identity_Client_TokenCache)

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token med appens identitet använder du metoden `AcquireTokenForClientAsync`:

```csharp
result = await app.AcquireTokenForClientAsync(scopes);
```

> |Där:| |
> |---------|---------|
> | `scopes` | Innehåller omfattningarna som begärdes. För konfidentiella klienter bör ett format som liknar `{Application ID URI}/.default` användas för att ange att omfattningarna som begärs är dem som statiskt definieras i appobjektet som anges i Azure-portalen (för Microsoft Graph, `{Application ID URI}` pekar på `https://graph.microsoft.com`). För anpassade webb-API:er definieras `{Application ID URI}` under avsnittet **Exponera ett API** i Programregistrering (förhandsversion) i Azure-portalen. |

Mer information finns i [referensdokumentationen för `AcquireTokenForClientAsync`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclientasync?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication_AcquireTokenForClientAsync_System_Collections_Generic_IEnumerable_System_String__)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om behörigheter och medgivande:

> [!div class="nextstepaction"]
> [Behörigheter och samtycke](v2-permissions-and-consent.md)

Mer information om autentiseringsflödet för det här scenariot finns i Oauth 2.0-flödet för klientautentiseringsuppgifter:

> [!div class="nextstepaction"]
> [Oauth-flöde för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)

> [!div class="nextstepaction"]
> [Flöden för klientautentiseringsuppgifter med MSAL.NET](https://aka.ms/msal-net-client-credentials)