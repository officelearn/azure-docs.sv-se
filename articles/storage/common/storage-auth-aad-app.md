---
title: Autentisera med Azure AD från Storage-program (förhandsversion) | Microsoft Docs
description: Autentisera med Azure AD från ett Azure Storage-program (förhandsversion).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 1bf4a8bba3b93c16f67d46f65292709ef2a1bba2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661010"
---
# <a name="authenticate-with-azure-ad-from-an-azure-storage-application-preview"></a>Autentisera med Azure AD från ett Azure Storage-program (förhandsgranskning)

En stor fördel med hjälp av Azure Active Directory (AD Azure) med Azure Storage är att det inte längre behöver dina autentiseringsuppgifter som ska lagras i din kod. I stället kan du begära en OAuth 2.0-åtkomsttoken från Azure AD. Azure AD hanterar autentisering av säkerhetsobjekt (en användare, grupp eller tjänstens huvudnamn) kör programmet. Om autentiseringen lyckas, Azure AD returnerar den åtkomst-token till programmet programmet kan sedan använda den åtkomst-token för att godkänna begäranden till Azure Storage

Den här artikeln visar hur du konfigurerar ditt program för autentisering med Azure AD. Använd ett liknande sätt kod exempel funktioner .NET, men andra språk.

Innan du kan verifiera ett säkerhetsobjekt från Azure Storage-program, konfigurera rollbaserad inställningar för åtkomstkontroll (RBAC) för att säkerhetsobjekt. Azure Storage definierar RBAC-roller som omfattar behörigheter för behållare och köer. När rollen RBAC tilldelas ett säkerhetsobjekt säkerhetsobjekt beviljas åtkomst till resursen. Mer information finns i [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).

En översikt över flödet för OAuth 2.0 kod bevilja finns [auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av OAuth 2.0-koden flöde beviljat med auktoriseringskod](../../active-directory/develop/active-directory-protocols-oauth-code.md).

> [!IMPORTANT]
> Den här förhandsgranskningen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga förrän Azure AD-integrering för Azure Storage har deklarerats allmänt tillgänglig. Om Azure AD-integrering inte stöds ännu för ditt scenario, fortsätta att använda delad nyckel auktorisering eller SAS-token i dina program. Mer information om förhandsversionen finns [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)](storage-auth-aad.md).
>
> Under förhandsgranskningen inkluderas kan RBAC rolltilldelningar ta upp till fem minuter att sprida.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program med en Azure AD-klient

Det första steget i att använda Azure AD för att bevilja åtkomst till lagringsresurser registrerar ditt klientprogram i Azure AD-klient. Registrera ditt program kan du anropa Azure [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) från din kod. ADAL tillhandahåller ett API för att autentisera med Azure AD från ditt program. Registrera ditt program kan du också tillåta anrop från programmet till Azure Storage-API: er med en åtkomst-token.

När du registrerar ditt program kan ange du information om ditt program till Azure AD. Azure AD sedan innehåller ett klient-ID (kallas även en *program-ID*) att du använder för att associera ditt program med Azure AD under körning. Läs mer om klient-ID i [program och tjänstens huvudnamn objekt i Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md).

