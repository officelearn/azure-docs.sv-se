---
title: Omvänd proxy för Azure Service Fabric
description: Använd Service Fabric omvänd proxy för kommunikation till mikrotjänster från insidan och utsidan av klustret.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282229"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Omvänd proxy i Azure Service Fabric
Omvänd proxy inbyggd i Azure Service Fabric hjälper mikrotjänster som körs i ett Service Fabric-kluster att upptäcka och kommunicera med andra tjänster som har http-slutpunkter.

## <a name="microservices-communication-model"></a>Kommunikationsmodell för mikrotjänster
Mikrotjänster i Service Fabric körs på en delmängd av noder i klustret och kan migrera mellan noderna av olika skäl. Därför kan slutpunkterna för mikrotjänster ändras dynamiskt. För att upptäcka och kommunicera med andra tjänster i klustret måste mikrotjänsten gå igenom följande steg:

1. Lös tjänstplatsen via namngivningstjänsten.
2. Anslut till tjänsten.
3. Radbryt föregående steg i en loop som implementerar principer för tjänstmatchning och återförsök för att tillämpa vid anslutningsfel

Mer information finns i [Ansluta och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunicera med hjälp av omvänd proxy
Omvänd proxy är en tjänst som körs på varje nod och hanterar slutpunktsupplösning, automatiskt återförsök och andra anslutningsfel för klienttjänsternas räkning. Omvänd proxy kan konfigureras för att tillämpa olika principer när den hanterar begäranden från klienttjänster. Med hjälp av en omvänd proxy kan klienttjänsten använda alla HTTP-kommunikationsbibliotek på klientsidan och kräver ingen särskild lösning och återförsökslogik i tjänsten. 

Omvänd proxy visar en eller flera slutpunkter på lokal nod för klienttjänster som ska användas för att skicka begäranden till andra tjänster.

![Intern kommunikation][1]

> [!NOTE]
> **Plattformar som stöds**
>
> Omvänd proxy i Service Fabric stöder för närvarande följande plattformar
> * *Windows-kluster:* Windows 8 och senare eller Windows Server 2012 och senare
> * *Linux Cluster:* Reverse Proxy är för närvarande inte tillgänglig för Linux-kluster
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Nå mikrotjänster utanför klustret
Standardmodellen för extern kommunikation för mikrotjänster är en opt-in-modell där varje tjänst inte kan nås direkt från externa klienter. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), som är en nätverksgräns mellan mikrotjänster och externa klienter, utför översättning av nätverksadresser och vidarebefordrar externa begäranden till interna IP:port-slutpunkter. Om du vill göra en mikrotjänstslutpunkt direkt tillgänglig för externa klienter måste du först konfigurera belastningsutjämnaren för att vidarebefordra trafik till varje port som tjänsten använder i klustret. Dessutom, de flesta mikrotjänster, särskilt tillståndskänsliga mikrotjänster, inte lever på alla noder i klustret. Mikrotjänsterna kan flyttas mellan noder vid redundans. I sådana fall kan belastningsutjämningsfaktorn inte effektivt bestämma platsen för målnoden för replikerna som den ska vidarebefordra trafik till.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Nå mikrotjänster via omvänd proxy från utanför klustret
I stället för att konfigurera porten för en enskild tjänst i Belastningsutjämnare kan du konfigurera bara porten för den omvända proxyn i Belastningsutjämnaren. Med den här konfigurationen kan klienter utanför klustret nå tjänster i klustret med hjälp av omvänd proxy utan ytterligare konfiguration.

![Extern kommunikation][0]

