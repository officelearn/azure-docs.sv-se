---
title: "Implementera disaster recovery med säkerhetskopiering och återställning i Azure API Management | Microsoft Docs"
description: "Lär dig hur du använder säkerhetskopiering och återställning för att utföra katastrofåterställning i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: 3fcd2fc4162cfbf549be979e15745934c2e4c6ff
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Implementera haveriberedskap med hjälp av tjänsten säkerhetskopiering och återställning i Azure API Management

Genom att välja att publicera och hantera dina API: er via Azure API Management du dra nytta av många feltolerans och funktionerna i infrastrukturen som du annars skulle behöva utforma, implementera och hantera. Azure-plattformen minskar en stor del av fel på en bråkdel av kostnaden.

Om du vill återställa från tillgänglighetsproblem som påverkar den region där din API Management-tjänsten körs, bör du vara redo att fylla i en annan region tjänsten när som helst. Du kanske vill reservera säkerhetskopieringstjänsten i en eller flera regioner och försök att underhålla sina konfigurationer och innehåll synkroniserat med den aktiva tjänsten beroende på dina mål för tillgänglighet och mål. Funktionen för tjänsten ”säkerhetskopiera och återställa” ger nödvändiga byggblock för att implementera din strategi för katastrofåterställning.

Den här guiden visar hur du autentiserar Azure Resource Manager begäranden och säkerhetskopiera och återställa din API Management-tjänstinstanser.

> [!NOTE]
> Processen för att säkerhetskopiera och återställa en API Management service-instans för katastrofåterställning kan också användas för att replikera API Management-tjänstinstanser för scenarier, till exempel Förproduktion.
>
> Varje säkerhetskopiering upphör att gälla efter 30 dagar. Om du försöker återställa en säkerhetskopia efter den 30-dagars giltighetstid har upphört att gälla kan återställningen misslyckas med ett `Cannot restore: backup expired` meddelande.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Autentiserande Azure Resource Manager-begäranden

