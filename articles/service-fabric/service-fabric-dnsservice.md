---
title: Azure Service Fabric DNS-tjänsten | Microsoft Docs
description: Använda Service Fabric dns-tjänsten för identifiering av mikrotjänster från i klustret.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: msfussell
ms.openlocfilehash: b49df76bc5b9c4d5f469e702b65ffd5762e49a39
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455844"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS-tjänsten i Azure Service Fabric
DNS-tjänsten är en valfri systemtjänst som du kan aktivera i klustret för att identifiera andra tjänster med hjälp av DNS-protokollet. 

Många tjänster, särskilt tjänster i behållare, är adresserbara via en befintlig URL. Kan lösa dessa tjänster med hjälp av DNS-standardprotokollet i stället för namngivning i Service Fabric-protokollet är önskvärt. DNS-tjänsten kan du mappa DNS-namn till ett tjänstnamn och kan därför matcha IP-adresser för slutpunkt. Sådan funktion underhåller blir mer flexibel med tjänster i behållare på olika plattformar och kan göra ”lift and shift” scenarier enklare genom att du kan använda befintliga service URL: er istället för att behöva skriva om kod för att använda tjänsten Naming. 

Tjänsten DNS matchar DNS-namn till tjänstnamn, vilket i sin tur kan matchas med Namngivningstjänsten för att returnera tjänstslutpunkten. DNS-namn för tjänsten tillhandahålls vid tidpunkten för skapandet. Följande diagram visar hur tjänsten DNS fungerar för tillståndslösa tjänster.

![Tjänsteslutpunkter](./media/service-fabric-dnsservice/stateless-dns.png)

Från och med Service Fabric version 6.3, har Service Fabric DNS-protokollet utökats och omfattar ett schema för adressering partitionerade tillståndskänsliga tjänster. De här tilläggen gör det möjligt att matcha IP-adresser för specifik partition med hjälp av en kombination av tillståndskänslig tjänst DNS-namn och partitionsnamnet. Alla tre partitioneringsscheman stöds:

- Med namnet partitionering
- Intervallet partitionering
- Singleton-partitionering

Följande diagram visar hur tjänsten DNS fungerar för partitionerade tillståndskänsliga tjänster.

