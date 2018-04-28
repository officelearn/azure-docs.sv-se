---
title: Använda Azure Active Directory för att autentisera Azure Batch tjänstelösningar | Microsoft Docs
description: Batch har stöd för Azure AD för autentisering från Batch-tjänsten.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: danlep
ms.openlocfilehash: c28af5a9773cc362663831346b58f599aed6ea9a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autentisera Batch tjänstelösningar med Active Directory

Azure Batch stöder autentisering med [Azure Active Directory] [ aad_about] (Azure AD). Azure AD är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Azure själva använder Azure AD för att autentisera sina kunder, administratörer och organisationens användare.

När du använder Azure AD-autentisering med Azure Batch kan du autentisera i ett av två sätt:

- Med hjälp av **integrerad autentisering** att autentisera en användare interagerar med programmet. Ett program med integrerad autentisering samlar in en användares autentiseringsuppgifter och använder dessa autentiseringsuppgifter för att autentisera åtkomst till Batch-resurser.
- Med hjälp av en **tjänstens huvudnamn** att autentisera en oövervakad. Ett huvudnamn för tjänsten definierar principer och behörigheter för ett program för att kunna representera programmet vid åtkomst till resurser vid körning.

Mer information om Azure AD finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Slutpunkter för autentisering

Om du vill autentisera Batch-program med Azure AD, måste du inkludera vissa välkända slutpunkter i koden.

### <a name="azure-ad-endpoint"></a>Azure AD-slutpunkt

Basen Azure AD myndighet slutpunkten är:

`https://login.microsoftonline.com/`

Om du vill autentisera med Azure AD, kan du använda den här slutpunkten tillsammans med klient-ID (katalog-ID). Klient-ID identifierar Azure AD-klienten ska användas för autentisering. Om du vill hämta klient-ID, Följ stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Klient-specifika slutpunkten krävs när du autentiserar med hjälp av ett huvudnamn för tjänsten. 
> 
> Klient-specifika slutpunkten är valfritt när du autentiserar med integrerad autentisering, men rekommenderas. Du kan också använda den vanliga Azure AD-slutpunkten. Vanliga slutpunkten ger en allmän referens samla in gränssnittet när en viss klient inte har angetts. Vanliga slutpunkten är `https://login.microsoftonline.com/common`.
>
>

Läs mer om Azure AD-slutpunkter [Autentiseringsscenarier för Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Slutpunkten för batch-resurs

Använd den **Azure Batch resurs endpoint** att hämta en token för att autentisera förfrågningar till Batch-tjänsten:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrera ditt program till en klient

Det första steget i att använda Azure AD för autentisering är registrera ditt program i en Azure AD-klient. Registrera ditt program kan du anropa Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) från din kod. ADAL tillhandahåller ett API för att autentisera med Azure AD från ditt program. Registrera ditt program krävs om du planerar att använda integrerad autentisering eller ett huvudnamn för tjänsten.

