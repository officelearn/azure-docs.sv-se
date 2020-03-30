---
title: Skaffa en token från Azure AD för att godkänna begäranden från ett klientprogram
titleSuffix: Azure Storage
description: Använd Azure Active Directory för att autentisera inifrån ett klientprogram, hämta en OAuth 2.0-token och auktorisera begäranden till Azure Blob-lagring och kölagring.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268488"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Skaffa en token från Azure AD för att godkänna begäranden från ett klientprogram

En viktig fördel med att använda Azure Active Directory (Azure AD) med Azure Blob storage eller Queue storage är att dina autentiseringsuppgifter inte längre behöver lagras i din kod. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsofts identitetsplattform (tidigare Azure AD). Azure AD autentiserar säkerhetsobjektet (en användare, grupp eller tjänsthuvudnamn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäranden till Azure Blob-lagring eller Kölagring.

Den här artikeln visar hur du konfigurerar ditt inbyggda program eller webbprogram för autentisering med Microsoft identity platform 2.0. Kodexemplet har .NET, men andra språk använder en liknande metod. Mer information om Microsoft identity platform 2.0 finns i [översikt över Microsoft identity platform (v2.0).](../../active-directory/develop/v2-overview.md)

En översikt över OAuth 2.0-kodbidragsflödet finns i [Auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av OAuth 2.0-kodbidragsflödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Tilldela en roll till ett Azure AD-säkerhetsobjekt

Om du vill autentisera ett säkerhetsobjekt från ditt Azure Storage-program konfigurerar du först RBAC-inställningar (Role-based Access Control) för det säkerhetsobjektet. Azure Storage definierar inbyggda RBAC-roller som omfattar behörigheter för behållare och köer. När RBAC-rollen tilldelas ett säkerhetsobjekt beviljas det säkerhetsobjektet åtkomst till resursen. Mer information finns i [Hantera åtkomsträttigheter till Azure Blob- och Ködata med RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program hos en Azure AD-klient

Det första steget i att använda Azure AD för att auktorisera åtkomst till lagringsresurser är att registrera ditt klientprogram med en Azure AD-klient från [Azure-portalen](https://portal.azure.com). När du registrerar klientprogrammet anger du information om programmet till Azure AD. Azure AD tillhandahåller sedan ett klient-ID (kallas även *ett program-ID)* som du använder för att associera ditt program med Azure AD vid körning. Mer information om klient-ID finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Om du vill registrera ditt Azure Storage-program följer du stegen i [Snabbstart: Registrera ett program med Microsoft identity-plattformen](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Följande bild visar vanliga inställningar för att registrera ett webbprogram:

![Skärmbild som visar hur du registrerar ditt lagringsprogram med Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Om du registrerar ditt program som ett inbyggt program kan du ange en giltig URI för **Redirect URI**. För inbyggda program behöver det här värdet inte vara en riktig URL. För webbprogram måste omdirigerings-URI:n vara en giltig URI, eftersom den anger webbadressen till vilken token tillhandahålls.

När du har registrerat ditt program visas program-ID:t (eller klient-ID) under **Inställningar:**

![Skärmbild som visar klient-ID](./media/storage-auth-aad-app/app-registration-client-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [Integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Bevilja dina registrerade appbehörigheter till Azure Storage

Bevilja sedan dina programbehörigheter för att anropa Azure Storage-API:er. Det här steget gör det möjligt för ditt program att auktorisera begäranden till Azure Storage med Azure AD.

1. På sidan **Översikt** för ditt registrerade program väljer du **Visa API-behörigheter**.
1. I avsnittet **API-behörigheter** väljer du **Lägg till en behörighet** och väljer Microsoft **API:er**.
1. Välj **Azure Storage** i listan över resultat för att visa fönstret **Api-behörigheter för begäran.**
1. Under **Vilken typ av behörigheter kräver ditt program?** **Delegated permissions** Det här alternativet är markerat som standard.
1. Markera kryssrutan bredvid **user_impersonation**i avsnittet **Välj behörigheter** i fönstret **Behörigheter för begäran** om API och klicka sedan på Lägg **till behörigheter**.

    ![Skärmbild som visar behörigheter för lagring](media/storage-auth-aad-app/registered-app-permissions-1.png)

**API-behörighetsfönstret** visar nu att ditt registrerade Azure AD-program har åtkomst till både Microsoft Graph och Azure Storage. Behörigheter beviljas Microsoft Graph automatiskt när du först registrerar din app med Azure AD.

![Skärmbild som visar behörigheter för registerappar](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Programmet behöver en klienthemlighet för att bevisa sin identitet när du begär en token. Så här lägger du till klienthemligheten:

1. Navigera till din appregistrering i Azure-portalen.
1. Välj inställningen **Certifikat & hemligheter.**
1. Under **Klienthemligheter**klickar du på **Ny klienthemlighet** för att skapa en ny hemlighet.
1. Ange en beskrivning av hemligheten och välj önskat utgångsdatum.
1. Kopiera omedelbart värdet på den nya hemligheten till en säker plats. Det fullständiga värdet visas bara en gång för dig.

    ![Skärmbild som visar klienthemlighet](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Klientbibliotek för tokenförvärv

När du har registrerat ditt program och beviljat det behörighet att komma åt data i Azure Blob-lagring eller Kölagring kan du lägga till kod i ditt program för att autentisera ett säkerhetsobjekt och hämta en OAuth 2.0-token. Om du vill autentisera och hämta token kan du använda antingen ett av [Microsofts identitetsplattformsautentiseringsbibliotek](../../active-directory/develop/reference-v2-libraries.md) eller ett annat bibliotek med öppen källkod som stöder OpenID Connect 1.0. Ditt program kan sedan använda åtkomsttoken för att auktorisera en begäran mot Azure Blob-lagring eller kölagring.

En lista över scenarier som det stöds att hämta token finns i avsnittet [autentiseringsflöden](/en-us/azure/active-directory/develop/msal-authentication-flows) i innehållet i [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Välkända värden för autentisering med Azure AD

Om du vill autentisera ett säkerhetsobjekt med Azure AD måste du inkludera några välkända värden i koden.

### <a name="azure-ad-authority"></a>Azure AD-behörighet

För Microsofts offentliga moln är den grundläggande Azure AD-behörigheten följande, där klient-ID är ditt Active *Directory-klient-ID* (eller katalog-ID):

`https://login.microsoftonline.com/<tenant-id>/`

Klient-ID identifierar Azure AD-klienten som ska användas för autentisering. Det kallas också katalog-ID. Om du vill hämta klient-ID:et navigerar du till sidan **Översikt** för din appregistrering i Azure-portalen och kopierar värdet därifrån.

### <a name="azure-storage-resource-id"></a>ID för Azure Storage-resurs

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: Skapa en blockblob

Kodexemplet visar hur du får en åtkomsttoken från Azure AD. Åtkomsttoken används för att autentisera den angivna användaren och sedan auktorisera en begäran om att skapa en blockblob. Om du vill att det här exemplet ska fungera följer du först stegen i föregående avsnitt.

Om du vill begära token behöver du följande värden från appens registrering:

- Namnet på din Azure AD-domän. Hämta det här värdet från **översiktssidan** i din Azure Active Directory.
- Klient-ID:t för klient (eller katalog). Hämta det här värdet från sidan **Översikt för** din appregistrering.
- Klient-ID(eller program). Hämta det här värdet från sidan **Översikt för** din appregistrering.
- Klientomdirigering URI. Hämta det här värdet från **autentiseringsinställningarna** för din appregistrering.
- Värdet på klienthemligheten. Hämta det här värdet från den plats som du tidigare kopierade det till.

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare

Om du vill köra kodexemplet skapar du ett lagringskonto i samma prenumeration som din Azure Active Directory. Skapa sedan en behållare i det lagringskontot. Exempelkoden skapar en blockblob i den här behållaren.

Tilldela sedan uttryckligen rollen **Storage Blob Data Contributor** till användarkontot som du ska köra exempelkoden under. Instruktioner om hur du tilldelar den här rollen i Azure-portalen finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörigheter för att komma åt data via Azure AD. Du måste uttryckligen tilldela dig själv en RBAC-roll för Azure Storage. Du kan tilldela den på prenumerationsnivå, resursgrupp, lagringskonto eller behållare eller kö.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Skapa ett webbprogram som ger åtkomst till Blob-lagring med Azure AD

När ditt program kommer åt Azure Storage gör det för användarens räkning, vilket innebär att blob- eller köresurser används med hjälp av behörigheterna för den användare som är inloggad. Om du vill prova det här kodexemplet behöver du ett webbprogram som uppmanar användaren att logga in med en Azure AD-identitet. Du kan skapa egna eller använda exempelprogrammet som tillhandahålls av Microsoft.

Ett ifyllt exempelwebbprogram som hämtar en token och använder den för att skapa en blob i Azure Storage är tillgängligt på [GitHub](https://aka.ms/aadstorage). Granska och köra det slutförda exemplet kan vara till hjälp för att förstå kodexemplen. Instruktioner om hur du kör det slutförda exemplet finns i avsnittet [Visa och kör det slutförda exemplet](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Lägga till referenser och använda satser  

Installera Azure Storage-klientbiblioteket från Visual Studio. Öppna menyn **Verktyg**. Välj **NuGet-pakethanterare** och sedan **Package Manager-konsolen**. Skriv följande kommandon i konsolfönstret för att installera nödvändiga paket från Azure Storage-klientbiblioteket för .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Lägg sedan till följande med hjälp av satser i HomeController.cs-filen:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Skapa en blockblob

Lägg till följande kodavsnitt för att skapa en blockblob:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Om du vill auktorisera blob- och köåtgärder med en OAuth 2.0-token måste du använda HTTPS.

I exemplet ovan hanterar .NET-klientbiblioteket auktoriseringen av begäran om att skapa blockbloben. Azure Storage-klientbibliotek för andra språk hanterar också auktoriseringen av begäran för dig. Men om du anropar en Azure Storage-åtgärd med en OAuth-token med REST API måste du auktorisera begäran med OAuth-token.

Om du vill anropa Blob- och Queue-tjänståtgärder med OAuth-åtkomsttoken skickar du åtkomsttoken i **auktoriseringshuvudet** med hjälp av **bärareschemat** och anger en tjänstversion av 2017-11-09 eller senare, som visas i följande exempel:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Hämta en OAuth-token från Azure AD

Lägg sedan till en metod som begär en token från Azure AD för användarens räkning. Den här metoden definierar det scope som behörigheter ska beviljas för. Mer information om behörigheter och scope finns [i Behörigheter och medgivande i slutpunkten för Microsoft-identitetsplattform](../../active-directory/develop/v2-permissions-and-consent.md).

Använd resurs-ID:t för att konstruera det scope som token ska hämtas för. Exemplet konstruerar omfånget med hjälp av resurs-ID tillsammans med det inbyggda `user_impersonation` omfånget, vilket anger att token begärs för användarens räkning.

Tänk på att du kan behöva presentera användaren med ett gränssnitt som gör det möjligt för användaren att samtycka till att begära token deras räkning. När samtycke är nödvändigt fångar exemplet **MsalUiRequiredException** och kräver en annan metod för att underlätta begäran om samtycke:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Samtycke är processen för en användare som beviljar tillstånd till ett program för att komma åt skyddade resurser för deras räkning. Microsofts identitetsplattform 2.0 stöder inkrementellt medgivande, vilket innebär att ett säkerhetsobjekt kan begära en minsta uppsättning behörigheter från början och lägga till behörigheter över tid efter behov. När koden begär en åtkomsttoken anger du omfattningen av de behörigheter `scope` som appen behöver vid en given tidpunkt i parametern. Mer information om inkrementellt medgivande finns i avsnittet **Inkrementellt och dynamiskt medgivande** i [Varför uppdatera till Microsoft identity platform (v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Följande metod konstruerar autentiseringsegenskaperna för att begära inkrementellt medgivande:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Visa och kör det slutförda exemplet

Om du vill köra exempelprogrammet klonar eller hämtar du det från [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Uppdatera sedan programmet enligt beskrivningen i följande avsnitt.

### <a name="provide-values-in-the-settings-file"></a>Ange värden i inställningsfilen

Uppdatera sedan *filen appsettings.json* med dina egna värden enligt följande:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Uppdatera lagringskontot och behållarnamnet

I *den HomeController.cs* filen uppdaterar du URI:n som refererar till blockbloben för att använda namnet på ditt lagringskonto och behållare:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Aktivera implicit tilldelningsflöde

Om du vill köra exemplet kan du behöva konfigurera det implicita bidragsflödet för appregistreringen. Följ de här stegen:

1. Navigera till din appregistrering i Azure-portalen.
1. Välj **autentiseringsinställningen** i avsnittet Hantera.
1. Markera kryssrutorna i avsnittet **Implicita bidrag** under **Avancerade inställningar**för att aktivera åtkomsttoken och ID-token, som visas i följande bild:

    ![Skärmbild som visar hur du aktiverar inställningar för implicit bidragsflöde](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Uppdatera porten som används av localhost

När du kör exemplet kan du upptäcka att du måste uppdatera den omdirigerings-URI som anges i appregistreringen för att använda den *localhost-port* som tilldelats vid körning. Så här uppdaterar du omdirigerings-URI:n för att använda den tilldelade porten:

1. Navigera till din appregistrering i Azure-portalen.
1. Välj **autentiseringsinställningen** i avsnittet Hantera.
1. Under **Omdirigera URI:er**redigerar du porten så att den matchar den som används av exempelprogrammet, vilket visas i följande bild:

    ![Skärmbild som visar omdirigera URI:er för appregistrering](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om Microsofts identitetsplattform finns i [Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/).
- Mer information om RBAC-roller för Azure-lagring finns i [Hantera åtkomsträttigheter till lagringsdata med RBAC](storage-auth-aad-rbac.md).
- Mer information om hur du använder hanterade identiteter för Azure-resurser med Azure Storage finns [i Autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).
