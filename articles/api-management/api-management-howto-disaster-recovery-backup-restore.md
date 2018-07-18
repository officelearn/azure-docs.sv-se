---
title: Implementera disaster recovery med säkerhetskopiering och återställning i Azure API Management | Microsoft Docs
description: Lär dig hur du använder säkerhetskopiering och återställning för att utföra katastrofåterställning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: b06a179459a449762555879669d177f811cb9560
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090885"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Implementera haveriberedskap med hjälp av service-säkerhetskopiering och återställning i Azure API Management

Genom att välja att publicera och hantera dina API: er via Azure API Management du dra nytta av många feltolerans och infrastrukturfunktioner som du annars skulle behöva utforma, implementera och hantera. Azure-plattformen minskar risken för en stor del av potentiella fel till en bråkdel av kostnaden.

Om du vill återställa från tillgänglighetsproblem som påverkar den region där API Management-tjänsten finns, bör du vara redo att fylla din tjänst i en annan region när som helst. Beroende på tillgänglighet målen och återställningstid, kanske du vill reservera en tjänst i en eller flera regioner för säkerhetskopiering och försök att underhålla sina konfigurationer och innehåll som är synkroniserade med den aktiva tjänsten. Funktionen för tjänsten ”säkerhetskopiera och Återställ” ger nödvändiga byggblock för att implementera din strategi för katastrofåterställning.

Den här guiden visar hur du autentiserar Azure Resource Manager-förfrågningar och hur du säkerhetskopierar och återställer din API Management-tjänstinstanser.

> [!NOTE]
> Processen för att säkerhetskopiera och återställa en API Management-tjänstinstans för haveriberedskap kan också användas för att replikera API Management-tjänstinstanser för scenarier, till exempel mellanlagring.
>
> Varje säkerhetskopiering upphör att gälla efter 30 dagar. Om du försöker återställa en säkerhetskopia när 30-dagars giltighetsperiod har gått ut, återställningen misslyckas med ett `Cannot restore: backup expired` meddelande.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Autentiserande Azure Resource Manager-begäranden