> [!IMPORTANT]
> REST API för säkerhetskopiering och återställning använder Azure Resource Manager och har en annan autentiseringsmekanism än REST-API: er för att hantera API Management-enheter. Stegen i det här avsnittet beskrivs hur du autentiserar Azure Resource Manager-begäranden. Mer information finns i [autentisera Azure Resource Manager begär](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Alla aktiviteter som du gör på resurser med Azure Resource Manager måste autentiseras med Azure Active Directory med följande steg:

* Lägg till ett program till Azure Active Directory-klient.
* Ange behörigheter för det program som du har lagt till.
* Hämta token för att autentisera förfrågningar till Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Använd den prenumeration som innehåller din API Management service-instans, navigera till den **App registreringar** fliken.

    > [!NOTE]
    > Om Azure Active Directory-standardkatalog inte visas för ditt konto, kontaktar du administratören för Azure-prenumeration om du vill ge behörighet till ditt konto.
3. Klicka på **Ny programregistrering**.

    Den **skapa** visas till höger. Det är där du anger AAD app relevant information.
4. Ange ett namn för programmet.
5. Programtyp, Välj **interna**.
6. Ange en platshållare-URL som `http://resources` för den **omdirigerings-URI**eftersom det är ett obligatoriskt fält, men värdet används inte senare. Markera kryssrutan om du vill spara programmet.
7. Klicka på **Skapa**.

### <a name="add-an-application"></a>Lägg till ett program

1. När programmet har skapats klickar du på **inställningar**.
2. Klicka på **nödvändiga behörigheter**.
3. Klicka på **+ Lägg till**.
4. Tryck på **väljer en API**.
5. Välj **Windows** **Azure Service Management API**.
6. Tryck på **Välj**. 

    ![Lägg till behörigheter](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klicka på **delegerade behörigheter** bredvid det nyligen tillagda programmet, markera kryssrutan för **åtkomst till Azure Service Management (förhandsversion)**.
8. Tryck på **Välj**.

### <a name="configuring-your-app"></a>Konfigurera appen

Det är nödvändigt att hämta en token innan du anropar API: er som genererar säkerhetskopieringen och återställa den. I följande exempel används den [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-paketet för att hämta token.

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

Ersätt `{tentand id}`, `{application id}`, och `{redirect uri}` följa dessa anvisningar:

1. Ersätt `{tenant id}` med klient-id för Azure Active Directory-program som du skapade. Du kan få åtkomst till id genom att klicka på **App registreringar** -> **slutpunkter**.

    ![Slutpunkter][api-management-endpoint]
2. Ersätt `{application id}` med det värde du får genom att navigera till den **inställningar** sidan.
3. Ersätt URL från den **omdirigerings-URI: er** är ditt Azure Active Directory-program.

    När värden har angetts ska i kodexempel returnera en token liknar följande exempel:

    ![Token][api-management-arm-token]

## <a name="calling-the-backup-and-restore-operations"></a>Anropar åtgärder för säkerhetskopiering och återställning

Ställ in begärandehuvudet auktorisering för REST-anrop innan du anropar ”säkerhetskopiering och återställning” operationerna som beskrivs i följande avsnitt.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"></a>Säkerhetskopiera en API Management-tjänst
Att säkerhetskopiera en API Management-tjänsten problemet följande HTTP-begäran:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Där:

* `subscriptionId`-id för prenumerationen som innehåller API Management-tjänsten som du försöker säkerhetskopiera
* `resourceGroupName`-en sträng i formatet 'Api - Default-{tjänsten region}' där `service-region` identifierar Azure-regionen där API Management-tjänst du försöker säkerhetskopiering finns, t.ex.`North-Central-US`
* `serviceName`-namnet på API Management-tjänsten du gör en säkerhetskopia av angavs när skapades
* `api-version`-Ersätt med`2014-02-14`

Ange målnamn Azure storage-konto, åtkomstnyckel, blob behållarens namn och namn på säkerhetskopia i brödtexten i begäran:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Ange värdet för den `Content-Type` begärandehuvudet till `application/json`.

Säkerhetskopiering är en tidskrävande process som kan ta flera minuter att slutföra.  Om begäran lyckades och säkerhetskopieringen har startats visas ett `202 Accepted` Svarets statuskod med en `Location` huvud.  Se ”GET-begäranden till URL: en i den `Location` huvudet för att ta reda på status för åtgärden. När säkerhetskopieringen pågår fortsätta att ta emot en '202 godkända-statuskod. En svarskod `200 OK` anger slutförande av säkerhetskopieringen.

Observera följande begränsningar när du gör en begäran om säkerhetskopieringen.

* **Behållaren** anges i begärandetexten **måste finnas**.
* När säkerhetskopiering pågår du **bör inte några hanteringsåtgärder för service** , till exempel SKU uppgradering eller nedgradering domän namnbytet osv.
* Återställning av en **säkerhetskopiering garanteras endast för 30 dagar** sedan den tidpunkt då skapades.
* **Användningsdata** används för att skapa rapporter för analytics **ingår inte** i säkerhetskopian. Använd [Azure API Management REST API] [ Azure API Management REST API] att regelbundet hämta analytics rapporter förvaras.
* Den frekvens med vilken du säkerhetskopiera service påverkar dina mål för återställningspunkt. För att minimera den rekommendationen implementera regelbundna säkerhetskopieringar samt utföra säkerhetskopieringar på begäran när viktiga ändringar till din API Management-tjänst.
* **Ändringar** gjorts i konfigurationen för tjänsten (till exempel API: er, principer, developer portal utseende) vid säkerhetskopiering åtgärden pågår **kan inte tas med i säkerhetskopieringen och därför går förlorad**.

### <a name="step2"></a>Återställa en API Management-tjänst
Om du vill återställa en API Management gör tjänsten från en tidigare skapad säkerhetskopia följande HTTP-begäran:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Där:

* `subscriptionId`-id för prenumerationen som innehåller API Management-tjänsten som du återställer en säkerhetskopia till
* `resourceGroupName`-en sträng i formatet 'Api - Default-{tjänsten region}' där `service-region` identifierar Azure-regionen där API Management-tjänsten som du återställer en säkerhetskopia till värd, t.ex.`North-Central-US`
* `serviceName`-namnet på API-hantering som återställs till tjänsten anges när skapades
* `api-version`-Ersätt med`2014-02-14`

Ange Säkerhetskopians plats är, kontonamn för Azure storage, åtkomstnyckel, blob behållarens namn och namn på säkerhetskopia i brödtexten i begäran:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Ange värdet för den `Content-Type` begärandehuvudet till `application/json`.

Återställningen är en tidskrävande process som kan ta upp till 30 minuter att slutföra. Om begäran lyckades och återställningen har startats visas ett `202 Accepted` Svarets statuskod med en `Location` huvud. Se ”GET-begäranden till URL: en i den `Location` huvudet för att ta reda på status för åtgärden. När återställningen pågår fortfarande '202 godkända-statuskod. En svarskod `200 OK` anger slutförande av återställningen.

> [!IMPORTANT]
> **SKU: N** för tjänsten som återställs till **måste matcha** SKU av tjänsten säkerhetskopierade återställs.
>
> **Ändringar** gjorde att konfigurationen för tjänsten (till exempel API: er, principer, developer portal utseende) när återställningen pågår **kan skrivas över**.
>
>

## <a name="next-steps"></a>Nästa steg
Ta en titt i följande Microsoft bloggar för två olika genomgångar av säkerhetskopiering/återställning.

* [Replikera Azure API Management-konton](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Azure API Management: Säkerhetskopiera och återställa konfiguration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Den metod som beskrivs av Stuart matchar inte den officiella vägledningen men det är intressant.

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