Om du vill registrera ditt Azure Storage-program, följer du stegen i den [lägga till ett program](../../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) i avsnittet [integrera program med Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Om du registrerar ditt program som det ursprungliga programmet kan du ange en giltig URI för den **omdirigerings-URI**. Värdet behöver inte vara en verklig slutpunkt.

![Skärmbild som visar hur du registrerar storage-program med Azure AD](./media/storage-auth-aad-app/app-registration.png)

När du har registrerat ditt program visas program-ID (eller klient-ID) under **inställningar**:

![Skärmbild som visar klient-ID](./media/storage-auth-aad-app/app-registration-client-id.png)

Mer information om hur du registrerar ett program med Azure AD finns [integrera program med Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Bevilja behörighet registrerade app till Azure Storage

Därefter måste du ge ditt program behörighet att anropa API: erna för Azure-lagring. Det här steget gör att programmet kan tillåta anrop till Azure Storage med Azure AD.

1. I det vänstra navigeringsfönstret i Azure portal väljer **alla tjänster**, och Sök efter **App registreringar**.
2. Sök efter namnet på det registrerade programmet som du skapade i föregående steg.
3. Välj appen registrerade och klicka på **inställningar**. I den **API-åtkomst** väljer **nödvändiga behörigheter**.
4. I den **nödvändiga behörigheter** bladet, klickar du på den **Lägg till** knappen.
5. Under **väljer en API**, söka efter ”Azure Storage” och välj **Azure Storage** från listan över resultat.

    ![Skärmbild som visar behörigheter för lagring](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Under **Välj behörigheter**, markera kryssrutan bredvid **åtkomst till Azure Storage**, och klicka på **Välj**.
7. Klicka på **Klar**.

Den **nödvändiga behörigheter** windows visas nu som din Azure AD-programmet har åtkomst till både Azure Active Directory och Azure Storage. Behörigheter till Azure AD automatiskt när du först registrera din app med Azure AD.

![Skärmbild som visar registrera appbehörigheter](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: skapa en blockblobb

Exemplet visar hur du hämta en token från Azure AD. Åtkomst-token används för att autentisera den angivna användaren och auktorisera en begäran om att skapa en blockblob. Om du vill att det här exemplet ska fungera, följer du stegen som beskrivs i föregående avsnitt.

> [!NOTE]
> Som en ägare av Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Du måste uttryckligen tilldela dig en RBAC roll för Azure Storage. Du kan tilldela den för din prenumeration, resursgrupp, storage-konto eller behållare eller kön. 
>
> Till exempel om du vill köra exempelkod för ett lagringskonto där du har en ägare och på din egen användaridentitet, måste du tilldela rollen RBAC för Blob-Data deltagare själv. Annars misslyckas anropet för att skapa blob med HTTP-statuskod 403 (förbjuden). Mer information finns i [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Välkända värden för autentisering med Azure AD

Om du vill autentisera ett säkerhetsobjekt med Azure AD, måste du inkludera vissa välkända värden i koden.

#### <a name="azure-ad-oauth-endpoint"></a>Azure AD OAuth-slutpunkten

Basen Azure AD myndighet slutpunkt för OAuth 2.0 är följande, där *klient-id* Active Directory klient-ID (eller katalog-ID):

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

Klient-ID identifierar Azure AD-klienten ska användas för autentisering. Om du vill hämta klient-ID, Följ stegen som beskrivs i **hämta klient-ID för Azure Active Directory**.

#### <a name="storage-resource-id"></a>Storage-resurs-ID

Använd Azure Storage-resurs-ID för att hämta en token för att autentisera förfrågningar till Azure Storage:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Hämta klient-ID för Azure Active Directory

Följ dessa steg för att hämta klient-ID:

1. Välj din Active Directory i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Kopiera GUID-värde som angetts för den **katalog-ID**. Det här värdet kallas även för klient-ID.

![Skärmbild som visar hur du kopierar klient-ID](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Lägg till referenser och med-uttryck  

Installera förhandsversionen av Azure Storage-klientbiblioteket i Visual Studio. Från den **verktyg** väljer du **Nuget Package Manager**, sedan **Pakethanterarkonsolen**. Skriv följande kommando i konsolen:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Lägg till följande using-instruktioner till din kod:

```dotnet
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Hämta en OAuth-token från Azure AD

Lägg till en metod som begär en token från Azure AD. Om du vill begära token ring den [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metod.

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/"; // Storage resource endpoint
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token"; // Azure AD OAuth endpoint
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
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

Slutligen använder åtkomsttoken för att skapa nya autentiseringsuppgifter för lagring och använder dessa autentiseringsuppgifter för att skapa blob:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Azure AD-integrering med Azure Storage kräver att du använder HTTPS för Azure Storage-åtgärder.

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).
- Läs om hur du använder hanterade tjänstidentiteten med Azure Storage i [autentisera med Azure AD från en Azure hanterade tjänstidentiteten (förhandsgranskning)](storage-auth-aad-msi.md).
- Om du vill lära dig mer om att logga in på Azure CLI och PowerShell med en Azure AD-identitet, se [använder en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsgranskning)](storage-auth-aad-script.md).
- Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg inlägg, [om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



