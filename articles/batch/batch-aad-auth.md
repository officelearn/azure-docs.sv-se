---
title: Autentisera Azure Batch-tjänster med Azure Active Directory
description: Batch stöder Azure AD för autentisering från batch-tjänsten. Läs om hur du autentiserar på ett av två sätt.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: labrenne
ms.openlocfilehash: f56c05f64086ac2e98e69d6b21fae7a0a63b5006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019527"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autentisera batchservicelösningar med Active Directory

Azure Batch stöder autentisering med [Azure Active Directory][aad_about] (Azure AD). Azure AD är Microsofts molnbaserade katalog- och identitetshanteringstjänst för flera innehavare. Azure använder azure ad för att autentisera sina kunder, tjänstadministratörer och organisationsanvändare.

När du använder Azure AD-autentisering med Azure Batch kan du autentisera på ett av två sätt:

- Genom att använda **integrerad autentisering** för att autentisera en användare som interagerar med programmet. Ett program med integrerad autentisering samlar in en användares autentiseringsuppgifter och använder dessa autentiseringsuppgifter för att autentisera åtkomst till batchresurser.
- Genom att använda ett **tjänsthuvudnamn** för att autentisera ett obevakat program. Ett tjänsthuvudnamn definierar principen och behörigheterna för ett program för att representera programmet när du öppnar resurser vid körning.

Mer information om Azure AD finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Slutpunkter för autentisering

Om du vill autentisera batchprogram med Azure AD måste du inkludera några välkända slutpunkter i koden.

### <a name="azure-ad-endpoint"></a>Slutpunkt för Azure AD

Slutpunkten för Azure AD-auktoritet är:

`https://login.microsoftonline.com/`

