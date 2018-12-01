---
title: Använd Azure Active Directory för att autentisera lösningar för Azure Batch service | Microsoft Docs
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
ms.openlocfilehash: 964ef3bd988d71bfb90dbaa473b4b259769064b3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722063"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autentisera lösningar för Batch-tjänsten med Active Directory

Azure Batch stöder autentisering med [Azure Active Directory] [ aad_about] (Azure AD). Azure AD är Microsofts flera innehavare molnbaserad katalog- och identity management-tjänsten. Azure själva använder Azure AD för att autentisera dess kunder, administratörer och användare i organisationer.

När du använder Azure AD-autentisering med Azure Batch kan autentisera du på något av två sätt:

- Med hjälp av **integrerad autentisering** att autentisera en användare som interagerar med programmet. Ett program med integrerad autentisering samlar in en användares autentiseringsuppgifter och använder dessa autentiseringsuppgifter för att autentisera åtkomst till Batch-resurser.
- Med hjälp av en **tjänstens huvudnamn** att autentisera övervakade program. Ett huvudnamn för tjänsten definierar principen och behörigheterna för ett program för att representera programmet vid åtkomst till resurser vid körning.

Läs mer om Azure AD i den [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Slutpunkter för autentisering

För att autentisera Batch-program med Azure AD, måste du inkludera vissa välkända slutpunkter i din kod.

### <a name="azure-ad-endpoint"></a>Azure AD-slutpunkt

Grundläggande Azure AD-utfärdare slutpunkten:

`https://login.microsoftonline.com/`

Om du vill autentisera med Azure AD måste använda du den här slutpunkten tillsammans med klient-ID (katalog-ID). Klient-ID identifierar Azure AD-klient ska användas för autentisering. Om du vill hämta klient-ID, följer du stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Klientspecifik slutpunkten är obligatorisk när du autentiserar med hjälp av ett huvudnamn för tjänsten. 
> 
> Klientspecifik slutpunkten är valfritt när du autentiserar med integrerad autentisering, men rekommenderas. Du kan också använda den vanliga Azure AD-slutpunkten. Vanliga slutpunkten ger en allmän referens samla in gränssnittet när en specifik klient inte har angetts. Vanliga slutpunkten är `https://login.microsoftonline.com/common`.
>
>

Läs mer om Azure AD-slutpunkter, [Autentiseringsscenarier för Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Batch resursslutpunkt

Använd den **Azure Batch resursslutpunkt** att hämta en token för att autentisera begäranden till Batch-tjänsten:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrera ditt program med en klient

Det första steget i att autentisera med hjälp av Azure AD är att registrera ditt program i en Azure AD-klient. Registrera ditt program kan du anropa Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) från din kod. ADAL tillhandahåller ett API för att autentisera med Azure AD från ditt program. Registrera ditt program måste anges om du planerar att använda integrerad autentisering eller ett huvudnamn för tjänsten.

När du registrerar ditt program kan ange du information om ditt program till Azure AD. Sedan Azure AD tillhandahåller ett program-ID (även kallat en *klient-ID*) att du använder för att associera ditt program med Azure AD vid körning. Läs mer om program-ID i [program och tjänstobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Om du vill registrera ett Batch-program, följer du stegen i den [lägga till ett program](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) i avsnittet [integrera program med Azure Active Directory][aad_integrate]. Om du registrerar ditt program som ett internt program, kan du ange en giltig URI för den **omdirigerings-URI**. Det behöver inte vara en verklig slutpunkt.

När du har registrerat ditt program visas det program-ID:

![Registrera ditt Batch-program med Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Mer information om hur du registrerar ett program med Azure AD finns i [Autentiseringsscenarier för Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Hämta klient-ID för ditt Active Directory

Klient-ID identifierar Azure AD-klienten som tillhandahåller autentiseringstjänster för ditt program. Följ dessa steg om du vill hämta klient-ID:

1. Välj din Active Directory i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Kopiera GUID-värdet som angetts för den **katalog-ID**. Det här värdet kallas även för klient-ID.

![Kopiera katalog-ID](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Använda integrerad autentisering

Du måste ge ditt program behörighet att ansluta till API: et för Batch-tjänsten för att autentisera med integrerad autentisering. Det här steget gör att programmet att autentisera anrop till API: et för Batch-tjänsten med Azure AD.

När du har [registrerat ditt program](#register-your-application-with-an-azure-ad-tenant), följa stegen i Azure portal för att ge det åtkomst till Batch-tjänsten:

1. I det vänstra navigeringsfönstret i Azure-portalen, väljer **alla tjänster**. Klicka på **Appregistreringar**.
2. Sök efter namnet på ditt program i listan över app-registreringar:

    ![Sök efter namnet på appen](./media/batch-aad-auth/search-app-registration.png)

3. Klicka på programmet och på **inställningar**. I den **API-åtkomst** väljer **behörigheter som krävs för**.
4. I den **nödvändiga behörigheter** bladet klickar du på den **Lägg till** knappen.
5. I **Välj en API**, Sök efter Batch-API: et. Sök efter var och en av de här strängarna tills du hittar API:t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Nyare Azure AD-klientorganisationer kan använda det här namnet.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** är id:t för API:t. 
6. När du har hittat Batch-API, markera den och klicka på **Välj**.
7. I **Välj behörigheter**, markerar du kryssrutan bredvid **Azure Batch-tjänsten för dataåtkomst** och klicka på **Välj**.
8. Klicka på **Klar**.

Den **nödvändiga behörigheter** windows nu visar att din Azure AD-program har åtkomst till både ADAL och Batch service API. Behörigheterna har beviljats till ADAL automatiskt när du först registrera din app med Azure AD.

![Bevilja API-behörigheter](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Använda ett huvudnamn för tjänsten 

För att autentisera ett program som körs obevakad måste använda du ett huvudnamn för tjänsten. När du har registrerat ditt program kan du följa stegen i Azure portal för att konfigurera ett huvudnamn för tjänsten:

1. Begär en hemlig nyckel för ditt program.
2. Tilldela en RBAC-roll till ditt program.

### <a name="request-a-secret-key-for-your-application"></a>Begär en hemlig nyckel för ditt program

När programmet autentiseras med ett huvudnamn för tjänsten skickar både program-ID och en hemlig nyckel till Azure AD. Du måste du skapa och kopiera den hemliga nyckeln du använder från din kod.

Följ dessa steg i Azure portal:

1. I det vänstra navigeringsfönstret i Azure-portalen, väljer **alla tjänster**. Klicka på **Appregistreringar**.
2. Sök efter namnet på ditt program i listan över app-registreringar.
3. Klicka på programmet och på **inställningar**. I den **API-åtkomst** väljer **nycklar**.
4. Ange en beskrivning av nyckeln för att skapa en nyckel. Välj sedan en varaktighet för nyckeln för ett eller två år. 
5. Klicka på den **spara** knappen för att skapa och visa nyckeln. Kopiera nyckelvärdet till en säker plats som du kan inte komma åt den igen när du har lämnat bladet. 

    ![Skapa en hemlig nyckel](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Tilldela en RBAC-roll till ditt program

För att autentisera med ett huvudnamn för tjänsten, måste du tilldela en RBAC-roll i ditt program. Följ de här stegen:

1. Gå till Batch-kontot som används av ditt program i Azure-portalen.
2. I den **inställningar** bladet för Batch-konto väljer **åtkomstkontroll (IAM)**.
3. Klicka på den **rolltilldelningar** fliken.
4. Klicka på den **Lägg till rolltilldelning** knappen. 
5. Från den **rollen** listrutan, väljer du antingen den _deltagare_ eller _läsare_ för ditt program. Mer information om dessa roller finns i [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).  
6. I den **Välj** fältet, anger du namnet på ditt program. Välj ditt program i listan och klicka på **spara**.

Ditt program bör nu visas i dina inställningar för åtkomstkontroll med ett RBAC-roll som tilldelats. 

![Tilldela en RBAC-roll till ditt program](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Hämta klient-ID för Azure Active Directory

Klient-ID identifierar Azure AD-klienten som tillhandahåller autentiseringstjänster för ditt program. Följ dessa steg om du vill hämta klient-ID:

1. Välj din Active Directory i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Kopiera GUID-värdet som angetts för den **katalog-ID**. Det här värdet kallas även för klient-ID.

![Kopiera katalog-ID](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Kodexempel

Kodexemplen i det här avsnittet visar hur du autentiserar med Azure AD med integrerad autentisering och ett huvudnamn för tjänsten. De flesta av dessa kodexempel med .NET, men begreppen är liknande för andra språk.

> [!NOTE]
> En Azure AD-autentiseringstoken upphör att gälla efter en timme. När du använder en långlivade **BatchClient** objekt, rekommenderar vi att du hämta en token från ADAL för varje begäran att säkerställa att du alltid har en giltig token. 
>
>
> För att uppnå detta i .NET, skriva en metod som hämtar en token från Azure AD och skicka den metoden för att en **BatchTokenCredentials** objektet som ett ombud. Delegera-metoden anropas för varje begäran till Batch-tjänsten så att en giltig token har angetts. Som standard cachelagrar ADAL token, så att en ny token hämtas från Azure AD vid behov. Mer information om token i Azure AD finns i [Autentiseringsscenarier för Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Kodexempel: använda Azure AD-integrerad autentisering med Batch .NET

Om du vill autentisera med integrerad autentisering från Batch .NET måste referera till den [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) paketet och [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) paketet.

Inkluderar följande `using` instruktioner i din kod:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referera till Azure AD-slutpunkten i din kod, inklusive klient-ID. Om du vill hämta klient-ID, följer du stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referens för Batch-tjänstslutpunkt för resursen:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till ditt Batch-konto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID är tillgängligt från din appregistrering i Azure portal:

```csharp
private const string ClientId = "<application-id>";
```

Kopiera omdirigerings-URI som du angav, även om du har registrerat ditt program som ett internt program. Omdirigerings-URI som angetts i din kod måste matcha omdirigeringen-URI som du angav när du har registrerat programmet:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Skriva en metod för att hämta autentiseringstoken från Azure AD. Den **GetAuthenticationTokenAsync** motringningsmetoden som visas här ADAL-anrop till autentisera en användare som interagerar med programmet. Den **AcquireTokenAsync** metod som tillhandahålls av ADAL uppmanar användaren att ange sina autentiseringsuppgifter och programmet fortsätter när användaren anger dem (om inte redan har det cachelagrade autentiseringsuppgifter):

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

Skapa en **BatchTokenCredentials** objekt som tar ombud som en parameter. Använd dessa autentiseringsuppgifter för att öppna en **BatchClient** objekt. Du kan använda som **BatchClient** objekt för efterföljande åtgärder mot Batch-tjänsten:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Kodexempel: med hjälp av en Azure AD-tjänstobjekt med Batch .NET

Om du vill autentisera med ett huvudnamn för tjänsten från Batch .NET måste referera till den [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) paketet och [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) paketet.

Inkluderar följande `using` instruktioner i din kod:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referera till Azure AD-slutpunkten i din kod, inklusive klient-ID. När du använder ett huvudnamn för tjänsten, måste du ange en klientspecifik slutpunkt. Om du vill hämta klient-ID, följer du stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referens för Batch-tjänstslutpunkt för resursen:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till ditt Batch-konto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID är tillgängligt från din appregistrering i Azure portal:

```csharp
private const string ClientId = "<application-id>";
```

Ange den hemliga nyckeln som du kopierade från Azure portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Skriva en metod för att hämta autentiseringstoken från Azure AD. Den **GetAuthenticationTokenAsync** motringningsmetoden som visas här anrop ADAL för obevakad autentisering:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Skapa en **BatchTokenCredentials** objekt som tar ombud som en parameter. Använd dessa autentiseringsuppgifter för att öppna en **BatchClient** objekt. Använda **BatchClient** objekt för efterföljande åtgärder mot Batch-tjänsten:

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
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Kodexempel: använda en Azure AD-tjänstobjekt med Batch Python

För att autentisera med ett huvudnamn för tjänsten från Batch Python, installera och referera till den [azure-batch](https://pypi.org/project/azure-batch/) och [azure gemensamma](https://pypi.org/project/azure-common/) moduler.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

När du använder ett huvudnamn för tjänsten, måste du ange klient-ID. Om du vill hämta klient-ID, följer du stegen som beskrivs i [hämta klient-ID för Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Referens för Batch-tjänstslutpunkt för resursen:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Referera till ditt Batch-konto:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID är tillgängligt från din appregistrering i Azure portal:

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

Använd autentiseringsuppgifter för tjänstens huvudnamn för att öppna en **BatchServiceClient** objekt. Använda **BatchServiceClient** objekt för efterföljande åtgärder mot Batch-tjänsten.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure AD i den [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/). Djupgående exempel som visar hur du använder ADAL finns i den [kodexempel för Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteket.

* Läs mer om tjänstens huvudnamn i [program och tjänstobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Om du vill skapa ett huvudnamn för tjänsten med Azure portal, [Använd portalen för att skapa Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md). Du kan också skapa ett huvudnamn för tjänsten med PowerShell eller Azure CLI.

* För att autentisera Batch Management-program med hjälp av Azure AD, se [autentisera med Batch Management-lösningar med Active Directory](batch-aad-auth-management.md).

* En Python-exempel på hur du skapar en Batch-klient som autentiseras med en Azure AD-token finns i den [distribution av Azure Batch anpassad avbildning med en Python-skriptet](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) exemplet.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[azure_portal]: http://portal.azure.com