![tillståndskänsliga Tjänsteslutpunkter](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamiska portar stöds inte av DNS-tjänsten. Lös tjänster som är tillgängliga på dynamiska portar med de [omvänd proxy-tjänst](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Aktivera DNS-tjänsten
> [!NOTE]
> DNS-tjänst för Service Fabric-tjänster stöds inte ännu i Linux.

När du skapar ett kluster med hjälp av portalen, DNS-tjänsten är aktiverad som standard i den **inkluderar DNS-tjänsten** kryssrutan på den **klusterkonfiguration** menyn:

![Aktivera DNS-tjänsten via portalen](./media/service-fabric-dnsservice/enable-dns-service.png)

Om du inte använder portalen för att skapa klustret eller om du uppdaterar ett befintligt kluster, måste du aktivera DNS-tjänsten i en mall:

- Om du vill distribuera ett nytt kluster, du kan använda den [exempel på mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en egen Resource Manager-mall. 
- Om du vill uppdatera ett befintligt kluster som du kan navigera till resursgruppen på portalen och klicka på **Automationsskript** att arbeta med en mall som representerar resursgruppens aktuella tillstånd för klustret och andra resurser i gruppen. Mer information finns i [Exportera mallen från resursgruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

När du har skapat en mall kan aktivera du DNS-tjänsten med följande steg:

1. Kontrollera att den `apiversion` är inställd på `2017-07-01-preview` eller senare för den `Microsoft.ServiceFabric/clusters` resurs, och, om inte, uppdatera det som visas i följande exempel:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera nu DNS-tjänsten på något av följande sätt:

   - Om du vill aktivera DNS-tjänsten med standardinställningar, lägger du till den i den `addonFeatures` avsnitt inuti den `properties` som visas i följande exempel:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - Om du vill aktivera tjänsten med än standardinställningarna, lägger du till en `DnsService` avsnitt i den `fabricSettings` avsnitt inuti den `properties` avsnittet. I detta fall använder du behöver inte lägga till DnsService till `addonFeatures`. Läs mer om de egenskaper som kan anges för DNS-tjänsten i [DNS-tjänstinställningar](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. När du har uppdaterat mallen kluster med ändringarna, tillämpar dem och låta uppgraderingen slutförts. När uppgraderingen är klar startar tjänsten DNS-system som körs i klustret. Tjänstnamnet är `fabric:/System/DnsService`, och du hittar den under den **System** tjänsten avsnittet i Service Fabric explorer. 


## <a name="setting-the-dns-name-for-your-service"></a>Ange DNS-namn för din tjänst
Du kan ange ett DNS-namn för dina tjänster antingen deklarativt för standardtjänster i filen applicationmanifest.XML eller via PowerShell-kommandon.

DNS-namnet för din tjänst är matchas i hela klustret så att det är viktigt att se till att DNS-namnet är unikt i klustret. 

Vi rekommenderar starkt att du använder en namngivningsschemat för `<ServiceDnsName>.<AppInstanceName>`, till exempel `service1.application1`. Om ett program distribueras med hjälp av Docker compose, tjänster tilldelas automatiskt DNS-namn med hjälp av den här namngivningsschemat.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ange DNS-namnet för en standardtjänst i ApplicationManifest.xml
Öppna projektet i Visual Studio eller din favoritredigerare och öppna filen applicationmanifest.XML. Gå till avsnittet standard tjänster och för varje tjänst lägger du till den `ServiceDnsName` attribut. I följande exempel visas hur du ställer in DNS-namnet på tjänsten `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
När programmet har distribuerats, visar tjänstinstansen i Service Fabric explorer DNS-namnet för den här instansen, enligt följande bild: 

![Tjänsteslutpunkter](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

I följande exempel anger DNS-namnet för en tillståndskänslig tjänst till `statefulsvc.app`. Tjänsten använder en namngiven partitioneringsschema. Observera att partitionsnamnen på är gemena. Det här är ett krav för partitioner som ska användas i DNS-frågor Mer information finns i [gör DNS-frågor på en tillståndskänslig tjänst partition](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ange DNS-namnet för en tjänst med hjälp av Powershell
Du kan ange DNS-namnet för en tjänst när du skapar den med hjälp av den `New-ServiceFabricService` Powershell-kommando. I följande exempel skapas en ny tillståndslös tjänst med DNS-namn `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Förhandsversion] Gör DNS-frågor på en tillståndskänslig tjänst-partition
Från och med Service Fabric version 6.3, stöder Service Fabric DNS-tjänsten frågor för tjänstpartitioner.

Följande namngivningsbegränsningar gäller för partitioner som ska användas i DNS-frågor:

   - Partitionsnamn måste vara DNS-kompatibel.
   - Flera etikett partitionsnamn (som omfattar punkt, '.', i namnet) ska inte användas.
   - Partitionsnamn på måste vara gemener.

DNS-frågor som är riktade till en partition är formaterat på följande sätt:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Där:

- *First-Label-Of-Partitioned-Service-DNSName* är den första delen av din DNS-namn.
- *PartitionPrefix* är ett värde som kan anges i avsnittet DnsService av klustermanifestet eller via klustrets Resource Manager-mall. Standardvärdet är ”-”. Mer information finns i [DNS-tjänstinställningar](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target partitionsnamnet* är namnet på partitionen. 
- *PartitionSuffix* är ett värde som kan anges i avsnittet DnsService av klustermanifestet eller via klustrets Resource Manager-mall. Standardvärdet är tom sträng. Mer information finns i [DNS-tjänstinställningar](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Återstående-partitionerad-Service-DNSName* återstående ingår i din DNS-namn.

I följande exempel visas DNS-frågor för partitionerade tjänster som körs på ett kluster med standardinställningarna för `PartitionPrefix` och `PartitionSuffix`: 

- Du löser partitionen ”0” för en tjänst med DNS-namnet `backendrangedschemesvc.application` som använder ett ranged partitioneringsschema, använda `backendrangedschemesvc-0.application`.
- Du löser partitionen ”första” av en tjänst med DNS-namnet `backendnamedschemesvc.application` som använder en namngiven partitioneringsschemat, använda `backendnamedschemesvc-first.application`.

DNS-tjänsten returnerar IP-adressen för den primära repliken av partitionen. Om ingen partition har angetts returnerar tjänsten IP-adressen för den primära repliken av en slumpmässigt valda partition.

## <a name="using-dns-in-your-services"></a>Med DNS i dina tjänster
Om du distribuerar flera tjänster kan hitta du slutpunkterna för andra tjänster för att kommunicera med med hjälp av ett DNS-namn. DNS-tjänsten fungerar för tillståndslösa tjänster och, i Service Fabric version 6.3 och senare för tillståndskänsliga tjänster. För tillståndskänsliga tjänster som körs på versioner av Service Fabric innan 6.3, kan du använda inbyggt [omvänd proxy-tjänst](./service-fabric-reverseproxy.md) för http-anrop att anropa en viss tjänst-partition. 

Dynamiska portar stöds inte av DNS-tjänsten. Du kan använda omvänd proxy-tjänsten för att lösa tjänster som använder dynamiska portar.

Följande kod visar hur du anropar en tillståndslös tjänst via DNS. Det är bara en vanlig http-anrop, där du anger DNS-namnet, porten och eventuella valfria sökväg som en del av URL: en.

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

Följande kod visar ett anrop på en specifik partition på en tillståndskänslig tjänst. I det här fallet innehåller DNS-namnet partitionsnamnet (partition1). Anropet förutsätter att ett kluster med standardvärden för `PartitionPrefix` och `PartitionSuffix`.

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

## <a name="known-issues"></a>Kända problem
* Det finns ett problem med DNS-sökning för tjänstnamn som innehåller ett bindestreck i DNS-namnet för Service Fabric versioner 6.3 och högre. Mer information om det här problemet spåra följande [GitHub-ärende](https://github.com/Azure/service-fabric-issues/issues/1197). En korrigering för detta kommer i nästa 6.3-uppdateringen. 

* DNS-tjänst för Service Fabric-tjänster stöds inte ännu i Linux. DNS-tjänsten har stöd för behållare på Linux. Manuell lösning med hjälp av Fabric klient/ServicePartitionResolver är tillgängliga alternativ.

## <a name="next-steps"></a>Nästa steg
Mer information om kommunikation inom klustret med [ansluta och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md)