Om du vill autentisera med Azure AD använder du den här slutpunkten tillsammans med klient-ID (katalog-ID). Klient-ID identifierar Azure AD-klienten som ska användas för autentisering. Så här hämtar du klient-ID:et i [Hämta klient-ID för din Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Den klientspecifika slutpunkten krävs när du autentiserar med hjälp av ett tjänsthuvudnamn. 
> 
> Den klientspecifika slutpunkten är valfri när du autentiserar med integrerad autentisering, men rekommenderas. Du kan dock också använda den gemensamma slutpunkten för Azure AD. Den gemensamma slutpunkten tillhandahåller ett allmänt autentiseringsgränssnitt när en viss klient inte tillhandahålls. Den gemensamma slutpunkten är `https://login.microsoftonline.com/common`.
>
>

Mer information om Azure AD-slutpunkter finns i [Autentiseringsscenarier för Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Slutpunkt för batchresurs

Använd **slutpunkten för Azure Batch-resurs** för att hämta en token för att autentisera begäranden till batch-tjänsten:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrera din ansökan hos en klient

Det första steget i att använda Azure AD för att autentisera är att registrera ditt program i en Azure AD-klientorganisation. Om du registrerar ditt program kan du anropa Azure [Active Directory Authentication Library][aad_adal] (ADAL) från din kod. ADAL tillhandahåller ett API för autentisering med Azure AD från ditt program. Registrering av ditt program krävs oavsett om du planerar att använda integrerad autentisering eller ett huvudnamn för tjänsten.

När du registrerar ditt program anger du information om ditt program till Azure AD. Azure AD tillhandahåller sedan ett program-ID (kallas även *klient-ID)* som du använder för att associera ditt program med Azure AD vid körning. Mer information om program-ID finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Om du vill registrera ditt Batch-program följer du anvisningarna i avsnittet [Lägga till ett program](../active-directory/develop/quickstart-register-app.md) i Integrering av program med Azure Active [Directory][aad_integrate]. Om du registrerar ditt program som ett inbyggt program kan du ange en giltig URI för **Redirect URI**. Det behöver inte vara en riktig slutpunkt.

När du har registrerat ditt program visas program-ID:t:

![Registrera ditt batchprogram med Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Mer information om hur du registrerar ett program med Azure AD finns i [Autentiseringsscenarier för Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Hämta klient-ID:et för Active Directory

Klient-ID identifierar Azure AD-klienten som tillhandahåller autentiseringstjänster till ditt program. Så här hämtar du klient-ID:et:

1. Välj Active Directory i Azure-portalen.
1. Välj **egenskaper**.
1. Kopiera GUID-värdet som angetts för **katalog-ID**. Det här värdet kallas också klient-ID.

![Kopiera katalog-ID:et](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Använda integrerad autentisering

Om du vill autentisera med integrerad autentisering måste du bevilja dina programbehörigheter för att ansluta till batch-tjänst-API:et. Det här steget gör det möjligt för ditt program att autentisera anrop till batch-tjänst-API:et med Azure AD.

När du har registrerat ditt program följer du dessa steg i Azure-portalen för att ge det åtkomst till batch-tjänsten:

1. Välj **Alla tjänster**i det vänstra navigeringsfönstret i Azure-portalen . Välj **Appregistreringar**.
1. Sök efter namnet på ditt program i listan över appregistreringar:

    ![Sök efter ditt programnamn](./media/batch-aad-auth/search-app-registration.png)

1. Välj programmet och välj **API-behörigheter**.
1. I avsnittet **API-behörigheter** väljer du **Lägg till en behörighet**.
1. Sök efter batch-API:et i **Välj ett API.** Sök efter var och en av de här strängarna tills du hittar API:t:
    1. **Microsoft Azure-batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** är id:t för API:t.
1. När du har hittat batch-API:et väljer du det och väljer **Välj**.
1. I **Välj behörigheter**markerar du kryssrutan bredvid Åtkomst till **Azure Batch Service** och väljer sedan Lägg till **behörigheter**.

**Api-behörighetsavsnittet** visar nu att ditt Azure AD-program har åtkomst till både Microsoft Graph och batch-tjänst-API:et. Behörigheter beviljas Microsoft Graph automatiskt när du först registrerar din app med Azure AD.

![Bevilja API-behörigheter](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Använda ett huvudnamn för tjänsten

Om du vill autentisera ett program som körs obevakat använder du ett huvudnamn för tjänsten. När du har registrerat ditt program följer du dessa steg i Azure-portalen för att konfigurera ett huvudnamn för tjänsten:

1. Begär en hemlighet för din ansökan.
1. Tilldela rollbaserad åtkomstkontroll (RBAC) till ditt program.

### <a name="request-a-secret-for-your-application"></a>Begär en hemlighet för din ansökan

När ditt program autentiseras med ett tjänsthuvudnamn skickas både program-ID:n och en hemlighet till Azure AD. Du måste skapa och kopiera den hemliga nyckeln som ska användas från koden.

Följ dessa steg i Azure-portalen:

1. Välj **Alla tjänster**i det vänstra navigeringsfönstret i Azure-portalen . Välj **Appregistreringar**.
1. Välj ditt program i listan över appregistreringar.
1. Markera programmet och välj sedan **Certifikat & hemligheter**. I avsnittet **Klienthemligheter** väljer du **Ny klienthemlighet**.
1. Om du vill skapa en hemlighet anger du en beskrivning av hemligheten. Välj sedan en förfallodatum för hemligheten med antingen ett år, två år eller ingen förfallodatum..
1. Välj **Lägg till** för att skapa och visa hemligheten. Kopiera det hemliga värdet till en säker plats, eftersom du inte kan komma åt det igen när du lämnar sidan.

    ![Skapa en hemlig nyckel](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Tilldela RBAC till ditt program

Om du vill autentisera med ett huvudnamn för tjänsten måste du tilldela RBAC till ditt program. Följ de här stegen:

1. I Azure-portalen navigerar du till batchkontot som används av ditt program.
1. I avsnittet **Inställningar** i batchkontot väljer du **Åtkomstkontroll (IAM)**.
1. Välj fliken **Rolltilldelningar.**
1. Välj **Lägg till rolltilldelning**.
1. Välj rollen *Deltagare* eller *Läsare* för ditt program i listrutan **Roll.** Mer information om dessa roller finns [i Komma igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).  
1. Ange namnet på programmet i fältet **Välj.** Välj ditt program i listan och välj sedan **Spara**.

Ditt program ska nu visas i inställningarna för åtkomstkontroll med en RBAC-roll tilldelad.

![Tilldela en RBAC-roll till ditt program](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Tilldela en anpassad roll

En anpassad roll ger detaljerad behörighet till en användare för att skicka jobb, uppgifter med mera. Detta ger möjlighet att förhindra att användare utför åtgärder som påverkar kostnaden, till exempel skapa pooler eller ändra noder.

Du kan använda en anpassad roll för att bevilja behörigheter till en Azure AD-användare, grupp eller tjänsthuvudnamn för följande RBAC-åtgärder:

- Microsoft.Batch/batchKonton/pooler/skrivning
- Microsoft.Batch/batchKonton/pooler/borttagning
- Microsoft.Batch/batchKonton/pooler/läsa
- Microsoft.Batch/batchKonton/jobbPlaner/skrivning
- Microsoft.Batch/batchKonto/jobbSchedules/delete
- Microsoft.Batch/batchKonto/jobbSchedules/read
- Microsoft.Batch/batchKonton/jobb/skrivning
- Microsoft.Batch/batchKonton/jobb/borttagning
- Microsoft.Batch/batchKonton/jobb/läsning
- Microsoft.Batch/batchKonton/certifikat/skrivning
- Microsoft.Batch/batchKonton/certifikat/borttagning
- Microsoft.Batch/batchKonton/certifikat/läsa
- Microsoft.Batch/batchKonton/läsa (för alla läsåtgärder)
- Microsoft.Batch/batchKonton/listKeys/åtgärd (för alla åtgärder)

Anpassade roller är för användare som autentiseras av Azure AD, inte batchkontouppgifterna (delad nyckel). Observera att batchkontouppgifterna ger fullständig behörighet till batchkontot. Observera också att jobb som använder automatiskpool kräver behörighet på poolnivå.

Här är ett exempel på en anpassad rolldefinition:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Mer allmän information om hur du skapar en anpassad roll finns i [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Hämta klient-ID:et för din Azure Active Directory

Klient-ID identifierar Azure AD-klienten som tillhandahåller autentiseringstjänster till ditt program. Så här hämtar du klient-ID:et:

1. Välj Active Directory i Azure-portalen.
1. Välj **egenskaper**.
1. Kopiera GUID-värdet som angetts för **katalog-ID**. Det här värdet kallas också klient-ID.

![Kopiera katalog-ID:et](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Kodexempel

Kodexemplen i det här avsnittet visar hur du autentiserar med Azure AD med integrerad autentisering och med ett tjänsthuvudnamn. De flesta av dessa kodexempel använder .NET, men begreppen är liknande för andra språk.

> [!NOTE]
> En Azure AD-autentiseringstoken upphör att gälla efter en timme. När du använder ett långlivat **BatchClient-objekt** rekommenderar vi att du hämtar en token från ADAL på varje begäran för att säkerställa att du alltid har en giltig token. 
>
>
> För att uppnå detta i .NET skriver du en metod som hämtar token från Azure AD och skickar den metoden till ett **BatchTokenCredentials-objekt** som ombud. Ombudsmetoden anropas på varje begäran till batch-tjänsten för att säkerställa att en giltig token tillhandahålls. Som standard cachelagrar ADAL token, så en ny token hämtas från Azure AD endast när det behövs. Mer information om token i Azure AD finns i [Autentiseringsscenarier för Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Kodexempel: Använda Azure AD-integrerad autentisering med batch .NET

Om du vill autentisera med integrerad autentisering från Batch .NET refererar du till [Azure Batch .NET-paketet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) och [ADAL-paketet.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Inkludera följande `using` satser i koden:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referera till Azure AD-slutpunkten i din kod, inklusive klient-ID. Så här hämtar du klient-ID:et i [Hämta klient-ID för din Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referera till slutpunkten för batch-tjänstresurser:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till ditt batchkonto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID:et är tillgängligt från din appregistrering i Azure-portalen:

```csharp
private const string ClientId = "<application-id>";
```

Kopiera även den omdirigera URI som du angav om du har registrerat programmet som ett inbyggt program. Den omdirigerings-URI som anges i koden måste matcha den omdirigera URI som du angav när du registrerade programmet:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Skriv en motringningsmetod för att hämta autentiseringstoken från Azure AD. **GetAuthenticationTokenAsync** motringningsmetoden som visas här anropar ADAL för att autentisera en användare som interagerar med programmet. Metoden **AcquireTokenAsync** som tillhandahålls av ADAL uppmanar användaren att ange sina autentiseringsuppgifter och programmet fortsätter när användaren tillhandahåller dem (såvida det inte redan har cachelagrat autentiseringsuppgifter):

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

Skapa ett **BatchTokenCredentials-objekt** som tar ombudet som en parameter. Använd dessa autentiseringsuppgifter för att öppna ett **BatchClient-objekt.** Du kan använda **batchclient-objektet** för efterföljande åtgärder mot batch-tjänsten:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Kodexempel: Använda ett Azure AD-tjänsthuvudnamn med Batch .NET

Om du vill autentisera med ett tjänsthuvudnamn från Batch .NET refererar du till [Azure Batch .NET-paketet](https://www.nuget.org/packages/Azure.Batch/) och [ADAL-paketet.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Inkludera följande `using` satser i koden:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referera till Azure AD-slutpunkten i din kod, inklusive klient-ID. När du använder ett tjänsthuvudnamn måste du ange en klientspecifik slutpunkt. Så här hämtar du klient-ID:et i [Hämta klient-ID för din Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referera till slutpunkten för batch-tjänstresurser:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till ditt batchkonto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID:et är tillgängligt från din appregistrering i Azure-portalen:

```csharp
private const string ClientId = "<application-id>";
```

Ange den hemliga nyckeln som du kopierade från Azure-portalen:

```csharp
private const string ClientKey = "<secret-key>";
```

Skriv en motringningsmetod för att hämta autentiseringstoken från Azure AD. **GetAuthenticationTokenAsync** motringningsmetoden som visas här anropar ADAL för obevakad autentisering:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Skapa ett **BatchTokenCredentials-objekt** som tar ombudet som en parameter. Använd dessa autentiseringsuppgifter för att öppna ett **BatchClient-objekt.** Använd sedan det **BatchClient-objektet** för efterföljande åtgärder mot batch-tjänsten:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Kodexempel: Använda ett Azure AD-tjänsthuvudnamn med Batch Python

Om du vill autentisera med ett tjänsthuvudnamn från Batch Python installerar och refererar du till [azure-batch-](https://pypi.org/project/azure-batch/) och [azure-common-modulerna.](https://pypi.org/project/azure-common/)

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

När du använder ett tjänsthuvudnamn måste du ange klient-ID. Så här hämtar du klient-ID:et i [Hämta klient-ID för din Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```python
TENANT_ID = "<tenant-id>"
```

Referera till slutpunkten för batch-tjänstresurser:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Referera till ditt batchkonto:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Ange program-ID (klient-ID) för ditt program. Program-ID:et är tillgängligt från din appregistrering i Azure-portalen:

```python
CLIENT_ID = "<application-id>"
```

Ange den hemliga nyckeln som du kopierade från Azure-portalen:

```python
SECRET = "<secret-key>"
```

Skapa ett **ServicePrincipalCredentials-objekt:**

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Använd tjänstens huvudreferenser för att öppna ett **BatchServiceClient-objekt.** Använd sedan det **BatchServiceClient-objektet** för efterföljande åtgärder mot batch-tjänsten.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure AD finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/). Djupgående exempel som visar hur du använder ADAL är tillgängliga i [Azure Code Samples-biblioteket.](https://azure.microsoft.com/resources/samples/?service=active-directory)

- Mer information om tjänsthuvudnamn finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Information om hur du skapar ett huvudnamn för tjänsten med Azure-portalen finns i [Använda portal för att skapa Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md). Du kan också skapa ett tjänsthuvudnamn med PowerShell eller Azure CLI.

- Information om hur du autentiserar batchhanteringsprogram med Azure AD finns i [Autentisera batchhanteringslösningar med Active Directory](batch-aad-auth-management.md).

- Ett Python-exempel på hur du skapar en batchklient autentiserad med en Azure AD-token finns i [distribuera Azure Batch Custom Image med ett Python Script-exempel.](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[azure_portal]: https://portal.azure.com
