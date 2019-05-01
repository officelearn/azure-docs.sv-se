---
title: Autentisera med Azure Active Directory att komma åt data för blob och kö från dina program | Microsoft Docs
description: Använda Azure Active Directory för att autentisera från inom ett program och sedan godkänna begäranden till blobbar och köer.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 979019da60c1129c6c6ef0bec42d9f9096735c1b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926045"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Autentisera med Azure Active Directory från ett program för åtkomst till blobbar och köer

En stor fördel med att använda Azure Active Directory (Azure AD) med Azure Storage är att det inte längre behöver dina autentiseringsuppgifter som ska lagras i din kod. I stället kan du begära en OAuth 2.0-åtkomsttoken från Azure AD. Azure AD hanterar autentisering för säkerhetsobjektet (en användare, grupp eller tjänstens huvudnamn) där programmet körs. Om autentiseringen lyckas, Azure AD tillbaka åtkomsttoken till programmet och programmet kan sedan använda den åtkomst-token för att godkänna begäranden till Azure Storage.

Den här artikeln visar hur du konfigurerar ditt program för autentisering med Azure AD. Använd ett liknande sätt kod exempel funktioner .NET, men andra språk.

Konfigurera rollbaserade inställningar för åtkomstkontroll (RBAC) för det säkerhetsobjektet innan du autentiserar ett säkerhetsobjekt från ditt Azure Storage-program. Azure Storage definierar RBAC-roller som omfattar behörigheter för behållare och köer. När RBAC-roll tilldelas till ett säkerhetsobjekt, beviljas det säkerhetsobjektet åtkomst till resursen. Mer information finns i [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).

En översikt över kod grant-flöde för OAuth 2.0, se [flöde beviljat med auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av OAuth 2.0-koden](../../active-directory/develop/v1-protocols-oauth-code.md).

Du måste använda HTTPS för att auktorisera blob och kö åtgärder med en OAuth-token.

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Tilldela en RBAC-roll till en Azure AD-säkerhetsobjekt