> [!IMPORTANT]
> REST-API för säkerhetskopiering och återställning använder Azure Resource Manager och har en annan autentiseringsmekanism än REST-API: er för att hantera dina API Management-entiteter. Stegen i det här avsnittet beskriver hur du autentiserar Azure Resource Manager-förfrågningar. Mer information finns i [autentisering av Azure Resource Manager begär](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Alla aktiviteter som du kan utföra på resurser med hjälp av Azure Resource Manager måste autentiseras med Azure Active Directory med hjälp av följande steg:

* Lägg till ett program till Azure Active Directory-klient.
* Ange behörigheter för det program som du har lagt till.
* Hämta token för autentisering av förfrågningar till Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Använd den prenumeration som innehåller din API Management-tjänstinstans, navigera till den **appregistreringar** fliken **Azure Active Directory** (Azure Active Directory > Hantera/App-registreringar).

    > [!NOTE]
    > Om Azure Active Directory-standardkatalog inte visas för ditt konto, kontakta administratören för Azure-prenumerationen för att ge behörighet till ditt konto.
3. Klicka på **Ny programregistrering**.

    Den **skapa** visas till höger. Det är där du anger att den relevanta informationen för AAD-app.
4. Ange ett namn för programmet.
5. Vilken typ av program, Välj **interna**.
6. Ange en URL-platshållaren som `http://resources` för den **omdirigerings-URI**, eftersom det är ett obligatoriskt fält, men värdet används inte senare. Klicka på kryssrutan för att spara programmet.
7. Klicka på **Skapa**.

### <a name="add-an-application"></a>Lägg till ett program

1. När programmet har skapats klickar du på **inställningar**.
2. Klicka på **behörigheter som krävs för**.
3. Klicka på **+ Lägg till**.
4. Tryck på **Välj en API**.
5. Välj **Windows** **Azure Service Management API**.
6. Tryck på **Välj**. 

    ![Lägga till behörigheter](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klicka på **delegerade behörigheter** bredvid det nyligen tillagda programmet, markera kryssrutan för **åtkomst till Azure Service Management (förhandsversion)**.
8. Tryck på **Välj**.

### <a name="configuring-your-app"></a>Konfigurera din app

Det är nödvändigt att hämta en token innan du anropar API: er som generera säkerhetskopian och återställer den. I följande exempel används den [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) nuget för att hämta token.

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
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Ersätt `{tentand id}`, `{application id}`, och `{redirect uri}` att följa dessa anvisningar:

1. Ersätt `{tenant id}` med klient-id för Azure Active Directory-program som du skapade. Du kan komma åt det ID: t genom att klicka på **appregistreringar** -> **slutpunkter**.

    ![Slutpunkter][api-management-endpoint]
2. Ersätt `{application id}` med värdet som du får genom att navigera till den **inställningar** sidan.
3. Ersätt den `{redirect uri}` med värdet från den **omdirigerings-URI: er** fliken i ditt Azure Active Directory-program.

    När värden har angetts kan ska kodexemplet returnera en token liknar följande exempel:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token kan upphör att gälla efter en viss tid. Kör kodexempel igen för att skapa en ny token.

## <a name="calling-the-backup-and-restore-operations"></a>Anropa åtgärder för säkerhetskopiering och återställning

Ställ in begärandehuvudet auktorisering för REST-anrop innan du anropar ”säkerhetskopiering och återställning” åtgärderna som beskrivs i följande avsnitt.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Säkerhetskopiera en API Management-tjänsten
Säkerhetskopiera ett problem med API Management-tjänsten följande HTTP-begäran:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Där:

* `subscriptionId` -ID: t för prenumerationen som innehåller API Management-tjänsten som du försöker säkerhetskopiera
* `resourceGroupName` – namnet på resursgruppen för Azure API Management-tjänsten
* `serviceName` – namnet på API Management-tjänsten du gör en säkerhetskopia av anges vid tidpunkten för den har skapandet
* `api-version` – Ersätt med `2014-02-14`

Ange mål Azure storage-kontonamn, åtkomstnyckel, blob-behållarnamn och namn på säkerhetskopia i brödtexten för begäran:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Ange värdet för den `Content-Type` begärandehuvudet till `application/json`.

Backup är en tidskrävande åtgärd som kan ta flera minuter att slutföra.  Om begäran lyckades och säkerhetskopieringen startades, får du en `202 Accepted` svarsstatuskod med en `Location` rubrik.  Se ”GET-begäranden till URL: en i den `Location` huvudet för att ta reda på status för åtgärden. När säkerhetskopieringen pågår kan fortsätta att ta emot statuskoden ”202-accepterad”. En svarskod `200 OK` anger säkerhetskopieringen slutförts och lyckats.

Observera följande begränsningar när du gör en begäran om säkerhetskopiering.

* **Behållaren** anges i begärandetexten **måste finnas**.
* När säkerhetskopiering pågår du **bör inte försöka alla tjänsthantering** , till exempel SKU-uppgradering eller nedgradera, ändring av domän namn, osv.
* Återställning av en **säkerhetskopiering garanteras endast för 30 dagar** sedan den tidpunkt då skapades.
* **Användningsdata** används för att skapa analysrapporter **ingår inte** i säkerhetskopian. Använd [Azure API Management REST API] [ Azure API Management REST API] att regelbundet hämta analysrapporter för säker förvaring.
* Den frekvens med vilken du säkerhetskopiera tjänst påverkar dina mål för återställningspunkt. För att minimera den rekommendationen implementera regelbundna säkerhetskopieringar samt utföra säkerhetskopieringar på begäran när du har gjort viktiga ändringar till API Management-tjänsten.
* **Ändringar** gjort i tjänstkonfigurationen (till exempel API: er, principer, developer portal utseende) vid säkerhetskopiering åtgärden pågår **kan inte ingå i säkerhetskopieringen och därför går förlorad**.

### <a name="step2"> </a>Återställa en API Management-tjänsten
Om du vill återställa en API Management-tjänsten från en tidigare skapad säkerhetskopia gör följande HTTP-begäran:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Där:

* `subscriptionId` -ID: t för prenumerationen som innehåller API Management-tjänsten som du återställer en säkerhetskopia i
* `resourceGroupName` – en sträng med formatet ”API - Default-{tjänsten region}' där `service-region` identifierar Azure-regionen där API Management-tjänsten som du återställer en säkerhetskopia till värd, till exempel `North-Central-US`
* `serviceName` – namnet på det API service håller på att återställas till anges vid tidpunkten för den har skapandet
* `api-version` – Ersätt med `2014-02-14`

Ange Säkerhetskopians plats som är, Azure storage-kontonamn, åtkomstnyckel, blob-behållarnamn och namn på säkerhetskopia i brödtexten för begäran:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Ange värdet för den `Content-Type` begärandehuvudet till `application/json`.

Återställning är en tidskrävande åtgärd som kan ta upp till 30 minuter att slutföra. Om begäran lyckades och återställningsprocessen har påbörjats, får du en `202 Accepted` svarsstatuskod med en `Location` rubrik. Se ”GET-begäranden till URL: en i den `Location` huvudet för att ta reda på status för åtgärden. Medan återställningen pågår kan du fortsätta att ta emot ”202-accepterad” statuskod. En svarskod `200 OK` anger slutförande av återställningen.

> [!IMPORTANT]
> **SKU: N** av tjänsten håller på att återställas till **måste matcha** SKU för säkerhetskopierade tjänsten håller på att återställas.
>
> **Ändringar** gjorde att konfigurationen av (till exempel API: er, principer, developer portal utseende) vid återställning pågår **kan skrivas över**.

> [!NOTE]
> Säkerhetskopiering och återställning kan också utföras med Powershell *Backup-AzureRmApiManagement* och *Restore-AzureRmApiManagement* respektive-kommandon.

## <a name="next-steps"></a>Nästa steg
Ta en titt i följande Microsoft-bloggar för två olika genomgångar av processen för säkerhetskopiering/återställning.

* [Replikera Azure API Management-konton](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Azure API Management: Säkerhetskopiera och återställa konfiguration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Den metod som beskrivs av Stuart matchar inte de officiella riktlinjer, men det är intressant.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