> [!WARNING]
> När du konfigurerar den omvända proxyporten i Belastningsutjämnaren är alla mikrotjänster i klustret som exponerar en HTTP-slutpunkt adresserbara utanför klustret. Det innebär att mikrotjänster som är avsedda att vara interna kan upptäckas av en bestämd obehörig användare. Detta kan medföra allvarliga sårbarheter som kan utnyttjas. till exempel:
>
> * En obehörig användare kan starta en denial of service-attack genom att upprepade gånger anropa en intern tjänst som inte har en tillräckligt härdad attackyta.
> * En obehörig användare kan leverera felformaterade paket till en intern tjänst som resulterar i oavsiktligt beteende.
> * En tjänst som är avsedd att vara intern kan returnera privat eller känslig information som inte är avsedd att exponeras för tjänster utanför klustret, vilket innebär att den här känsliga informationen exponeras för en obehörig användare. 
>
> Se till att du till fullo förstår och mildrar de potentiella säkerhetskonsekvenserna för klustret och apparna som körs på det, innan du gör den omvända proxyporten offentlig. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-format för adressering av tjänster med hjälp av omvänd proxy
Den omvända proxyn använder ett specifikt uri-format (Uniform Resource Identifier) för att identifiera den tjänstpartition som den inkommande begäran ska vidarebefordras till:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** Den omvända proxyn kan konfigureras för att acceptera HTTP- eller HTTPS-trafik. För HTTPS-vidarebefordran finns [i Anslut till en säker tjänst med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md) när du har omvänd proxyinställning för att lyssna på HTTPS.
* **Klustrets fullständigt kvalificerade domännamn (FQDN) | intern IP:** För externa klienter kan du konfigurera omvänd proxy så att den kan nås via klusterdomänen, till exempel mycluster.eastus.cloudapp.azure.com. Som standard körs den omvända proxyn på varje nod. För intern trafik kan den omvända proxyn nås på localhost eller på någon intern nod-IP, till exempel 10.0.0.1.
* **Hamn:** Detta är porten, till exempel 19081, som har angetts för omvänd proxy.
* **ServiceInstanceName:** Det här är det fullständigt kvalificerade namnet på den distribuerade tjänstinstans som du försöker nå utan "tyget:/" System. Till exempel, för att nå *tyget:/myapp/myservice/* service, skulle du använda *myapp/myservice*.

    Tjänstinstansnamnet är skiftlägeskänsligt. Om du använder ett annat hölje för tjänstinstansnamnet i URL:en misslyckas begäranden med 404 (Hittades inte).
* **Suffixsökväg:** Det här är den faktiska URL-sökvägen, till exempel *myapi/values/add/3*, för den tjänst som du vill ansluta till.
* **PartitionKey:** För en partitionerad tjänst är detta den beräknade partitionsnyckeln för den partition som du vill nå. Observera att detta *inte* är partitionenS GUID. Den här parametern krävs inte för tjänster som använder singleton-partitionsschemat.
* **PartitionKind:** Det här är tjänstpartitionsschemat. Detta kan vara "Int64Range" eller "Named". Den här parametern krävs inte för tjänster som använder singleton-partitionsschemat.
* **Lyssnarnamn** Slutpunkterna från tjänsten är av formuläret {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. När tjänsten exponerar flera slutpunkter identifierar detta slutpunkten som klientbegäran ska vidarebefordras till. Detta kan utelämnas om tjänsten bara har en lyssnare.
* **MålReplicaSelector** Detta anger hur målrepliken eller instansen ska väljas.
  * När måltjänsten är tillståndskänslig kan TargetReplicaSelector vara något av följande: "PrimaryReplica", "RandomSecondaryReplica" eller "RandomReplica". När den här parametern inte har angetts är standardvärdet "PrimaryReplica".
  * När måltjänsten är tillståndslös väljer omvänd proxy en slumpmässig instans av tjänstpartitionen att vidarebefordra begäran till.
* **Timeout:**  Detta anger timeout för HTTP-begäran som skapats av den omvända proxyn till tjänsten för klientbegäran. Standardvärdet är 60 sekunder. Detta är en valfri parameter.

### <a name="example-usage"></a>Exempel på användning
Låt oss till exempel ta med sig tjänsten *fabric:/MyApp/MyService* som öppnar en HTTP-lyssnare på följande WEBBADRESS:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Följande är resurserna för tjänsten:

* `/index.html`
* `/api/users/<userId>`

Om tjänsten använder singleton-partitioneringsschemat krävs inte frågesträngparametrarna *PartitionKey* och *PartitionKind* och tjänsten kan nås med hjälp av gatewayen som:

* Externt:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internt:`http://localhost:19081/MyApp/MyService`

Om tjänsten använder det enhetliga int64-partitioneringsschemat måste *partitionkey-* och *PartitionKind-frågesträngparametrarna* användas för att nå en partition av tjänsten:

* Externt:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internt:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Om du vill nå de resurser som tjänsten exponerar placerar du bara resurssökvägen efter tjänstnamnet i URL:en:

* Externt:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internt:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Gatewayen vidarebefordrar sedan dessa begäranden till tjänstens URL:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Särskild hantering av hamndelningstjänster
Den omvända proxyn för Service Fabric försöker lösa en tjänstadress igen och försöka igen begäran när en tjänst inte kan nås. När en tjänst inte kan nås har tjänstinstansen eller repliken i allmänhet flyttats till en annan nod som en del av sin normala livscykel. När detta inträffar kan den omvända proxyn få ett nätverksanslutningsfel som anger att en slutpunkt inte längre är öppen på den ursprungligen lösta adressen.

Repliker eller tjänstinstanser kan dock dela en värdprocess och kan även dela en port när den finns på en http.sys-baserad webbserver, inklusive:

* [System.Net.httpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

I det här fallet är det troligt att webbservern är tillgänglig i värdprocessen och svarar på begäranden, men den matchade tjänstinstansen eller repliken är inte längre tillgänglig på värden. I det här fallet får gatewayen ett HTTP 404-svar från webbservern. Ett HTTP 404-svar kan alltså ha två olika betydelser:

- Ärende #1: Serviceadressen är korrekt, men den resurs som användaren begärde finns inte.
- Ärende #2: Serviceadressen är felaktig och den resurs som användaren begärde kan finnas på en annan nod.

Det första fallet är en normal HTTP 404, som anses vara ett användarfel. I det andra fallet har användaren dock begärt en resurs som finns. Den omvända proxyn kunde inte hitta den eftersom själva tjänsten har flyttats. Den omvända proxyn måste lösa adressen igen och försöka igen begäran.

Den omvända proxyn behöver därför ett sätt att skilja mellan dessa två fall. För att göra denna distinktion krävs en ledtråd från servern.

* Som standard förutsätter den omvända proxyn ärende #2 och försöker lösa och utfärda begäran igen.
* Om du vill ange fall #1 till den omvända proxyn bör tjänsten returnera följande HTTP-svarshuvud:

  `X-ServiceFabric : ResourceNotFound`

Det här HTTP-svarshuvudet anger en normal HTTP 404-situation där den begärda resursen inte finns, och den omvända proxyn försöker inte lösa tjänstadressen igen.

## <a name="special-handling-for-services-running-in-containers"></a>Särskild hantering för tjänster som körs i containrar

För tjänster som körs inuti behållare kan `Fabric_NodeIPOrFQDN` du använda miljövariabeln för att konstruera den [omvända proxy-URL:en](#uri-format-for-addressing-services-by-using-the-reverse-proxy) enligt följande kod:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
För det lokala `Fabric_NodeIPOrFQDN` klustret är inställd på "localhost" som standard. Starta det lokala `-UseMachineName` klustret med parametern för att se till att behållarna kan nå omvänd proxy som körs på noden. Mer information finns i [Konfigurera utvecklarmiljön för felsökning av behållare](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Service Fabric-tjänster som körs i Docker Compose-behållare kräver en särskild docker-compose.yml *Ports-avsnitt* http: eller https: konfiguration. Mer information finns i [Docker Compose-distributionsstöd i Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera omvänd proxy på ett kluster](service-fabric-reverseproxy-setup.md).
* [Konfigurera vidarebefordran för att skydda HTTP-tjänsten med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnostisera omvända proxy-händelser](service-fabric-reverse-proxy-diagnostics.md)
* Se ett exempel på HTTP-kommunikation mellan tjänster i ett [exempelprojekt på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Fjärrproceduranrop med reliable services-ommotning](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i reliable services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikation med hjälp av Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
