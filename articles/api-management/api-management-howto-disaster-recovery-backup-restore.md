---
title: Implementera haveriberedskap med säkerhetskopiering och återställning i API Management
titleSuffix: Azure API Management
description: Lär dig hur du använder säkerhetskopiering och återställning för att utföra haveriberedskap i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: e74d7dcf8764d167e0080c9d7cca5573bd69ef1d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261014"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management

Genom att publicera och hantera dina API:er via Azure API Management utnyttjar du feltolerans och infrastrukturfunktioner som du annars skulle utforma, implementera och hantera manuellt. Azure-plattformen minskar en stor del av potentiella fel till en bråkdel av kostnaden.

Om du vill återställa från tillgänglighetsproblem som påverkar regionen som är värd för din API Management-tjänst kan du när som helst konfigurera om tjänsten i en annan region. Beroende på ditt mål på återställningstiden kanske du vill behålla en väntetjänst i en eller flera regioner. Du kan också försöka behålla deras konfiguration och innehåll synkroniserat med den aktiva tjänsten enligt återställningspunktsmålet. Funktionerna för säkerhetskopiering och återställning av tjänsten ger de byggstenar som krävs för att implementera katastrofåterställningsstrategin.

Säkerhetskopierings- och återställningsåtgärder kan också användas för att replikera API Management-tjänstkonfiguration mellan driftsmiljöer, t.ex. Se upp för att körningsdata som användare och prenumerationer också kommer att kopieras, vilket kanske inte alltid är önskvärt.

Den här guiden visar hur du automatiserar säkerhetskopiering och återställning och hur du säkerställer autentisering av begäranden om säkerhetskopiering och återställning av Azure Resource Manager.

> [!IMPORTANT]
> Återställningsåtgärden ändrar inte anpassad värdnamnskonfiguration för måltjänsten. Vi rekommenderar att du använder samma anpassade värdnamn och TLS-certifikat för både aktiva tjänster och väntelägestjänster, så att trafiken kan dirigeras om till standby-instansen genom en enkel DNS CNAME-ändring när återställningen är slutförd.
>
> Säkerhetskopieringsåtgärden samlar inte in föraggerade loggdata som används i rapporter som visas på Analytics-bladet i Azure-portalen.

> [!WARNING]
> Varje säkerhetskopia upphör att gälla efter 30 dagar. Om du försöker återställa en säkerhetskopia efter att 30-dagars utgångsdatum `Cannot restore: backup expired` har gått ut misslyckas återställningen med ett meddelande.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autentisera Azure Resource Manager-begäranden

> [!IMPORTANT]
> REST API för säkerhetskopiering och återställning använder Azure Resource Manager och har en annan autentiseringsmekanism än REST-API:erna för att hantera dina API Management-entiteter. Stegen i det här avsnittet beskriver hur du autentiserar Azure Resource Manager-begäranden. Mer information finns i [Autentisera Azure Resource Manager-begäranden](/rest/api/index).

Alla uppgifter som du utför på resurser som använder Azure Resource Manager måste autentiseras med Azure Active Directory med hjälp av följande steg:

-   Lägg till ett program i Azure Active Directory-klienten.
-   Ange behörigheter för det program som du har lagt till.
-   Hämta token för att autentisera begäranden till Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Med hjälp av prenumerationen som innehåller din API Management-tjänstinstans navigerar du till fliken **Appregistreringar** i **Azure Active Directory** (Azure Active Directory > Hantera/App-registreringar).

    > [!NOTE]
    > Om standardkatalogen för Azure Active Directory inte är synlig för ditt konto kontaktar du administratören för Azure-prenumerationen för att bevilja de behörigheter som krävs till ditt konto.

3. Klicka på **Ny programregistrering**.

    Fönstret **Skapa** visas till höger. Det är där du anger relevant information för AAD-appen.

