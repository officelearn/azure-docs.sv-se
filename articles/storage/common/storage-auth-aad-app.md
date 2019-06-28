---
title: Autentisera med Azure Active Directory att komma åt blob och kö data från ditt klientprogram
description: Använda Azure Active Directory för att autentisera från inom ett klientprogram, hämta en OAuth 2.0-token och godkänna förfrågningar till Azure Blob storage och Queue storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e57291292d8957fd323f9be03bb7df0492484ea8
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341622"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Autentisera med Azure Active Directory från ett program för åtkomst till blobbar och köer

En stor fördel med att använda Azure Active Directory (Azure AD) med Azure Blob storage eller Queue storage är att det inte längre behöver dina autentiseringsuppgifter som ska lagras i din kod. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsoft identity-plattformen (tidigare Azure AD). Azure AD autentiserar säkerhetsobjekt (en användare, grupp eller tjänstens huvudnamn) där programmet körs. Om autentiseringen lyckas, Azure AD tillbaka åtkomsttoken till programmet och programmet kan sedan använda den åtkomst-token för att godkänna begäranden till Azure Blob storage och Queue storage.

Den här artikeln visar hur du konfigurerar ditt interna program eller en webbapp för autentisering med Microsoft identity-plattformen 2.0. Använd ett liknande sätt kod exempel funktioner .NET, men andra språk. Läs mer om Microsoft identity-plattformen 2.0 [översikt över Microsoft identity-plattformen (v2.0)](../../active-directory/develop/v2-overview.md).

