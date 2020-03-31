---
title: DNS-tjänst för Azure Service Fabric
description: Använd Service Fabric dns-tjänst för att upptäcka mikrotjänster inifrån klustret.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458025"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS-tjänsten i Azure Service Fabric
DNS-tjänsten är en valfri systemtjänst som du kan aktivera i klustret för att identifiera andra tjänster med DNS-protokollet. 

Många tjänster, särskilt behållartjänster, kan adresseras via en befintlig URL. Det är önskvärt att kunna lösa dessa tjänster med hjälp av standard-DNS-protokollet, i stället för service fabric-namngivningstjänstprotokollet. Dns-tjänsten gör att du kan mappa DNS-namn till ett tjänstnamn och därmed lösa IP-adresser för slutpunkter. Sådana funktioner upprätthåller portabiliteten för containeriserade tjänster på olika plattformar och kan göra det enklare att lyfta och flytta scenarier genom att låta dig använda befintliga tjänstadresser i stället för att behöva skriva om kod för att utnyttja namngivningstjänsten. 

DNS-tjänsten mappar DNS-namn till tjänstnamn, som i sin tur matchas av namngivningstjänsten för att returnera tjänstens slutpunkt. DNS-namnet för tjänsten anges när den skapas. Följande diagram visar hur DNS-tjänsten fungerar för tillståndslösa tjänster.

![tjänstens slutpunkter](./media/service-fabric-dnsservice/stateless-dns.png)

Från och med Service Fabric version 6.3 har DNS-protokollet Service Fabric utökats till att omfatta ett schema för adressering av partitionerade tillståndskänsliga tjänster. Dessa tillägg gör det möjligt att lösa specifika partitions-IP-adresser med hjälp av en kombination av tillståndskänsligt tjänst DNS-namn och partitionsnamnet. Alla tre partitioneringsscheman stöds:

- Namngiven partitionering
- Varierad partitionering
- Singleton-partitionering

Följande diagram visar hur DNS-tjänsten fungerar för partitionerade tillståndskänsliga tjänster.

