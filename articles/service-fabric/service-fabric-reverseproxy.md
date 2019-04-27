---
title: Azure Service Fabric omvänd proxy | Microsoft Docs
description: Använda Service Fabric omvänd proxy för kommunikation till mikrotjänster från inuti och utanför klustret.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 6ce6f1f6559b43a64fb7edd0773a20f8ee0cf8a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837971"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Omvänd proxy i Azure Service Fabric
Omvänd proxy som är inbyggda i Azure Service Fabric hjälper mikrotjänster som körs i ett Service Fabric-kluster identifiera och kommunicera med andra tjänster som har http-slutpunkter.

## <a name="microservices-communication-model"></a>Modell med Mikrotjänster kommunikation
Mikrotjänster i Service Fabric körs på en delmängd av noderna i klustret och kan migrera mellan noder av olika anledningar. Slutpunkter för mikrotjänster kan därför ändras dynamiskt. För att identifiera och kommunicera med andra tjänster i klustret, måste mikrotjänst gå igenom följande steg:

1. Lös tjänstlokalisering via namngivningstjänsten.
2. Ansluta till tjänsten.
3. Omsluta föregående steg i en loop som implementerar tjänsten upplösning och försök principer tillämpas på anslutningsfel

Mer information finns i [Connect och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunicera med omvänd proxy
Omvänd proxy är en tjänst som körs på varje nod och hanterar slutpunktsmappning, automatiska återförsök och andra anslutningsfel för klienttjänster. Omvänd proxy kan konfigureras för att tillämpa olika principer som den hanterar förfrågningar från klienttjänster. Om du använder en omvänd proxy kan klienttjänsten kan använda alla databaskommunikationsbibliotek för klientsidan HTTP och inte kräver särskilda upplösning och logik för omprövning i tjänsten. 

Omvänd proxy Exponerar en eller flera slutpunkter på lokal nod för klienttjänster ska användas för att skicka förfrågningar till andra tjänster.

![Intern kommunikation][1]

> [!NOTE]
> **Plattformar som stöds**
>
> Omvänd proxy i Service Fabric stöder för närvarande följande plattformar
> * *Windows Cluster*: Windows 8 och senare eller Windows Server 2012 och senare
> * *Linux-kluster*: Omvänd Proxy är inte tillgänglig för Linux-kluster
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Nå mikrotjänster från utanför klustret
Extern kommunikation standardmodellen för mikrotjänster är en opt-in-modell där varje tjänst inte kan nås direkt från externa klienter. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), vilket är en nätverksgräns mellan mikrotjänster och externa klienter utför nätverksadresser och vidarebefordrar externa begäranden till interna IP:port slutpunkter. Om du vill göra en mikrotjänst slutpunkten direkt tillgänglig för externa klienter, måste du först konfigurera en belastningsutjämnare för att vidarebefordra trafik till varje port som tjänsten använder i klustret. Dessutom finns de flesta mikrotjänster, särskilt tillståndskänsliga mikrotjänster, inte på alla noder i klustret. Mikrotjänster kan flytta mellan noder i redundans. I sådana fall kan inte Load Balancer effektivt fastställa platsen för målnoden repliker som den ska vidarebefordra trafik.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Nå mikrotjänster via den omvända proxyn från utanför klustret
I stället för att konfigurera porten för en enskild tjänst i Load Balancer kan konfigurera du bara porten för omvänd proxy i belastningsutjämnaren. Den här konfigurationen kan klienter utanför klustret nå tjänster i klustret via omvänd proxy utan ytterligare konfiguration.

![Extern kommunikation][0]