När du registrerar ditt program kan ange du information om ditt program till Azure AD. Azure AD sedan innehåller ett program-ID (kallas även en *klient-ID*) att du använder för att associera ditt program med Azure AD under körning. Mer information om program-ID finns [program och tjänstens huvudnamn objekt i Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Om du vill registrera din Batch-program, följer du stegen i den [lägga till ett program](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) i avsnittet [integrera program med Azure Active Directory][aad_integrate]. Om du registrerar ditt program som det ursprungliga programmet kan du ange en giltig URI för den **omdirigerings-URI**. Det behöver inte vara en verklig slutpunkt.

När du har registrerat ditt program, ser du det program-ID:

![Registrera ditt Batch-program med Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Mer information om hur du registrerar ett program med Azure AD finns [Autentiseringsscenarier för Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Hämta klient-ID för ditt Active Directory

Klient-ID identifierar Azure AD-klient som tillhandahåller autentiseringstjänster för ditt program. Följ dessa steg för att hämta klient-ID:

1. Välj din Active Directory i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Kopiera GUID-värde som angetts för den **katalog-ID**. Det här värdet kallas även för klient-ID.

![Kopiera katalog-ID](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Använda integrerad autentisering

Du måste ge ditt program behörighet att ansluta till API för Batch-tjänsten för att autentisera med integrerad autentisering. Det här steget gör att programmet kan autentisera anrop till API för Batch-tjänsten med Azure AD.

När du har [registrerade programmet](#register-your-application-with-an-azure-ad-tenant), Följ dessa steg i Azure portal för att bevilja åtkomst till Batch-tjänsten:

1. I det vänstra navigeringsfönstret i Azure portal väljer **alla tjänster**. Klicka på **App registreringar**.
2. Sök efter namnet på programmet i listan över app registreringar:

    ![Sök efter programnamnet](./media/batch-aad-auth/search-app-registration.png)

3. Klicka på programmet och på **inställningar**. I den **API-åtkomst** väljer **nödvändiga behörigheter**.
4. I den **nödvändiga behörigheter** bladet, klickar du på den **Lägg till** knappen.
5. I **väljer en API**, Sök efter Batch-API. Sök efter var och en av de här strängarna tills du hittar API:t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Nyare Azure AD-klientorganisationer kan använda det här namnet.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** är id:t för API:t. 
6. När du har hittat Batch-API och på **Välj**.
7. I **Välj behörigheter**, markera kryssrutan bredvid **Azure Batch-tjänsten för dataåtkomst** och på **Välj**.
8. Klicka på **Klar**.

Den **nödvändiga behörigheter** windows nu visar att din Azure AD-program har åtkomst till både ADAL och batchen service API. Behörigheter som ADAL automatiskt när du först registrera din app med Azure AD.

![Bevilja API-behörigheter](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Använd ett huvudnamn för tjänsten 

Om du vill autentisera ett program som körs obevakad, kan du använda ett huvudnamn för tjänsten. När du har registrerat ditt program, Följ dessa steg i Azure portal för att konfigurera ett huvudnamn för tjänsten:

1. Begär en hemlig nyckel för ditt program.
2. Tilldela en RBAC-roll i tillämpningsprogrammet.

### <a name="request-a-secret-key-for-your-application"></a>Begär en hemlig nyckel för ditt program

När programmet autentiseras med en tjänst som skickar både program-ID och en hemlig nyckel till Azure AD. Du måste skapa och kopiera den hemliga nyckeln ska användas från din kod.

Följ dessa steg i Azure-portalen:

1. I det vänstra navigeringsfönstret i Azure portal väljer **alla tjänster**. Klicka på **App registreringar**.
2. Sök efter namnet på programmet i listan över app registreringar.
3. Klicka på programmet och på **inställningar**. I den **API-åtkomst** väljer **nycklar**.
4. Ange en beskrivning av nyckeln för att skapa en nyckel. Välj sedan en varaktighet för nyckeln för en eller två år. 
5. Klicka på den **spara** för att skapa och visa nyckeln. Kopiera värdet för nyckeln till en säker plats som du inte åtkomst till den igen när du lämnar bladet. 

    ![Skapa en hemlig nyckel](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Tilldela en RBAC roll till ditt program

För att autentisera med en tjänstens huvudnamn, måste du tilldela en RBAC-roll i tillämpningsprogrammet. Följ de här stegen:

1. Navigera till Batch-kontot som används av ditt program i Azure-portalen.
2. I den **inställningar** bladet för Batch-kontot väljer **Access Control (IAM)**.
3. Klicka på knappen **Lägg till**. 
4. Från den **rollen** listrutan och välj någon av _deltagare_ eller _Reader_ roll för ditt program. Mer information om dessa roller finns [Kom igång med rollbaserad åtkomstkontroll i Azure portal](../role-based-access-control/overview.md).  
5. I den **Välj** anger du namnet på ditt program. Markera programmet i listan och klickar på **spara**.

Ditt program bör nu visas i dina inställningar för åtkomstkontroll med en RBAC-roll som tilldelats. 

![Tilldela en RBAC roll till ditt program](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Hämta klient-ID för Azure Active Directory

Klient-ID identifierar Azure AD-klient som tillhandahåller autentiseringstjänster för ditt program. Följ dessa steg för att hämta klient-ID:

1. Välj din Active Directory i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Kopiera GUID-värde som angetts för den **katalog-ID**. Det här värdet kallas även för klient-ID.

![Kopiera katalog-ID](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Kodexempel

Kodexemplen i det här avsnittet visar hur du autentisera med Azure AD med hjälp av integrerad autentisering och ett huvudnamn för tjänsten. De flesta av dessa kodexempel använda .NET, men begrepp som är liknande för andra språk.

> [!NOTE]
> En Azure AD authentication token upphör att gälla efter en timme. När du använder en långlivade **BatchClient** objekt, rekommenderar vi att du hämtar en token från ADAL för varje begäran att säkerställa att du alltid har en giltig token. 
>
>
> För att uppnå i .NET skriva en metod som hämtar en token från Azure AD och överför denna metod för att en **BatchTokenCredentials** objektet som ett ombud. Delegate-metoden anropas för varje begäran att Batch-tjänsten för att säkerställa att en giltig token. Som standard cachelagrar ADAL-token så att en ny token hämtas från Azure AD bara när det behövs. Mer information om token i Azure AD finns [Autentiseringsscenarier för Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exempel: använda Azure AD-integrerad autentisering med Batch .NET

Om du vill autentisera med integrerad autentisering från Batch .NET, referera till den [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) paketet och [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) paketet.

Inkludera följande `using` instruktioner i koden:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referens för Azure AD-slutpunkt i din kod, inklusive klient-ID. Om du vill hämta klient-ID, Följ stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Resursen tjänstslutpunkten Batch-referens:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till Batch-kontot:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID är tillgänglig från din appregistrering i Azure-portalen:

```csharp
private const string ClientId = "<application-id>";
```

Kopiera omdirigerings-URI som du angav även om du har registrerat ditt program som det ursprungliga programmet. Omdirigerings-URI som angetts i din kod måste matcha omdirigerings-URI som du angav när du registrerade programmet:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Skriva en metod för att hämta autentiseringstoken från Azure AD. Den **GetAuthenticationTokenAsync** Återanropsmetoden som visas här anrop ADAL att autentisera en användare som interagerar med programmet. Den **AcquireTokenAsync** metod som tillhandahålls av ADAL efterfrågar sina autentiseringsuppgifter och programmet fortsätter när användaren anger dem (om det redan har cachelagrade autentiseringsuppgifter):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Skapa en **BatchTokenCredentials** objekt som tar ombudet som en parameter. Använd dessa autentiseringsuppgifter för att öppna en **BatchClient** objekt. Du kan använda som **BatchClient** objekt för efterföljande åtgärder mot Batch-tjänsten:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exempel: med hjälp av en Azure AD-tjänstens huvudnamn med Batch .NET

Om du vill autentisera med ett huvudnamn för tjänsten från Batch .NET, referera till den [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) paketet och [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) paketet.

Inkludera följande `using` instruktioner i koden:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referens för Azure AD-slutpunkt i din kod, inklusive klient-ID. Du måste ange en slutpunkt för klienten när du använder ett huvudnamn för tjänsten. Om du vill hämta klient-ID, Följ stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Resursen tjänstslutpunkten Batch-referens:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till Batch-kontot:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID är tillgänglig från din appregistrering i Azure-portalen:

```csharp
private const string ClientId = "<application-id>";
```

Ange den hemliga nyckeln som du kopierade från Azure portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Skriva en metod för att hämta autentiseringstoken från Azure AD. Den **GetAuthenticationTokenAsync** Återanropsmetoden som visas här anrop ADAL för obevakad autentisering:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Skapa en **BatchTokenCredentials** objekt som tar ombudet som en parameter. Använd dessa autentiseringsuppgifter för att öppna en **BatchClient** objekt. Använd som **BatchClient** objekt för efterföljande åtgärder mot Batch-tjänsten:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Exempel: med hjälp av en Azure AD-tjänstens huvudnamn med Batch Python

Om du vill autentisera med ett huvudnamn för tjänsten från Batch Python, installera och referera till den [azure batch](https://pypi.org/project/azure-batch/) och [azure gemensamma](https://pypi.org/project/azure-common/) moduler.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

När du använder ett huvudnamn för tjänsten, måste du ange klient-ID. Om du vill hämta klient-ID, Följ stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Resursen tjänstslutpunkten Batch-referens:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Referera till Batch-kontot:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID är tillgänglig från din appregistrering i Azure-portalen:

```python
CLIENT_ID = "<application-id>";
```

Ange den hemliga nyckeln som du kopierade från Azure portal:

```python
SECRET = "<secret-key>";
```

Skapa en **ServicePrincipalCredentials** objekt:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Använda service principal autentiseringsuppgifter för att öppna en **BatchServiceClient** objekt. Använd som **BatchServiceClient** objekt för efterföljande åtgärder mot Batch-tjänsten.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure AD finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/). Djupgående exempel som visar hur du använder ADAL finns i den [Azure kodexempel](https://azure.microsoft.com/resources/samples/?service=active-directory) bibliotek.

* Läs mer om tjänstens huvudnamn i [program och tjänstens huvudnamn objekt i Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Om du vill skapa ett huvudnamn för tjänsten med hjälp av Azure portal finns [använda portalen för att skapa Active Directory applikationen eller tjänsten säkerhetsobjekt som kan komma åt resurser](../resource-group-create-service-principal-portal.md). Du kan också skapa ett huvudnamn för tjänsten med PowerShell eller Azure CLI.

* För att autentisera Batch-hantering av program med Azure AD finns [autentiserar Batch hanteringslösningar med Active Directory](batch-aad-auth-management.md).

* En Python-exempel på hur du skapar en Batch-klient som autentiseras med en Azure AD-token finns i [distribuerar Azure Batch anpassad avbildning med Python-skriptet](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) exempel.

[aad_about]: ../active-directory/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[azure_portal]: http://portal.azure.com
