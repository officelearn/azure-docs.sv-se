---
title: Skapa DNS-zoner och postuppsättningar med .NET SDK
titleSuffix: Azure DNS
description: I den här utbildningssökvägen kommer du igång med att skapa DNS-zoner och postuppsättningar i Azure DNS med hjälp av .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.openlocfilehash: c497209e456ff838786edaa19e46ebc5c1858d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938867"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Skapa DNS-zoner och postuppsättningar med .NET SDK

Du kan automatisera åtgärder för att skapa, ta bort eller uppdatera DNS-zoner, postuppsättningar och poster med hjälp av DNS SDK med .NET DNS Management-biblioteket. Ett fullständigt Visual Studio-projekt finns [här.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Skapa ett huvudkonto för tjänsten

Vanligtvis beviljas programmatisk åtkomst till Azure-resurser via ett dedikerat konto i stället för dina egna användaruppgifter. Dessa dedikerade konton kallas "tjänstens huvudnamn"-konton. Om du vill använda exempelprojektet Azure DNS SDK måste du först skapa ett huvudkonto för tjänsten och tilldela det rätt behörigheter.

1. Följ [dessa instruktioner](../active-directory/develop/howto-authenticate-service-principal-powershell.md) för att skapa ett huvudkonto för tjänsten (azure DNS SDK-exempelprojektet förutsätter lösenordsbaserad autentisering.)
2. Skapa en resursgrupp ([så här gör du](../azure-resource-manager/templates/deploy-portal.md)).
3. Använd Azure RBAC för att bevilja tjänstens huvudkonto DNS Zone Contributor-behörigheter till resursgruppen ([så här](../role-based-access-control/role-assignments-portal.md)gör du .)
4. Om du använder exempelprojektet Azure DNS SDK redigerar du filen program.cs enligt följande:

   * Infoga rätt värden `tenantId`för `clientId` , (kallas även `secret` konto-ID), (tjänstens huvudkontolösenord) och `subscriptionId` som används i steg 1.
   * Ange det resursgruppsnamn som valts i steg 2.
   * Ange ett DNS-zonnamn som du väljer.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-paket och namnområdesdeklarationer

Om du vill använda Azure DNS .NET SDK måste du installera **Azure DNS Management Library** NuGet-paketet och andra nödvändiga Azure-paket.

1. Öppna ett projekt eller ett nytt projekt i **Visual Studio.**
2. Gå till **Tools** **>** **NuGet Package Manager** **>** **Hantera NuGet Paket för lösning...**.
3. Klicka på **Bläddra**, aktivera kryssrutan **Inkludera förhandsversion** och skriv **Microsoft.Azure.Management.Dns** i sökrutan.
4. Markera paketet och klicka på **Installera** om du vill lägga till det i Visual Studio-projektet.
5. Upprepa processen ovan för att även installera följande paket: **Microsoft.Rest.ClientRuntime.Azure.Authentication** och **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Lägga till namnrymdsdeklarationer

Lägga till följande namnområdesdeklarationer

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Initiera DNS-hanteringsklienten

Innehåller `DnsManagementClient` de metoder och egenskaper som krävs för att hantera DNS-zoner och postuppsättningar.  Följande kod loggar in på tjänstens huvudkonto och skapar ett `DnsManagementClient` objekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Skapa eller uppdatera en DNS-zon

Om du vill skapa en DNS-zon skapas först ett "Zone"-objekt för att innehålla DNS-zonparametrarna. Eftersom DNS-zoner inte är länkade till en viss region är platsen inställd på "global". I det här exemplet läggs även en [Azure Resource Manager-tagg](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) till i zonen.

Om du faktiskt vill skapa eller uppdatera zonen i Azure DNS `DnsManagementClient.Zones.CreateOrUpdateAsyc` skickas zonobjektet som innehåller zonparametrarna till metoden.

> [!NOTE]
> DnsManagementClient stöder tre driftslägen: synkron ("CreateOrUpdate"),asynkron ("CreateOrUpdateAsync") eller asynkron med åtkomst till HTTP-svaret ("CreateOrUpdateWithHttpMessagesAsync").  Du kan välja något av dessa lägen, beroende på ditt programbehov.

Azure DNS stöder optimistisk samtidighet, som kallas [Etags](dns-getstarted-create-dnszone.md). I det här exemplet anger du "*" för huvudet "If-None-Match" säger till Azure DNS att skapa en DNS-zon om det inte redan finns någon.  Anropet misslyckas om det redan finns en zon med förnamnet i den angivna resursgruppen.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Skapa DNS-postuppsättningar och poster

DNS-poster hanteras som en postuppsättning. En postuppsättning är en uppsättning poster med samma namn och posttyp inom en zon.  Postuppsättningsnamnet är relativt till zonnamnet, inte det fullständigt kvalificerade DNS-namnet.

Om du vill skapa eller uppdatera en postuppsättning skapas ett `DnsManagementClient.RecordSets.CreateOrUpdateAsync`parameterobjekt för "RecordSet" och skickas till . Precis som med DNS-zoner finns det tre driftslägen: synkron ("CreateOrUpdate"),asynkron ("CreateOrUpdateAsync") eller asynkron med åtkomst till HTTP-svaret ("CreateOrUpdateWithHttpMessagesAsync").

Precis som med DNS-zoner innehåller åtgärder på postuppsättningar stöd för optimistisk samtidighet.  I det här exemplet, eftersom varken "If-Match" eller "If-None-Match" anges, skapas postuppsättningen alltid.  Det här anropet skriver över alla befintliga postuppsättningar med samma namn och posttyp i den här DNS-zonen.

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

`DnsManagementClient.Zones.Get` Metoderna `DnsManagementClient.RecordSets.Get` och hämtar enskilda zoner respektive postuppsättningar. RecordSets identifieras efter typ, namn och zon och resursgrupp som de finns i. Zoner identifieras med sitt namn och den resursgrupp de finns i.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Uppdatera en befintlig postuppsättning

Om du vill uppdatera en befintlig DNS-postuppsättning hämtar du först postuppsättningen och uppdaterar sedan postuppsättningens innehåll och skickar sedan ändringen.  I det här exemplet anger vi "Etag" från den hämtade posten som anges i parametern "If-Match". Anropet misslyckas om en samtidig åtgärd har ändrat postuppsättningen under tiden.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Listzoner och postuppsättningar

Om du vill visa zoner använder du metoderna *DnsManagementClient.Zones.List...* som stöder listningen antingen alla zoner i en viss resursgrupp eller alla zoner i en viss Azure-prenumeration (över resursgrupper.) Om du vill visa postuppsättningar använder du metoder för *DnsManagementClient.RecordSets.List...* som stöder antingen en lista över alla postuppsättningar i en viss zon eller endast de postuppsättningar av en viss typ.

Observera att när du listar zoner och postuppsättningar kan resultaten sidnumreras.  I följande exempel visas hur du itererar genom resultatsidorna. (En artificiellt liten sidstorlek på "2" används för att tvinga fram personsökning, i praktiken bör den här parametern utelämnas och standardsidans storlek användas.)

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

Hämta [exempelprojektet Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), som innehåller ytterligare exempel på hur du använder Azure DNS .NET SDK, inklusive exempel för andra DNS-posttyper.