> [!WARNING]
> När du konfigurerar den omvända proxyn port i belastningsutjämnaren är alla mikrotjänster i klustret som Exponerar en HTTP-slutpunkt adresserbara från utanför klustret. Det innebär att mikrotjänster ska vara interna kanske kan identifieras av en bestämd skadliga användare. Detta medför potentiellt allvarliga säkerhetsrisker som kan utnyttjas; till exempel:
>
> * En obehörig användare kan starta en DoS-angrepp genom att anropa en intern tjänst som inte har en tillräckligt strikta attackyta upprepade gånger.
> * En obehörig användare kan leverera felaktiga paket till en intern tjänst, vilket resulterar i oönskat beteende.
> * En tjänst som är avsedd att vara intern kan returnera privat eller känslig information som är inte avsedd att vara exponerat för tjänster utanför klustret, därför exponera känslig information till en obehörig användare. 
>
> Kontrollera att helt förstå och minimera de potentiella aspekterna av säkerhet för ditt kluster och de appar som körs på den, innan du gör omvänd proxy offentliga. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-format för att hantera tjänster med hjälp av omvänd proxy
Den omvända proxyn använder formatet specifika uniform resource identifier (URI) för att identifiera tjänsten partitionen som den inkommande begäranden ska vidarebefordras:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** Omvänd proxy kan konfigureras för att godkänna HTTP eller HTTPS-trafik. HTTPS-vidarebefordran finns [Anslut till en säker tjänst med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md) när du har konfigurerat för omvänd proxy ska lyssna på HTTPS.
* **Klustret fullständigt kvalificerade domännamnet (FQDN) | intern IP-adress:** Du kan konfigurera den omvända proxyn så att den kan nås via kluster-domän, till exempel mycluster.eastus.cloudapp.azure.com för externa klienter. Som standard körs den omvända proxyn på varje nod. För intern trafik kan du nått omvänd proxy på localhost eller på alla interna noden IP-adresser, t.ex 10.0.0.1.
* **Port:** Det här är port, till exempel 19081, som har angetts för den omvända proxyn.
* **ServiceInstanceName:** Detta är det fullständigt kvalificerade namn för den distribuerade tjänst-instans som du försöker nå utan den ”fabric: /” schema. Till exempel för att nå den *fabric: / myapp/myservice/* -tjänsten som du skulle använda *myapp/myservice*.

    Namnet på service-instansen är skiftlägeskänsligt. Med hjälp av ett annat skiftläge för tjänstnamnet i URL: en instans innebär att begäranden att misslyckas med 404 (hittades inte).
* **Sökväg till suffix:** Det här är den faktiska URL-sökvägen, till exempel *myapi/värden/Lägg till/3*, för tjänsten som du vill ansluta till.
* **PartitionKey:** Detta är beräknade Partitionsnyckeln för den partition som du vill nå för en partitionerad tjänst. Observera att detta *inte* partitions-ID-GUID. Den här parametern krävs inte för tjänster som använder singleton-partitionsschema.
* **PartitionKind:** Det här är tjänstpartitionsschemat. Detta kan vara 'Int64Range' eller 'Med namnet ”. Den här parametern krävs inte för tjänster som använder singleton-partitionsschema.
* **ListenerName** slutpunkter från tjänsten är i formatet {”slutpunkter”: {”Listener1”: ”slutpunkt 1”, ”Listener2”: ”Endpoint2”...}}. När tjänsten exponerar flera slutpunkter, identifierar den här kolumnen den slutpunkt som klientbegäran ska vidarebefordras till. Detta kan utelämnas om tjänsten har endast en lyssnare.
* **TargetReplicaSelector** detta anger hur target repliken eller instans måste väljas.
  * När Måltjänsten är tillståndskänslig kan TargetReplicaSelector vara något av följande:  'PrimaryReplica', 'RandomSecondaryReplica' eller 'RandomReplica'. När den här parametern inte anges är standardvärdet 'PrimaryReplica'.
  * När Måltjänsten är tillståndslösa, hämtar en slumpmässig instans av tjänstpartition att vidarebefordra begäran till omvänd proxy.
* **Timeout:**  Detta anger timeout för HTTP-begäran som skapats av omvänd proxy till tjänsten räkning klientbegäran. Standardvärdet är 60 sekunder. Det här är en valfri parameter.

### <a name="example-usage"></a>Exempel på användning
Till exempel ska vi ta den *fabric: / MyApp/MyService* tjänst som öppnar en HTTP-lyssnare på följande URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Följande är resurserna för tjänsten:

* `/index.html`
* `/api/users/<userId>`

Om tjänsten använder singleton partitioneringsschema, den *PartitionKey* och *PartitionKind* frågesträngparametrar är inte obligatoriska och tjänsten kan nås med hjälp av gateway som:

* Externt: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internt: `http://localhost:19081/MyApp/MyService`

Om tjänsten använder partitioneringsschemat Uniform Int64 den *PartitionKey* och *PartitionKind* frågesträngparametrar måste användas för att nå en partition av tjänsten:

* Externt: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internt: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

För att nå de resurser som tjänsten exponerar, placerar du helt enkelt resurssökvägen efter tjänstnamnet i URL: en:

* Externt: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internt: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Gatewayen kommer sedan att vidarebefordra dessa begäranden till tjänstens URL:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Särskild hantering för delning av port tjänster
Service Fabric omvänd proxy försöker lösa en tjänstadress igen och försök begäran när en tjänst inte kan nås. I allmänhet när en tjänst inte kan nås, har tjänstinstansen eller repliken flyttats till en annan nod som en del av sin normala livscykel. När detta sker få omvänd proxy ett anslutningsfel i nätverket som anger att en slutpunkt är inte längre är öppen på den ursprungligen matchade adressen.

Dock repliker eller instanser av tjänsten kan dela en värdprocess och kan också dela en port när värd för en http.sys-baserade webbserver, inklusive:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

I det här fallet är det troligt att webbservern är tillgängligt i värdprocessen och svara på begäranden, men löst tjänstinstansen eller repliken inte längre tillgängligt på värddatorn. I det här fallet får gatewayen ett HTTP 404-svar från webbservern. Ett HTTP 404-svar kan därför ha två olika innebörd:

- Fall #1: Tjänstadressen är korrekt, men den resurs som användaren begärde finns inte.
- Fall #2: Serviceadressen är felaktig och den resurs som användaren begärde kan finnas på en annan nod.

Det första fallet är en vanlig HTTP 404 som anses vara ett användarfel. I det andra fallet har dock användaren begärde en resurs som finns. Det gick inte att hitta den eftersom själva tjänsten har flyttats omvänd proxy. Omvänd proxy måste matcha adressen igen och försök begäran.

Omvänd proxy innebär behöver ett sätt att skilja mellan dessa två metoder. Om du vill göra den skillnaden, krävs en ledtråd från servern.

* Som standard den omvända proxyn förutsätter fall #2 och försöker lösa och skicka begäran igen.
* Om du vill ange fall #1 till omvänd proxy ska tjänsten returnera följande HTTP-svarshuvud:

  `X-ServiceFabric : ResourceNotFound`

Den här HTTP-Svarsrubrik visar en normal HTTP 404-situation där den begärda resursen finns inte och den omvända proxyn inte kommer att matcha tjänstadressen igen.

## <a name="special-handling-for-services-running-in-containers"></a>Särskild hantering för tjänster som körs i behållare

För tjänster som körs i behållare, kan du använda miljövariabeln, `Fabric_NodeIPOrFQDN` att konstruera den [omvänd proxy-URL: en](#uri-format-for-addressing-services-by-using-the-reverse-proxy) som i följande kod:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
För det lokala klustret `Fabric_NodeIPOrFQDN` är inställd på ”localhost” som standard. Starta det lokala klustret med den `-UseMachineName` parametern för att kontrollera behållare kan nå omvänd proxy som körs på noden. Mer information finns i [konfigurerar utvecklarmiljön för att felsöka behållare](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Service Fabric-tjänster som körs i Docker Compose-behållare kräver en särskild docker-compose.yml *portarna avsnittet* http: eller https: konfiguration. Mer information finns i [Docker Compose distributionsstöd i Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Nästa steg
* [Installera och konfigurera omvänd proxy i ett kluster](service-fabric-reverseproxy-setup.md).
* [Konfigurera vidarebefordran till säker HTTP-tjänsten med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnostisera omvänd proxy-händelser](service-fabric-reverse-proxy-diagnostics.md)
* Se ett exempel på HTTP-kommunikation mellan tjänster i en [exempelprojektet på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [RPC-anrop med Reliable Services-fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikation med Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