En översikt över kod grant-flöde för OAuth 2.0, se [flöde beviljat med auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av OAuth 2.0-koden](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Tilldela en roll till en Azure AD-säkerhetsgrupp huvudnamn

För att autentisera ett säkerhetsobjekt från ditt Azure Storage-program måste du först konfigurera rollbaserad inställningar för åtkomstkontroll (RBAC) för det säkerhetsobjektet. Azure Storage definierar inbyggda RBAC-roller som omfattar behörigheter för behållare och köer. När RBAC-roll tilldelas till ett säkerhetsobjekt, beviljas det säkerhetsobjektet åtkomst till resursen. Mer information finns i [hantera åtkomsträttigheter till Azure-Blob och kö data med RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program med en Azure AD-klient

Det första steget i att använda Azure AD för att bevilja åtkomst till lagringsresurser som registrerar ditt klientprogram med Azure AD-klient från den [Azure-portalen](https://portal.azure.com). När du registrerar ditt klientprogram kan ange du information om programmet till Azure AD. Sedan Azure AD tillhandahåller ett klient-ID (även kallat en *program-ID*) att du använder för att associera ditt program med Azure AD vid körning. Läs mer om klient-ID i [program och tjänstobjekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Registrera ditt Azure Storage-program genom att följa anvisningarna i [snabbstarten: Registrera ett program med Microsoft identity-plattformen](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). I följande bild visas vanliga inställningar för att registrera ett webbprogram:

![Skärmbild som visar hur du registrerar ditt storage-program med Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Om du registrerar ditt program som ett internt program, kan du ange en giltig URI för den **omdirigerings-URI**. För interna program har inte det här värdet vara en verklig URL. För webbprogram, måste omdirigerings-URI vara en giltig URI, eftersom den anger den URL som token tillhandahålls.

När du har registrerat ditt program visas det program-ID (eller klient-ID) under **inställningar**:

![Skärmbild som visar klient-ID](./media/storage-auth-aad-app/app-registration-client-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Bevilja registrerad app till Azure Storage

Därefter måste ge ditt program behörighet att anropa API: erna för Azure Storage. Det här steget gör att programmet att godkänna begäranden till Azure Storage med Azure AD.

1. På den **översikt** sidan för ditt registrerade program, Välj **API visningsbehörigheter**.
1. I den **API-behörigheter** väljer **lägga till en behörighet** och välj **API: er som min organisation använder**.
1. Under den **API: er som min organisation använder** avsnittet, Sök efter ”Azure Storage” och välj **Azure Storage** i listan med resultat att visa den **begär API-behörigheter** fönstret.

    ![Skärmbild som visar behörigheter för lagring](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. Under **vilken typ av behörigheter för ditt program kräver?** , Observera att det är tillgängligt behörighetstyp **delegerade behörigheter**. Det här alternativet väljs automatiskt som standard.
1. I den **Välj behörigheter** delen av den **begär API-behörigheter** fönstret markerar du kryssrutan bredvid **user_impersonation**, klicka sedan på **Lägg till behörigheter**.
1. Den **API-behörigheter** nu visar att din Azure AD-program har åtkomst till både Microsoft Graph och Azure Storage. Behörigheterna har beviljats till Microsoft Graph automatiskt när du först registrera din app med Azure AD.

    ![Skärmbild som visar registrera app-behörigheter](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Programmet måste en klienthemlighet för att bevisa sin identitet när du begär en token. Lägg till klienthemligheten genom att följa dessa steg:

1. Gå till din appregistrering i Azure-portalen.
1. Välj den **certifikat och hemligheter** inställningen.
1. Under **klienten hemligheter**, klickar du på **nya klienthemligheten** att skapa en ny hemlighet.
1. Ange en beskrivning för hemligheten och välja önskad giltighetsintervallet.
1. Kopiera värdet för den nya hemligheten direkt till en säker plats. Hela värdet visas bara en gång till dig.

    ![Skärmbild som visar klienthemlighet](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Klientbibliotek för tokenförvärv

När du har registrerat ditt program och den har beviljat behörighet att komma åt data i Azure Blob storage eller Queue storage kan du lägga till kod till programmet att autentisera ett säkerhetsobjekt och få en OAuth 2.0-token. För att autentisera och hämta token, kan du använda någon av de [autentiseringsbibliotek för Microsoft identity-plattformen](../../active-directory/develop/reference-v2-libraries.md) eller ett annat bibliotek för öppen källkod som har stöd för OpenID Connect 1.0. Programmet kan sedan använda den åtkomst-token för att godkänna en begäran mot Azure Blob storage och Queue storage.

En lista över scenarier för vilka hämta token stöds finns i den [scenarier](https://aka.ms/msal-net-scenarios) delen av den [Microsoft Authentication Library (MSAL) för .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-lagringsplatsen.

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: Skapa en blockblob

Exemplet visar hur du hämta en token från Azure AD. Åtkomsttoken används för att autentisera den angivna användaren och sedan godkänna en begäran om att skapa en blockblob. För att få det här exemplet fungerar, följer du stegen som i föregående avsnitt.

Om du vill begära token, behöver du följande värden från appens registrering:

- Namnet på din Azure AD-domän. Hämtar värdet från den **översikt** sidan i Azure Active Directory.
- Klient (eller katalog)-ID. Hämtar värdet från den **översikt** sidan i din appregistrering.
- Klienten (eller program)-ID. Hämtar värdet från den **översikt** sidan i din appregistrering.
- Den klient omdirigeringen-URI. Hämtar värdet från den **autentisering** inställningar för appregistrering av din.
- Värdet för klienthemligheten. Hämta värdet från den plats som du tidigare har kopierat.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Välkända värden för autentisering med Azure AD

För att autentisera en säkerhetsprincip med Azure AD, måste du inkludera vissa välkända värden i din kod.

#### <a name="azure-ad-authority"></a>Azure AD-utfärdare

För offentliga Microsoft-molnet, grundläggande Azure AD-utfärdare är enligt följande, där *klient-id* Active Directory-klient-ID (eller katalog-ID):

`https://login.microsoftonline.com/<tenant-id>/`

Klient-ID identifierar Azure AD-klient ska användas för autentisering. Det också kallas katalog-ID Om du vill hämta klient-ID, navigera till den **översikt** för din appregistrering i Azure-portalen och kopiera värdet därifrån.

#### <a name="storage-resource-id"></a>Storage resurs-ID

Använd Azure Storage resurs-ID för att hämta en token för auktorisering av förfrågningar till Azure Storage:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare

Skapa ett lagringskonto i samma prenumeration som Azure Active Directory för att köra kodexemplet. Sedan skapa en behållare i det lagringskontot. Exempelkoden skapar en blockblob i den här behållaren.

Sedan uttryckligen tilldela den **Storage Blob Data-deltagare** rollen till det användarkonto som du ska köras under exempelkoden. Anvisningar om hur du tilldelar den här rollen i Azure-portalen finns i [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> När du skapar ett Azure Storage-konto kan tilldelas du automatiskt inte behörighet att komma åt data via Azure AD. Du måste uttryckligen tilldela dig själv en RBAC-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resursgrupp, storage-konto, eller behållare eller kön.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Skapa ett webbprogram som auktoriserar åtkomst till Blob storage med Azure AD

När ditt program har åtkomst till Azure Storage, handlar det på användarens vägnar, vilket innebär att blob eller kön resurser används med behörigheterna för den användare som är inloggad. Om du vill testa detta kodexempel, behöver du ett webbprogram som uppmanar användaren att logga in med en Azure AD-identitet. Du kan skapa en egen eller använda det exempelprogram som tillhandahålls av Microsoft.

Ett slutförda exempelwebbprogram som hämtar en token och används för att skapa en blob i Azure Storage är tillgängligt på [GitHub](https://aka.ms/aadstorage). Granska och köra den färdiga exemplet kan vara till hjälp för att förstå kodexemplen. Instruktioner om hur du kör det färdiga exemplet finns i avsnittet [visa och köra den färdiga exemplet](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Lägg till referenser och using-satser  

Installera Azure Storage-klientbibliotek från Visual Studio. Från den **verktyg** menyn och välj **Nuget-Pakethanteraren**, sedan **Pakethanterarkonsolen**. Skriv följande kommandon i konsolfönstret för att installera de nödvändiga paketen från Azure Storage-klientbiblioteket för .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Lägg sedan till följande using-satser i filen HomeController.cs:

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
> Du måste använda HTTPS för att auktorisera blob och kö åtgärder med en OAuth 2.0-token.

I exemplet ovan hanterar .NET-klientbiblioteket godkännande av begäran om att skapa blockblob. Godkännande av begäran om du hanterar även i Azure Storage-klientbibliotek för andra språk. Men om du anropar en Azure Storage-åtgärd med en OAuth-token med hjälp av REST-API, behöver sedan du auktorisera begäran med hjälp av OAuth-token.

För att anropa Blob och kö tjänståtgärder med OAuth-åtkomsttoken, skicka åtkomsttoken i den **auktorisering** rubrik med hjälp av den **ägar** system och ange en service-version 2017-11-09 eller högre, som i följande exempel visas:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Hämta en OAuth-token från Azure AD

Lägg sedan till en metod som begär en token från Azure AD. Den token som du begär blir användarens räkning och vi kommer att använda metoden GetTokenOnBehalfOfUser.

Kom ihåg att om du nyligen har loggat in och du begär en token för den `storage.azure.com` resurs, behöver du presentera ett användargränssnitt där användaren kan godkänna sådan åtgärd för deras räkning. Att underlätta att du behöver att fånga upp den `MsalUiRequiredException` och lägga till funktioner för att begära tillstånd, som visas i följande exempel:

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
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Medgivande är en process för en användare beviljas behörighet att ett program för att komma åt skyddade resurser å deras vägnar. Microsoft identity-plattformen 2.0 stöder inkrementella medgivande, vilket innebär att ett säkerhetsobjekt kan begära en minsta uppsättning behörigheter från början och Lägg till behörigheter över tid efter behov. När koden begär en åtkomsttoken, ange omfånget för behörigheter som din app behöver vid något tillfälle genom att i den `scope` parametern. Mer information om inkrementella medgivande finns i avsnittet **inkrementell och dynamiska medgivande** i [Varför uppdateras till Microsoft identity-plattformen (v2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Följande metod konstruktioner egenskaper för autentisering för att begära inkrementella medgivande:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>Visa och köra den färdiga exemplet

Om du vill köra exempelprogrammet, klona eller ladda ned det från [GitHub](https://aka.ms/aadstorage). Uppdatera sedan programmet enligt beskrivningen i följande avsnitt.

### <a name="provide-values-in-the-settings-file"></a>Ange värden i filen

Därefter uppdaterar den *appsettings.json* fil med dina egna värden, enligt följande:

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

### <a name="update-the-storage-account-and-container-name"></a>Uppdatera namnet för storage-konto och en behållare

I den *HomeController.cs* uppdaterar den URI som refererar till blockblob för att använda namnet på ditt lagringskonto och en behållare:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Aktivera implicit beviljande av flöde

Du kan behöva konfigurera implicit beviljande av flödet för appregistrering av din om du vill köra exemplet. Följ de här stegen:

1. Gå till din appregistrering i Azure-portalen.
1. I avsnittet ”Hantera”, väljer du den **autentisering** inställningen.
1. Under **avancerade inställningar**i den **Implicit beviljande** väljer du kryssrutorna för att aktivera åtkomst-token och ID-token som visas i följande bild:

    ![Skärmbild som visar hur du aktiverar inställningarna för implicit beviljande av flöde](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Uppdatera den port som används av localhost

När du kör exemplet hända att du behöver uppdatera omdirigerings-URI som angetts i din appregistrering för att använda den *localhost* port som har tilldelats vid körning. Följ dessa steg om du vill uppdatera omdirigeringen-URI att använda tilldelad port:

1. Gå till din appregistrering i Azure-portalen.
1. I avsnittet ”Hantera”, väljer du den **autentisering** inställningen.
1. Under **omdirigerings-URI: er**, redigera porten för att matcha som används av exempelprogrammet, enligt följande bild:

    ![Skärmbild som visar omdirigerings-URI: er för appregistrering](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Microsoft identity-plattformen i [Microsoft identity-plattformen](https://docs.microsoft.com/azure/active-directory/develop/).
- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).
- Läs om hur du använder hanterade identiteter för Azure-resurser med Azure Storage i [autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).
