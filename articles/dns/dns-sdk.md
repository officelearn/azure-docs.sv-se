---
title: Skapa DNS-zoner och postuppsättningar i Azure DNS med .NET SDK | Microsoft Docs
description: Hur du skapar DNS-zoner och postuppsättningar i Azure DNS med hjälp av .NET SDK.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: victorh
ms.openlocfilehash: 14860ae48e520f86ce9d5bea739605d1a4baf0c7
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173201"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Skapa DNS-zoner och postuppsättningar med .NET SDK

Du kan automatisera åtgärder för att skapa, ta bort eller uppdatera DNS-zoner, postuppsättningar och poster med hjälp av DNS-SDK med DNS-hantering för .NET-biblioteket. En fullständig Visual Studio-projekt är tillgänglig [här.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Skapa ett konto för tjänstens huvudnamn

Vanligtvis beviljas programmatisk åtkomst till Azure-resurser via ett särskilt konto i stället för dina egna autentiseringsuppgifter. Dessa dedikerade konton kallas ”tjänstens huvudnamn” konton. Om du vill använda Azure DNS-SDK-exempelprojektet, måste du först skapa ett konto för tjänstens huvudnamn och tilldela det lämpliga behörigheter.

1. Följ [instruktionerna](../azure-resource-manager/resource-group-authenticate-service-principal.md) att skapa ett konto för tjänstens huvudnamn (Azure DNS-SDK-exempelprojektet förutsätter lösenordsbaserad autentisering.)
2. Skapa en resursgrupp ([här är hur](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Använd Azure RBAC för att bevilja tjänstens objektkonto DNS-zon-bidragsgivare till resursgruppen ([här är hur](../role-based-access-control/role-assignments-portal.md).)
4. Om du använder Azure DNS-SDK-exempelprojektet, redigerar du filen 'program.cs' på följande sätt:

   * Infoga rätt värden för tenantId, clientId (även kallat konto-ID), hemlighet (service principal kontolösenordet) och prenumerations-ID som används i steg 1.
   * Ange resursgruppens namn valdes i steg 2.
   * Ange namnet på en DNS-zon valfri.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-paket och namnrymdsdeklarationer

Om du vill använda .NET SDK för Azure DNS måste du installera den **Azure DNS Management Library** NuGet-paketet och andra Azure-paket som krävs.

1. I **Visual Studio**, öppna ett projekt eller ett nytt projekt.
2. Gå till **verktyg** **>** **NuGet-Pakethanteraren** **>** **hantera NuGet-paket för Lösning...** .
3. Klicka på **Bläddra**, aktivera den **inkludera förhandsversion** markerar du kryssrutan och skriv **Microsoft.Azure.Management.Dns** i sökrutan.
4. Välj paketet och klicka på **installera** lägga till den i Visual Studio-projektet.
5. Upprepa processen ovan för att även installera följande paket: **Microsoft.Rest.ClientRuntime.Azure.Authentication** och **Microsoft.Azure.Management.ResourceManager**.

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

Om du vill skapa en DNS-zon skapas först en ”zonobjektet” så att den innehåller parametrar för DNS-zon. Eftersom DNS-zoner inte är länkade till en viss region, anges platsen till ”global”. I det här exemplet en [Azure Resource Manager-tagg-](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) läggs också till i zonen.

För att verkligen skapa eller uppdatera zonen i Azure DNS, zonobjektet som innehåller parametrar för zonen skickas till den *DnsManagementClient.Zones.CreateOrUpdateAsyc* metod.

> [!NOTE]
> DnsManagementClient stöder tre lägen för åtgärden: synkron (”CreateOrUpdate”), asynkrona (”CreateOrUpdateAsync”), eller asynkron med åtkomst till HTTP-svaret (CreateOrUpdateWithHttpMessagesAsync).  Du kan välja någon av dessa lägen, beroende på dina programbehov.

Azure DNS stöder Optimistisk samtidighet, kallas [Etags](dns-getstarted-create-dnszone.md). I det här exemplet anger ”*” för den ”If-None-Match-huvudet talar om för Azure DNS för att skapa en DNS-zon om det inte redan finns.  Anropet misslyckas om en zon med det angivna namnet finns redan i den angivna resursgruppen.

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

## <a name="create-dns-record-sets-and-records"></a>Skapa DNS-postuppsättningar och poster

DNS-poster som hanteras som en uppsättning av poster. En postuppsättning är en uppsättning poster med samma namn och typ av post inom en zon.  Namnet på postuppsättningen är i förhållande till zonnamnet, inte det fullständigt kvalificerade DNS-namnet.

Om du vill skapa eller uppdatera en postuppsättning, ett ”RecordSet” parametrarna-objekt skapas och skickas till *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Som med DNS-zoner, det finns tre lägen för åtgärden: synkron (”CreateOrUpdate”), asynkrona (”CreateOrUpdateAsync”), eller asynkron med åtkomst till HTTP-svaret (CreateOrUpdateWithHttpMessagesAsync).

Precis som med DNS-zoner, omfattar åtgärder på postuppsättningar stöd för Optimistisk samtidighet.  I det här exemplet, eftersom varken ”If-Match” eller ”If-None-Match' har angetts, skapas postuppsättningen alltid.  Det här anropet skriver över alla befintliga postuppsättningen med samma namn och typ av resurspost i DNS-zonen.

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

## <a name="get-zones-and-record-sets"></a>Get-zoner och uppsättningar av poster

Den *DnsManagementClient.Zones.Get* och *DnsManagementClient.RecordSets.Get* metoder hämta enskilda zoner och postuppsättningar, respektive. Postuppsättningar identifieras efter typ, namn och gruppen zon och DNS-resurs som de finns i. Zoner identifieras av sina namn och resursgrupp som de finns i.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Uppdatera en befintlig uppsättning av poster

Om du vill uppdatera en befintlig DNS-postuppsättning, först hämta postuppsättningen och sedan uppdaterar innehållet för postuppsättningen, skicka ändringen.  I det här exemplet anger vi Etag från hämtade postuppsättningen i If-Match-parametern. Anropet misslyckas om en samtidig åtgärd har ändrat den post som angetts under tiden.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista zoner och uppsättningar av poster

Använd om du vill visa zoner i *DnsManagementClient.Zones.List...*  metoder, som stöder lista antingen alla zoner i en viss resursgrupp eller alla zoner i en Azure-prenumeration (över resursgrupper.) Använd om du vill visa en lista över postuppsättningar *DnsManagementClient.RecordSets.List...*  metoder, som stöder antingen lista alla postuppsättningar i en viss zon eller endast dessa uppsättningar av en viss typ av poster.

Observera när zoner och postuppsättningar som är ett resultat kan vara sidnumrerade.  I följande exempel visar hur du gå igenom sidorna i resultaten. (Ett artificiellt små sidstorlek för ”2” som används för att tvinga sidindelning; i praktiken bör den här parametern utelämnas och Standardsidstorleken användas.)

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

Ladda ned den [Azure DNS .NET SDK-exempelprojektet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), som innehåller ytterligare exempel på hur du använder Azure DNS .NET SDK, inklusive exempel för andra DNS-posttyper.
