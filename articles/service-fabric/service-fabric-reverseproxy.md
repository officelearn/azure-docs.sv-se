---
title: "Azure Service Fabric omvänd proxy | Microsoft Docs"
description: "Använda Service Fabric omvänd proxy för kommunikation med mikrotjänster från inom och utanför klustret."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 7f29860519d4dce76f0b7f866852484b93ce7b02
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Omvänd proxy i Azure Service Fabric
Omvänd proxy som är inbyggda i Azure Service Fabric hjälper mikrotjänster som körs i ett Service Fabric-kluster identifiera och kommunicera med andra tjänster som har HTTP-slutpunkter.

## <a name="microservices-communication-model"></a>Mikrotjänster kommunikation modellen
Mikrotjänster i Service Fabric körs på en delmängd av noderna i klustret och kan migrera mellan noder av olika skäl. Därför kan slutpunkterna för mikrotjänster ändras dynamiskt. Mikrotjänster måste gå igenom följande steg för att identifiera och kommunicera med andra tjänster i klustret:

1. Lös tjänstlokalisering via naming service.
2. Ansluta till tjänsten.
3. Omsluta ovanstående steg i en loop som implementerar tjänsten upplösning och försök principer tillämpas på anslutningsfel

Mer information finns i [Connect och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunicerar med hjälp av omvänd proxy
Omvänd proxy är en tjänst som körs på varje nod och hanterar slutpunktsmappning, automatiska återförsök och andra anslutningsfel för klienttjänster. Omvänd proxy kan konfigureras för att tillämpa olika principer som den hanterar begäranden från klienttjänster. Om du använder en omvänd proxy kan klienttjänsten att använda alla bibliotek för klientsidan http-kommunikation och inte kräver särskilda upplösning och försök logiken i tjänsten. 

Omvänd proxy Exponerar en eller flera slutpunkter på lokala noden för klienttjänster som ska användas för att skicka förfrågningar till andra tjänster.

![Intern kommunikation][1]

> **Plattformar som stöds**
>
> Omvänd proxy i Service Fabric stöder för närvarande följande plattformar
> * *Windows-kluster*: Windows 8 och senare eller Windows Server 2012 och senare
> * *Linux-kluster*: omvänd Proxy är inte tillgänglig för Linux-kluster

## <a name="reaching-microservices-from-outside-the-cluster"></a>Nå mikrotjänster från utanför klustret
Standardmodell för extern kommunikation för mikrotjänster är en opt-in-modell där varje tjänst inte kan nås direkt från externa klienter. [Azure belastningsutjämnare](../load-balancer/load-balancer-overview.md), vilket är en nätverksgräns mellan mikrotjänster och externa klienter utför nätverksadresser och vidarebefordrar externa begäranden till interna IP:port slutpunkter. Om du vill göra en mikrotjänster endpoint direkt tillgänglig för externa klienter, måste du först konfigurera belastningsfördelning, så att vidarebefordra trafik till varje port som tjänsten använder i klustret. De flesta mikrotjänster, särskilt tillståndskänslig mikrotjänster Direktmigrering inte dessutom på alla noder i klustret. Mikrotjänster kan flytta mellan noder på redundanskluster. I sådana fall kan inte belastningsutjämnaren effektivt fastställa platsen för målnoden repliker som den ska vidarebefordra trafik.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Nå mikrotjänster via omvänd proxy från utanför klustret
Du kan konfigurera precis porten för omvänd proxy i belastningsutjämnaren i stället för att konfigurera porten för en enskild tjänst i belastningsutjämnaren. Den här konfigurationen kan klienter utanför klustret nå tjänster i klustret via omvänd proxy utan ytterligare konfiguration.

![Extern kommunikation][0]

> [!WARNING]
> När du konfigurerar omvänd proxy-port i belastningsutjämnaren adresseras alla mikrotjänster i klustret som Exponerar en HTTP-slutpunkt från utanför klustret.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-format för adressering tjänster med hjälp av omvänd proxy
Omvänd proxy använder formatet specifika uniform resource identifier (URI) för att identifiera tjänsten partitionen som den inkommande begäranden ska vidarebefordras:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** omvänd proxy kan konfigureras för att godkänna HTTP eller HTTPS-trafik. HTTPS-vidarebefordran finns [Anslut till en säker tjänst med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md) när du har konfigurerat omvänd proxy ska lyssna på HTTPS.
* **Klustret fullständigt kvalificerade domännamnet (FQDN) | intern IP-adress:** för externa klienter kan du konfigurera omvänd proxy så att den kan nås via klusterdomänen, till exempel mycluster.eastus.cloudapp.azure.com. Som standard körs omvänd proxy på varje nod. För intern trafik kan omvänd proxy nås på localhost eller på alla interna noden IP-adresser, t.ex 10.0.0.1.
* **Port:** porten, till exempel 19081, som har angetts för omvänd proxy.
* **ServiceInstanceName:** detta är det fullständigt kvalificerade namnet på den distribuerade tjänst-instans som du försöker nå utan den ”fabric: /” schema. Till exempel för att nå den *fabric: / myapp/myservice/* tjänsten, som du vill använda *myapp/myservice*.

    Service-instansen är skiftlägeskänsliga. Med hjälp av ett annat skiftläge för tjänsten instansnamnet i URL: en medför begäranden att misslyckas med 404 (inget hittas).
* **Suffix sökväg:** detta är den faktiska URL-sökvägen som *myapi/värden/Lägg till/3*, för tjänsten som du vill ansluta till.
* **PartitionKey:** för en partitionerad tjänst är beräknad Partitionsnyckeln för den partition som du vill nå. Observera att detta *inte* partitions-ID-GUID. Den här parametern krävs inte för tjänster som använder partitionsschema singleton.
* **PartitionKind:** detta är partitionsschema för tjänsten. Detta kan vara 'Int64Range' eller 'Med namnet'. Den här parametern krävs inte för tjänster som använder partitionsschema singleton.
* **ListenerName** slutpunkter från tjänsten är i formatet {”slutpunkter”: {”Listener1”: ”slutpunkt 1”, ”Listener2”: ”Endpoint2”...}}. När tjänsten visar flera slutpunkter, identifierar den slutpunkt som klientbegäran ska vidarebefordras till. Detta kan utelämnas om tjänsten har endast en lyssnare.
* **TargetReplicaSelector** anger hur replikuppsättningens eller instans måste väljas.
  * När Måltjänsten är tillståndskänslig TargetReplicaSelector kan vara något av följande: 'PrimaryReplica', 'RandomSecondaryReplica' eller 'RandomReplica'. Om den här parametern inte anges är standardvärdet 'PrimaryReplica'.
  * När Måltjänsten är tillståndslös hämtar en slumpmässig instans av tjänsten partitionen att vidarebefordra begäran till omvänd proxy.
* **Timeout:** anger timeout för HTTP-begäran som skapats av omvänd proxy till tjänsten för klientbegäran. Standardvärdet är 60 sekunder. Det här är en valfri parameter.

### <a name="example-usage"></a>Exempel på användning
Exempelvis ta den *fabric: / MyApp/MyService* tjänst som öppnar en HTTP-lyssnare på följande URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Följande är resurserna för tjänsten:

* `/index.html`
* `/api/users/<userId>`

Om tjänsten använder singleton partitioneringsschema, den *PartitionKey* och *PartitionKind* frågan string-parametrar är inte obligatoriska och tjänsten kan nås via en gateway som:

* Externt:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internt:`http://localhost:19081/MyApp/MyService`

Om tjänsten använder partitioneringsschema Uniform Int64 den *PartitionKey* och *PartitionKind* frågan string-parametrar måste användas för att nå en partition av tjänsten:

* Externt:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internt:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

För att nå de resurser som tjänsten visar bara placera resursens sökväg efter tjänstnamnet i URL-Adressen:

* Externt:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internt:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Gatewayen kommer sedan att vidarebefordra dessa begäranden till tjänstens URL:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Särskild hantering för delning av port tjänster
Service Fabric omvänd proxy försöker lösa en tjänstadress igen och försök begäran när en tjänst inte kan nås. I allmänhet när en tjänst kan inte nås, service-instans eller replik har flyttats till en annan nod som en del av sin normala livscykel. När detta inträffar kan omvänd proxy får ett fel i anslutningen som anger att en slutpunkt är inte längre öppna den ursprungligen matcha adressen.

Dock replikeringar eller instanser av tjänsten kan dela en värdprocess och kan också dela en port när finns en http.sys-baserade webbservern, inklusive:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

I det här fallet är det troligt att webbservern är tillgängliga i värdprocessen och svarar på begäran, men löst tjänstinstansen eller repliken inte längre tillgänglig på värden. I det här fallet får gatewayen ett HTTP 404-svar från webbservern. Ett HTTP 404-svar kan därför ha två distinkta innebörd:

- Fall #1: serviceadressen är korrekt, men den resurs som användaren begärde finns inte.
- Fall #2: serviceadressen är felaktig och resursen som användaren har begärt kan finnas på en annan nod.

Det första fallet är en normal HTTP 404 som anses vara ett användarfel. I det andra fallet har dock användaren begärde en resurs som finns. Omvänd proxy kunde inte hitta den eftersom själva tjänsten har flyttats. Omvänd proxy måste matcha adressen igen och försök sedan begäran.

Omvänd proxy måste därför kan skilja mellan dessa två fall. För att göra denna skillnad, krävs en ledtråd från servern.

* Som standard omvänd proxy förutsätter fall #2 och försöker matcha och skicka begäran igen.
* Om du vill ange fall #1 till omvänd proxy ska tjänsten returnera följande HTTP-Svarsrubrik:

  `X-ServiceFabric : ResourceNotFound`

Den här HTTP-Svarsrubrik visar en normal HTTP 404-situation där den begärda resursen finns inte och omvänd proxy försöker inte matcha tjänstadressen igen.

## <a name="setup-and-configuration"></a>Installation och konfiguration

### <a name="enable-reverse-proxy-via-azure-portal"></a>Aktivera omvänd proxy via Azure-portalen

Azure-portalen innehåller ett alternativ för att aktivera omvänd proxy medan du skapar ett nytt Service Fabric-kluster.
Under **skapar Service Fabric-kluster**, steg 2: klusterkonfiguration, konfiguration av noden typ, markera kryssrutan ”Aktivera omvänd proxy”.
För att konfigurera säker omvänd proxy, SSL-certifikat kan anges i steg3: säkerhet, konfigurera säkerhetsinställningar för klustret, markera kryssrutan för att ”innehåller ett SSL-certifikat för omvänd proxy” och ange information om certifikat.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Aktivera omvänd proxy via Azure Resource Manager-mallar

Du kan använda den [Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) att aktivera omvänd proxy i Service Fabric för klustret.

Referera till [konfigurera HTTPS omvänd Proxy i ett kluster för säker](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) för Azure Resource Manager mallen exempel för att konfigurera säker omvänd proxy med förnya ett certifikat och hantering av certifikatet.

Först måste hämta du mallen för det kluster som du vill distribuera. Du kan använda exempelmallarna, eller så kan du skapa en anpassad mall för hanteraren för filserverresurser. Sedan kan du aktivera omvänd proxy med hjälp av följande steg:

1. Definiera en port för omvänd proxy i den [parametrar avsnittet](../azure-resource-manager/resource-group-authoring-templates.md) för mallen.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Ange porten för varje nodetype objekten i den **klustret** [typen avsnittet](../azure-resource-manager/resource-group-authoring-templates.md).

    Porten som identifieras av parameternamn, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Ställ in Azure belastningsutjämnare regler för den port som du angav i steg 1 för att åtgärda omvänd proxy från utanför Azure klustret.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Konfigurera SSL-certifikat på porten för omvänd proxy genom att lägga till certifikatet till den ***reverseProxyCertificate*** egenskap i den **klustret** [typen avsnittet](../resource-group-authoring-templates.md) .

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Stöd för en omvänd proxy-certifikat som skiljer sig från klustret certifikatet
 Om certifikatet omvänd proxy skiljer sig från det certifikat som skyddar klustret ska tidigare angivna certifikatet installeras på den virtuella datorn och därefter lagt till i åtkomstkontrollistan (ACL) så att Service Fabric kan komma åt den. Detta kan göras den **virtualMachineScaleSets** [typen avsnittet](../resource-group-authoring-templates.md). Lägga till certifikatet i osProfile för installation. Avsnittet tillägg i mallen kan uppdatera certifikat i Åtkomstkontrollistan.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> När du använder certifikat som skiljer sig från klustret certifikat för att aktivera omvänd proxy på ett befintligt kluster, installera certifikat för omvänd proxy och uppdatera ACL på klustret innan du aktiverar omvänd proxy. Slutför den [Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) distribution med hjälp av inställningarna som nämnts tidigare innan du startar en distribution för att aktivera omvänd proxy i steg 1 – 4.

## <a name="next-steps"></a>Nästa steg
* Se ett exempel på HTTP-kommunikation mellan tjänster i en [exempelprojektet på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Vidarebefordran till säker HTTP-tjänsten med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [RPC-anrop med Reliable Services fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikation med hjälp av Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* Ytterligare omvänd proxy konfigurationsalternativ finns ApplicationGateway/http-avsnittet i [anpassa Service Fabric-klusterinställningar](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