För att autentisera ett säkerhetsobjekt från ditt Azure Storage-program måste du först konfigurera rollbaserad inställningar för åtkomstkontroll (RBAC) för det säkerhetsobjektet. Azure Storage definierar RBAC-roller som omfattar behörigheter för behållare och köer. När RBAC-roll tilldelas till ett säkerhetsobjekt, beviljas det säkerhetsobjektet åtkomst till resursen. Mer information finns i [hantera åtkomsträttigheter till Azure-Blob och kö data med RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program med en Azure AD-klient

Det första steget i att använda Azure AD för att bevilja åtkomst till lagringsresurser registrerar ditt klientprogram i en Azure AD-klient. Registrera ditt program kan du anropa Azure [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) från din kod. ADAL tillhandahåller ett API för att autentisera med Azure AD från ditt program. Registrera ditt program kan du också att auktorisera anrop från programmet till Azure Storage API: er med åtkomsttoken.

När du registrerar ditt program kan ange du information om ditt program till Azure AD. Sedan Azure AD tillhandahåller ett klient-ID (även kallat en *program-ID*) att du använder för att associera ditt program med Azure AD vid körning. Läs mer om klient-ID i [program och tjänstobjekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Registrera ditt Azure Storage-program genom att följa stegen i den [lägga till ett program](../../active-directory/develop/quickstart-register-app.md) i avsnittet [integrera program med Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Om du registrerar ditt program som ett internt program, kan du ange en giltig URI för den **omdirigerings-URI**. Värdet behöver inte vara en verklig slutpunkt.

![Skärmbild som visar hur du registrerar ditt storage-program med Azure AD](./media/storage-auth-aad-app/app-registration.png)

När du har registrerat ditt program visas det program-ID (eller klient-ID) under **inställningar**:

![Skärmbild som visar klient-ID](./media/storage-auth-aad-app/app-registration-client-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Bevilja registrerad app till Azure Storage

Därefter måste du ge ditt program behörighet att anropa API: erna för Azure Storage. Det här steget gör att programmet att auktorisera anrop till Azure Storage med Azure AD.

1. I det vänstra navigeringsfönstret i Azure-portalen, väljer **alla tjänster**, och Sök efter **Appregistreringar**.
2. Sök efter namnet på det registrerade programmet som du skapade i föregående steg.
3. Välj din registrerad app och klicka på **inställningar**. I den **API-åtkomst** väljer **behörigheter som krävs för**.
4. I den **nödvändiga behörigheter** bladet klickar du på den **Lägg till** knappen.
5. Under **Välj en API**, Sök efter ”Azure Storage” och välj **Azure Storage** från listan över resultat.

    ![Skärmbild som visar behörigheter för lagring](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Under **Välj behörigheter**, markera kryssrutan bredvid **åtkomst till Azure Storage**, och klicka på **Välj**.
7. Klicka på **Klar**.

Den **nödvändiga behörigheter** windows nu visar att din Azure AD-program har åtkomst till både Azure Active Directory och Azure Storage. Behörigheterna har beviljats till Azure AD automatiskt när du först registrera din app med Azure AD.

![Skärmbild som visar registrera app-behörigheter](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: Skapa en blockblob

Exemplet visar hur du hämta en token från Azure AD. Åtkomsttoken används för att autentisera den angivna användaren och sedan godkänna en begäran om att skapa en blockblob. För att få det här exemplet fungerar, följer du stegen som i föregående avsnitt.

> [!NOTE]
> Ägare för Azure Storage-kontot kan tilldelas du automatiskt inte behörighet att komma åt data. Du måste uttryckligen tilldela dig själv en RBAC-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resursgrupp, storage-konto, eller behållare eller kön. 
>
> Till exempel om du vill köra exempelkod för ett lagringskonto där du är ägare och på din egen användaridentitet, måste du tilldela rollen RBAC för Blob Data-deltagare till dig själv. I annat fall misslyckas anropet för att skapa bloben med HTTP-statuskod 403 (förbjudet). Mer information finns i [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Välkända värden för autentisering med Azure AD

För att autentisera en säkerhetsprincip med Azure AD, måste du inkludera vissa välkända värden i din kod.

#### <a name="azure-ad-authority"></a>Azure AD-utfärdare

För offentliga Microsoft-molnet, grundläggande Azure AD-utfärdare är enligt följande, där *klient-id* Active Directory-klient-ID (eller katalog-ID):

`https://login.microsoftonline.com/<tenant-id>/`

Klient-ID identifierar Azure AD-klient ska användas för autentisering. Om du vill hämta klient-ID, följer du stegen som beskrivs i **hämta klient-ID för Azure Active Directory**.

#### <a name="storage-resource-id"></a>Storage resurs-ID

Använd Azure Storage resurs-ID för att hämta en token för autentisering av förfrågningar till Azure Storage:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Hämta klient-ID för Azure Active Directory

Följ dessa steg om du vill hämta klient-ID:

1. Välj din Active Directory i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Kopiera GUID-värdet som angetts för den **katalog-ID**. Det här värdet kallas även för klient-ID.

![Skärmbild som visar hur du kopierar klient-ID](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Lägg till referenser och using-satser  

Installera Azure Storage-klientbibliotek från Visual Studio. Från den **verktyg** menyn och välj **Nuget-Pakethanteraren**, sedan **Pakethanterarkonsolen**. Skriv följande kommando i konsolen för att installera den senaste versionen av klientbiblioteket för .NET:

```
Install-Package WindowsAzure.Storage
```

Även installera den senaste versionen av ADAL:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Lägg sedan till följande using-satser i koden:

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Hämta en OAuth-token från Azure AD

Lägg sedan till en metod som begär en token från Azure AD. Om du vill begära token, anropa den [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metod. Se till att du har följande värden från de steg som du följde tidigare:

- ID för innehavare (katalog)
- Klient (program)-ID
- Klienten omdirigerings-URI

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Skapa blockblob

Slutligen använder åtkomsttoken för att skapa nya storage-autentiseringsuppgifter och använder dessa autentiseringsuppgifter för att skapa bloben. Tänk på att du måste använda HTTPS för att auktorisera blob och kö åtgärder med en OAuth-token.:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Azure AD-integrering med Azure Storage kräver att du använder HTTPS för Azure Storage-åtgärder.

I exemplet ovan hanterar .NET-klientbiblioteket godkännande av begäran om att skapa blockblob. Andra lagringsklientbiblioteken hanterar även auktorisering för begäran för dig. Men om du anropar en Azure Storage-åtgärd med en OAuth-token med hjälp av REST-API, behöver sedan du auktorisera begäran med hjälp av OAuth-token.   

För att anropa Blob och kö tjänståtgärder med OAuth-åtkomsttoken, skicka åtkomsttoken i den **auktorisering** rubrik med hjälp av den **ägar** system och ange en service-version 2017-11-09 eller högre, som i följande exempel visas:

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Mer information om hur du auktoriserar Azure Storage-åtgärder från REST finns [autentisera med Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).
- Läs om hur du använder hanterade identiteter för Azure-resurser med Azure Storage i [autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).
- Läs hur du kör Azure CLI och PowerShell-kommandon med autentiseringsuppgifter för Azure AD i [kör Azure CLI eller PowerShell-kommandon med autentiseringsuppgifter för Azure AD åtkomst till blob-eller kön](storage-auth-aad-script.md).