![tillståndskänsliga tjänstslutpunkter](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamiska portar stöds inte av DNS-tjänsten. Om du vill lösa tjänster som exponeras på dynamiska portar använder du den [omvända proxytjänsten](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Aktivera DNS-tjänsten
> [!NOTE]
> DNS-tjänsten för Service Fabric-tjänster stöds ännu inte på Linux.

När du skapar ett kluster med portalen aktiveras DNS-tjänsten som standard i kryssrutan **Inkludera DNS-tjänst** på **klusterkonfigurationsmenyn:**

![Aktivera DNS-tjänst via portalen](./media/service-fabric-dnsservice/enable-dns-service.png)

Om du inte använder portalen för att skapa klustret eller om du uppdaterar ett befintligt kluster måste du aktivera DNS-tjänsten i en mall:

- Om du vill distribuera ett nytt kluster kan du antingen använda [exempelmallarna](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en egen Resource Manager-mall. 
- Om du vill uppdatera ett befintligt kluster kan du navigera till klustrets resursgrupp på portalen och klicka på **Automation Script** för att arbeta med en mall som återspeglar det aktuella tillståndet för klustret och andra resurser i gruppen. Mer information finns i [Exportera mallen från resursgruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

När du har en mall kan du aktivera DNS-tjänsten med följande steg:

1. Kontrollera att `apiversion` resursen `2017-07-01-preview` är inställd `Microsoft.ServiceFabric/clusters` på eller senare och, om inte, uppdatera den enligt följande exempel:

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

   - Om du vill aktivera DNS-tjänsten med `addonFeatures` standardinställningar `properties` lägger du till den i avsnittet i avsnittet enligt följande exempel:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Om du vill aktivera tjänsten med `DnsService` andra än `fabricSettings` standardinställningar `properties` lägger du till ett avsnitt i avsnittet i avsnittet. I det här fallet behöver du inte lägga `addonFeatures`till DnsService i . Mer information om vilka egenskaper som kan ställas in för DNS-tjänsten finns i [DNS-tjänstinställningarna](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. När du har uppdaterat klustermallen med ändringarna använder du dem och låter uppgraderingen slutföras. När uppgraderingen är klar börjar DNS-systemtjänsten köras i klustret. Tjänstnamnet är `fabric:/System/DnsService`och du hittar det under avsnittet **Systemtjänst** i Service Fabric Explorer. 

> [!NOTE]
> När du uppgraderar DNS från inaktiverat till aktiverat, kanske Service Fabric Explorer inte återspeglar det nya tillståndet. Lös genom att ändra UpgradePolicy i mallen Azure Resource Manager. Mer information finns i [mallreferensen för serviceinfrastruktur.](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications)

> [!NOTE]
> Om du aktiverar DNS-tjänsten när du utvecklar på en lokal dator åsidosätts vissa DNS-inställningar. Om du får problem med att ansluta till internet kontrollerar du DNS-inställningarna.

## <a name="setting-the-dns-name-for-your-service"></a>Ställa in DNS-namnet för tjänsten
Du kan ange ett DNS-namn för dina tjänster antingen deklarativt för standardtjänster i filen ApplicationManifest.xml eller via PowerShell-kommandon.

DNS-namnet för tjänsten kan lösas upp i hela klustret, så det är viktigt att se till att DNS-namnet är unikt i klustret. 

Vi rekommenderar starkt att du använder `<ServiceDnsName>.<AppInstanceName>`ett namngivningsschema för ; till exempel `service1.application1`. Om ett program distribueras med Docker-sammansättning tilldelas tjänster automatiskt DNS-namn med det här namngivningsschemat.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ange DNS-namnet för en standardtjänst i ApplicationManifest.xml
Öppna projektet i Visual Studio eller din favoritredigerare och öppna filen ApplicationManifest.xml. Gå till standardtjänstavsnittet och lägg `ServiceDnsName` till attributet för varje tjänst. I följande exempel visas hur du ställer in DNS-namnet på tjänsten på`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
När programmet har distribuerats visar tjänstinstansen i Service Fabric-utforskaren DNS-namnet för den här instansen, vilket visas i följande bild: 

![tjänstens slutpunkter](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

I följande exempel anges DNS-namnet för `statefulsvc.app`en tillståndskänslig tjänst till . Tjänsten använder ett namngivet partitioneringsschema. Observera att partitionsnamnen är gemener. Detta är ett krav för partitioner som ska vara inriktade på DNS-frågor. Mer information finns i [Göra DNS-frågor på en tillståndskänslig tjänstpartition](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ange DNS-namnet för en tjänst med Powershell
Du kan ange DNS-namnet för en `New-ServiceFabricService` tjänst när du skapar den med powershell-kommandot. I följande exempel skapas en ny tillståndslös tjänst med DNS-namnet`service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Förhandsgranska] Göra DNS-frågor på en tillståndskänslig tjänstpartition
Från och med Service Fabric version 6.3 stöder TJÄNSTEN Service Fabric DNS-tjänsten frågor om tjänstpartitioner.

För partitioner som ska användas i DNS-frågor gäller följande namngivningsbegränsningar:

   - Partitionsnamn bör vara DNS-kompatibla.
   - Partitionsnamn med flera etiketter (som innehåller punkt,'., i namnet) bör inte användas.
   - Partitionsnamn bör vara gemener.

DNS-frågor som är inriktade på en partition formateras på följande sätt:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Där:

- *First-Label-Of-Partitioned-Service-DNSName* är den första delen av tjänstens DNS-namn.
- *PartitionPrefix* är ett värde som kan anges i avsnittet DnsService i klustermanifestet eller via klustrets Resource Manager-mall. Standardvärdet är "--". Mer information finns i [DNS-tjänstinställningar .](./service-fabric-cluster-fabric-settings.md#dnsservice)
- *Target-Partition-Name* är namnet på partitionen. 
- *PartitionSuffix* är ett värde som kan anges i avsnittet DnsService i klustermanifestet eller via klustrets Resource Manager-mall. Standardvärdet är tom sträng. Mer information finns i [DNS-tjänstinställningar .](./service-fabric-cluster-fabric-settings.md#dnsservice)
- *Återstående partitionerad tjänst-DNSName* är den återstående delen av tjänstens DNS-namn.

I följande exempel visas DNS-frågor för partitionerade tjänster som `PartitionPrefix` `PartitionSuffix`körs i ett kluster som har standardinställningar för och: 

- Om du vill matcha partitionen "0" för en tjänst med DNS-namn `backendrangedschemesvc.application` som använder ett intervallpartitioneringsschema använder du `backendrangedschemesvc-0.application`.
- Om du vill matcha partitionen "först" för en tjänst med DNS-namn `backendnamedschemesvc.application` som använder ett namngivet partitioneringsschema använder du `backendnamedschemesvc-first.application`.

DNS-tjänsten returnerar IP-adressen för den primära repliken för partitionen. Om ingen partition anges returnerar tjänsten IP-adressen för den primära repliken för en slumpmässigt vald partition.

## <a name="using-dns-in-your-services"></a>Använda DNS i dina tjänster
Om du distribuerar mer än en tjänst kan du hitta slutpunkterna för andra tjänster att kommunicera med med hjälp av ett DNS-namn. DNS-tjänsten fungerar för tillståndslösa tjänster och i Service Fabric version 6.3 och senare för tillståndskänsliga tjänster. För tillståndskänsliga tjänster som körs på versioner av Service Fabric före 6.3 kan du använda den inbyggda [omvänd proxytjänsten](./service-fabric-reverseproxy.md) för http-anrop för att anropa en viss tjänstpartition. 

Dynamiska portar stöds inte av DNS-tjänsten. Du kan använda den omvända proxytjänsten för att matcha tjänster som använder dynamiska portar.

Följande kod visar hur du anropar en tillståndslös tjänst via DNS. Det är helt enkelt ett vanligt http-anrop där du anger DNS-namnet, porten och valfri sökväg som en del av webbadressen.

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

Följande kod visar ett anrop på en specifik partition av en tillståndskänslig tjänst. I det här fallet innehåller DNS-namnet partitionsnamnet (partition1). Anropet förutsätter ett kluster `PartitionPrefix` `PartitionSuffix`med standardvärden för och .

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
* För Service Fabric-versionerna 6.3 och senare finns det ett problem med DNS-sökning för tjänstnamn som innehåller ett bindestreck i DNS-namnet. Om du vill ha mer information om det här problemet kan du spåra följande [GitHub-problem](https://github.com/Azure/service-fabric-issues/issues/1197). En fix för detta kommer i nästa 6,3 uppdatering. 

* DNS-tjänsten för Service Fabric-tjänster stöds ännu inte på Linux. DNS-tjänsten stöds för behållare på Linux. Manuell upplösning med Fabric Client/ServicePartitionResolver är det tillgängliga alternativet.

## <a name="next-steps"></a>Nästa steg
Läs mer om servicekommunikation i klustret med [kontakt och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md)