4. Ange ett namn på programmet.
5. För programtypen väljer du **Native**.
6. Ange en platshållaradress, till exempel `http://resources` för Redirect **URI**, eftersom det är ett obligatoriskt fält, men värdet används inte senare. Klicka på kryssrutan för att spara programmet.
7. Klicka på **Skapa**.

### <a name="add-an-application"></a>Lägga till ett program

1. När programmet har skapats klickar du på **API-behörigheter**.
2. Klicka på **+ Lägg till en behörighet**.
4. Tryck på **Välj Microsoft-API:er**.
5. Välj **Azure Service Management**.
6. Tryck **på Markera**.

    ![Lägga till behörigheter](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klicka på **Delegerade behörigheter** bredvid det nyligen tillagda programmet, markera kryssrutan för **Access Azure Service Management (förhandsversion)**.
8. Tryck **på Markera**.
9. Klicka på **Bevilja behörigheter**.

### <a name="configuring-your-app"></a>Konfigurera din app

Innan du anropar API:erna som genererar säkerhetskopian och återställer den måste du skaffa en token. I följande exempel används paketet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet för att hämta token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Ersätt `{tenant id}` `{application id}`, `{redirect uri}` och med hjälp av följande instruktioner:

1. Ersätt `{tenant id}` med klient-ID för Azure Active Directory-programmet som du skapade. Du kan komma åt ID genom att klicka på **Slutpunkter för appregistreringar** -> **Endpoints**.

    ![Slutpunkter][api-management-endpoint]

2. Ersätt `{application id}` med det värde du får genom att navigera till sidan **Inställningar.**
3. Ersätt `{redirect uri}` värdet från fliken **Omdirigera URI:er i** ditt Azure Active Directory-program.

    När värdena har angetts ska kodexemplet returnera en token som liknar följande exempel:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token kan upphöra att gälla efter en viss period. Kör kodexemplet igen för att generera en ny token.

## <a name="calling-the-backup-and-restore-operations"></a>Anropa säkerhetskopierings- och återställningsåtgärder

REST-API:erna är [Api Management Service - Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) and Api Management Service - [Restore](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Innan du anropar åtgärderna "säkerhetskopiering och återställning" som beskrivs i följande avsnitt anger du huvudet för auktoriseringsbegäran för REST-anropet.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Säkerhetskopiera en API Management-tjänst

Så här säkerhetskopierar du ett PROBLEM MED API Management-tjänsten följande HTTP-begäran:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

där:

-   `subscriptionId`- ID för prenumerationen som innehåller API Management-tjänsten som du försöker säkerhetskopiera
-   `resourceGroupName`- Namnet på resursgruppen för din Azure API Management-tjänst
-   `serviceName`- namnet på API Management-tjänsten som du gör en säkerhetskopia av angiven när den skapades
-   `api-version`- ersätta med`2018-06-01-preview`

I brödtexten för begäran anger du namnet på målkontot för Azure-lagringskonto, åtkomstnyckel, blob-behållarnamn och namn på säkerhetskopiering:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ange värdet för `Content-Type` begäranden `application/json`till .

Säkerhetskopiering är en tidskrävande åtgärd som kan ta mer än en minut att slutföra. Om begäran lyckades och säkerhetskopieringen påbörjades får du en `202 Accepted` svarsstatuskod med ett `Location` huvud. Gör GET-begäranden till URL:en i sidhuvudet `Location` för att ta reda på åtgärdens status. Medan säkerhetskopieringen pågår fortsätter du att få statuskoden "202 Accepterad". En svarskod `200 OK` för anger att säkerhetskopieringen har slutförts.

Observera följande begränsningar när du gör en begäran om säkerhetskopiering eller återställning:

-   **Behållare som** anges i begärandetexten **måste finnas**.
-   Medan säkerhetskopiering pågår **undviker du hanteringsändringar i tjänsten,** till exempel SKU-uppgradering eller nedgradering, ändring i domännamn med mera.
-   Återställning av en **säkerhetskopia garanteras endast i 30 dagar** sedan tidpunkten för dess tillkomst.
-   **Användningsdata** som används för att skapa analysrapporter **ingår inte** i säkerhetskopian. Använd [AZURE API Management REST API][azure api management rest api] för att regelbundet hämta analysrapporter för förvaring.
-   Dessutom är följande objekt inte en del av säkerhetskopieringsdata: anpassade domän TLS/SSL-certifikat och eventuella mellanliggande certifikat eller rotcertifikat som överförs av kund, utvecklarportalinnehåll och inställningar för integrering av virtuella nätverk.
-   Hur ofta du utför säkerhetskopior av tjänsten påverkar återställningspunktsmålet. För att minimera det rekommenderar vi att du implementerar regelbundna säkerhetskopior och säkerhetskopieringar på begäran när du har gjort ändringar i api management-tjänsten.
-   **Ändringar** som görs i tjänstkonfigurationen (till exempel API:er, principer och utvecklarportalens utseende) medan säkerhetskopiering pågår **kan uteslutas från säkerhetskopian och gå förlorad**.
-   **Tillåt** åtkomst från kontrollplan till Azure Storage-konto, om det har [brandvägg][azure-storage-ip-firewall] aktiverat. Kunden bör öppna uppsättningen [Azure API Management Control Plane IP-adresser][control-plane-ip-address] på sitt lagringskonto för säkerhetskopiering till eller återställning från. 

> [!NOTE]
> Om du försöker göra säkerhetskopiering/återställning från/till en API Management-tjänst med hjälp av ett lagringskonto som har [brandvägg][azure-storage-ip-firewall] aktiverat, i samma Azure-region, kommer detta inte att fungera. Detta beror på att begäranden till Azure Storage inte är SNATed till en offentlig IP från Compute > (Azure Api Management control Plane). Lagringsbegäran för över region kommer att SNATed.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Återställa en API Management-tjänst

Om du vill återställa en API Management-tjänst från en tidigare skapad säkerhetskopia gör du följande HTTP-begäran:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

där:

-   `subscriptionId`- ID för prenumerationen som innehåller API Management-tjänsten som du återställer en säkerhetskopia till
-   `resourceGroupName`- namnet på resursgruppen som innehåller Azure API Management-tjänsten som du återställer en säkerhetskopia till
-   `serviceName`- Namnet på api Management-tjänsten som återställs till angivet när den skapades
-   `api-version`- ersätta med`2018-06-01-preview`

Ange platsen för säkerhetskopian av säkerhetskopian i brödtexten. Det vill än, lägg till Azure-lagringskontonamn, åtkomstnyckel, blob-behållarnamn och namn på säkerhetskopiering:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ange värdet för `Content-Type` begäranden `application/json`till .

Återställning är en tidskrävande åtgärd som kan ta upp till 30 minuter eller mer att slutföra. Om begäran lyckades och återställningsprocessen påbörjades `202 Accepted` får du `Location` en svarsstatuskod med ett huvud. Gör GET-begäranden till URL:en i sidhuvudet `Location` för att ta reda på åtgärdens status. Medan återställningen pågår fortsätter du att få statuskoden "202 Accepterad". En svarskod `200 OK` för anger att återställningen har slutförts.

> [!IMPORTANT]
> **SKU för** tjänsten som återställs till **måste matcha** SKU för säkerhetskopierade tjänsten som återställs.
>
> **Ändringar** som görs i tjänstkonfigurationen (till exempel API:er, principer, utvecklarportalens utseende) medan återställningen pågår **kan skrivas över**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Säkerhetskopierings- och återställningsåtgärder kan också utföras med PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) och [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) respektive.

## <a name="next-steps"></a>Nästa steg

Kolla in följande resurser för olika genomgångar av säkerhetskopiering/återställning.

-   [Replikera Azure API Management-konton](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatisera API Management-säkerhetskopiering och -återställning med Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Säkerhetskopiering och återställning](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    av konfiguration_Metoden som beskrivs av Stuart matchar inte den officiella vägledningen men det är intressant._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
