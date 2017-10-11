---
title: "Azure Service Fabric DNS-tjänsten | Microsoft Docs"
description: "Använda Service Fabric DNS-tjänsten för identifiering av mikrotjänster från i klustret."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS-tjänsten i Azure Service Fabric
DNS-tjänsten är en valfri systemtjänst som du kan aktivera i klustret för att identifiera andra tjänster med hjälp av DNS-protokollet.

Många tjänster, särskilt av tjänster, kan ha ett befintligt URL-namn och att kunna lösa dem med hjälp av DNS-standardprotokollet (i stället för protokollet Naming Service) är önskvärt, särskilt i ”lyfta och flytta” scenarier. DNS-tjänsten kan du mappa DNS-namn till ett namn och därför matcha IP-adresser för slutpunkt. 

Tjänsten DNS matchar DNS-namn till tjänstnamn som i sin tur kan matchas med namngivningstjänst att returnera tjänstslutpunkten. DNS-namn för tjänsten tillhandahålls vid tidpunkten för skapandet. 

![slutpunkter][0]

## <a name="enabling-the-dns-service"></a>Aktivera DNS-tjänsten
Du måste först aktivera tjänsten DNS i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda den [exempel mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en Resource Manager-mall. Du kan aktivera DNS-tjänsten med följande steg:

1. Kontrollera att den `apiversion` är inställd på `2017-07-01-preview` för den `Microsoft.ServiceFabric/clusters` resursen, och om inte, uppdatera det som visas i följande utdrag:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nu aktivera DNS-tjänsten genom att lägga till följande `addonFeatures` avsnittet efter den `fabricSettings` avsnittet som visas i följande utdrag: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. När du har uppdaterat mallen för kluster med föregående ändringarna kan använda dem och låta uppgraderingen slutförts. När installationen är klar startas tjänsten DNS-systemet körs i klustret som kallas `fabric:/System/DnsService` under system service-avsnittet i Service Fabric explorer. 

Du kan också aktivera DNS-tjänsten via portalen när klustret skapas. DNS-tjänsten kan aktiveras med en kryssruta för `Include DNS service` i den `Cluster configuration` menyn som visas i följande skärmbild:

![Aktivera DNS-tjänsten via portalen][2]


## <a name="setting-the-dns-name-for-your-service"></a>Ange ett DNS-namn för tjänsten
När DNS-tjänsten körs i klustret, kan du ange ett DNS-namn för dina tjänster deklarativt för standardtjänster i antingen den `ApplicationManifest.xml` eller via Powershell-kommandon.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ange DNS-namnet för en standardtjänst i ApplicationManifest.xml
Öppna projektet i Visual Studio eller ditt favoritprogram redigerare och öppna den `ApplicationManifest.xml` filen. Gå till avsnittet standard tjänster och för varje tjänst lägga till den `ServiceDnsName` attribut. I följande exempel visas hur du anger DNS-namnet på tjänsten`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
När programmet har distribuerats, service-instans i Service Fabric explorer visar DNS-namn för den här instansen, som visas i följande bild: 

![slutpunkter][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ange DNS-namnet för en tjänst med Powershell
Du kan ange DNS-namnet för en tjänst när du skapar den med hjälp av den `New-ServiceFabricService` Powershell. I följande exempel skapas en ny tillståndslös tjänst med DNS-namn`service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Med hjälp av DNS i dina tjänster
Om du distribuerar mer än en tjänst hittar du slutpunkter av andra tjänster kan kommunicera med genom att använda en DNS-namn. DNS-tjänsten kan bara användas för tillståndslösa tjänster, eftersom DNS-protokollet inte kan kommunicera med tillståndskänsliga tjänster. Du kan använda inbyggda omvänd proxy för http-anrop för tillståndskänsliga tjänster för att anropa en viss tjänst partition.

Följande kod visar hur du anropar en annan tjänst som ger helt enkelt en vanliga http-anropet du porten och en valfri sökväg som en del av URL: en.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Nästa steg
Mer information om kommunikation inom klustret med [ansluta och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
