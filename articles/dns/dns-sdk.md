---
title: Skapa DNS-zoner och postuppsättningar i Azure DNS med .NET SDK | Microsoft Docs
description: Hur du skapar DNS-zoner och postuppsättningar i Azure DNS med hjälp av .NET SDK.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: jonatul
ms.openlocfilehash: f6f10649670dac523289724603e2aecaf17fa67d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Skapa DNS-zoner och postuppsättningar med .NET SDK

Du kan automatisera åtgärder för att skapa, ta bort eller uppdatera DNS-zoner, uppsättningar av poster och poster med hjälp av DNS SDK med .NET DNS-bibliotek. En fullständig Visual Studio-projekt är tillgänglig [här.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Skapa ett huvudnamn tjänstkonto

Normalt beviljas programmatisk åtkomst till Azure-resurser via ett särskilt konto i stället för dina egna autentiseringsuppgifter. Dessa dedikerade konton kallas ' UPN ' tjänstkonton. Om du vill använda Azure DNS SDK exempelprojektet måste du först skapa en tjänstens objektkonto och tilldela rätt behörighet.

1. Följ [instruktionerna](../azure-resource-manager/resource-group-authenticate-service-principal.md) att skapa en tjänstens objektkonto (exempelprojektet Azure DNS SDK förutsätter lösenordsbaserad autentisering.)
2. Skapa en resursgrupp ([här är hur](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Använda Azure RBAC bevilja tjänstens objektkonto DNS-zonen-deltagare åtkomstbehörighet till resursgruppen ([här är hur](../role-based-access-control/role-assignments-portal.md).)
4. Om du använder Azure DNS SDK exempelprojektet redigerar du filen 'program.cs' enligt följande:

   * Infoga korrekta värden för klient-ID, clientId (även kallat konto-ID), hemligheten (service principal kontolösenord) och prenumerations-ID som används i steg 1.
   * Ange Resursgruppnamnet som valdes i steg 2.
   * Ange namnet på en DNS-zon önskat.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-paket och namnrymdsdeklarationer

Om du vill använda Azure DNS .NET SDK, måste du installera den **Azure DNS-bibliotek** NuGet-paketet och andra krävs för Azure-paket.

1. I **Visual Studio**, öppna ett projekt eller ett nytt projekt.
2. Gå till **verktyg** **>** **NuGet Package Manager** **>** **hantera NuGet-paket för Lösning...** .
3. Klicka på **Bläddra**, aktivera den **inkludera förhandsversion** kryssrutan och skriv **Microsoft.Azure.Management.Dns** i sökrutan.
4. Välj paketet och klickar på **installera** lägga till den i Visual Studio-projekt.
5. Upprepa proceduren ovan för att även installera följande paket: **Microsoft.Rest.ClientRuntime.Azure.Authentication** och **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Lägga till namnrymdsdeklarationer

Lägg till följande namnrymdsdeklarationer

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Initiera DNS management-klienten

Den *DnsManagementClient* innehåller metoder och egenskaper som är nödvändiga för att hantera DNS-zoner och postuppsättningar.  Följande kod loggar in på tjänstens objektkonto och skapar ett DnsManagementClient-objekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Skapa eller uppdatera en DNS-zon

Om du vill skapa en DNS-zon skapas först ett ”zonen”-objekt innehåller DNS-zonen parametrar. Eftersom DNS-zoner inte är kopplad till en viss region, har platsen angetts till 'globala'. I det här exemplet en [Azure Resource Manager 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) läggs också till i zonen.

Om du vill att skapa eller uppdatera zonen i Azure DNS, zon-objektet som innehåller parametrar för zonen har överförts till den *DnsManagementClient.Zones.CreateOrUpdateAsyc* metod.

> [!NOTE]
> DnsManagementClient stöder tre arbetslägen: synkron ('CreateOrUpdate'), asynkron ('CreateOrUpdateAsync'), eller asynkron med åtkomst till HTTP-svaret (CreateOrUpdateWithHttpMessagesAsync).  Du kan välja något av dessa lägen, beroende på dina programbehov.

Azure DNS stöder Optimistisk samtidighet, kallas [Etags](dns-getstarted-create-dnszone.md). I det här exemplet anger ”*” för den 'If-None-Match-sidhuvudet anger Azure DNS för att skapa en DNS-zon om det inte redan finns.  Anropet misslyckas om en zon med det angivna namnet finns redan i den angivna resursgruppen.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Skapa uppsättningar av DNS-poster och poster

DNS-poster som hanteras som en uppsättning poster. En postuppsättning är en uppsättning poster med samma namn och typ inom en zon.  Namnet på postuppsättningen är i förhållande till zonnamnet, inte det fullständigt kvalificerade DNS-namnet.

För att skapa eller uppdatera en uppsättning poster, ett ”RecordSet” parametrar-objekt skapas och skickas till *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Som med DNS-zoner, det finns tre arbetslägen: synkron ('CreateOrUpdate'), asynkron ('CreateOrUpdateAsync'), eller asynkron med åtkomst till HTTP-svaret (CreateOrUpdateWithHttpMessagesAsync).

Precis som med DNS-zoner är åtgärder på postuppsättningar stöd för Optimistisk samtidighet.  I det här exemplet eftersom varken 'If-Match' eller 'If-None-Match' anges, skapas uppsättningen av poster alltid.  Det här anropet skriver över alla befintliga-postuppsättning med samma namn och typ av post i DNS-zonen.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Hämta zoner och postuppsättningar

Den *DnsManagementClient.Zones.Get* och *DnsManagementClient.RecordSets.Get* metoder hämta enskilda zoner och postuppsättningar, respektive. Postuppsättningar identifieras av deras typ, namn och gruppen zon och resurser som de finns i. Zoner identifieras genom sina namn och de finns i resursgruppen.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Uppdatera en befintlig uppsättning av poster

Om du vill uppdatera en befintlig DNS-postuppsättning, först hämta uppsättningen poster och sedan uppdatera postuppsättning innehållet, skicka ändringen.  I det här exemplet anger vi Etag från hämtade postuppsättningen i If-Match-parametern. Anropet misslyckas om en samtidig åtgärd har ändrat posten under tiden.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista zoner och postuppsättningar

Använd om du vill visa en lista över zoner i *DnsManagementClient.Zones.List...*  metoder som stöder med antingen alla zoner i en viss resursgrupp eller alla zoner i en viss Azure-prenumeration (över resursgrupper.) Använd om du vill visa en lista över postuppsättningar *DnsManagementClient.RecordSets.List...*  metoder som stöder antingen visar en lista över alla uppsättningar av poster i en viss zon eller de postuppsättningar av en viss typ.

Observera när du visar en lista över zoner och postuppsättningar som är ett resultat kan sidbrytning.  I följande exempel visar hur du gå igenom sidorna i resultaten. (Ett artificiellt små sidstorleken för '2' används för att tvinga sidindelning; i praktiken den här parametern bör uteslutas och standardvärdet för sidstorlek används.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Nästa steg

Hämta den [Azure DNS .NET SDK exempelprojektet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), som innehåller ytterligare exempel på hur du använder Azure DNS .NET SDK, inklusive exempel för andra typer av DNS-poster.
