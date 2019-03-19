---
title: Ansluta och kommunicera med tjänster i Azure Service Fabric | Microsoft Docs
description: Lär dig att lösa, ansluta och kommunicera med tjänster i Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: c4516e86e25bb31b113b495a239c9eae9df8c9f8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094777"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Ansluta och kommunicera med tjänster i Service Fabric
I Service Fabric körs en tjänst någonstans i ett Service Fabric-kluster, vanligtvis fördelade på flera virtuella datorer. Det kan flyttas från en plats till en annan, antingen av tjänstens ägare eller automatiskt av Service Fabric. Tjänster är inte statiskt knutna till en viss dator eller en adress.

Ett Service Fabric-program består normalt av många olika tjänster, där varje tjänst utför en särskild aktivitet. De här tjänsterna kan kommunicera med varandra för att bilda en fullständig funktion, till exempel rendering olika delar av ett webbprogram. Det finns också klientprogram som ansluter till och kommunicera med tjänster. Det här dokumentet beskriver hur du konfigurerar kommunikationen med och mellan dina tjänster i Service Fabric.

## <a name="bring-your-own-protocol"></a>Ta med ditt eget protokoll
Service Fabric hjälper till att hantera livscykeln för dina tjänster men det gör inte beslut om vad dina tjänster gör. Detta omfattar kommunikation. När din tjänst öppnas av Service Fabric, som är din tjänsts möjlighet att ställa in en slutpunkt för inkommande begäranden, med hjälp av de protokoll eller kommunikation stack som du vill. Tjänsten kommer att lyssna på en normal **IP:port** -adress med adresseringsschema, till exempel en URI. Flera instanser av tjänsten eller repliker kan dela en värdprocess, i vilket fall måste de antingen att använda olika portar eller använda en delning av port mekanism, till exempel http.sys kernel-drivrutinen i Windows. I båda fallen varje tjänstinstans eller replik i en värdprocess måste vara unikt adresserbara.

![Tjänsteslutpunkter][1]

## <a name="service-discovery-and-resolution"></a>Identifiering och lösning
I ett distribuerat system kan tjänster flytta från en dator till en annan över tid. Detta kan inträffa av olika anledningar, inklusive resurser, uppgraderingar, växling vid fel eller skala ut. Det innebär att tjänstens slutpunktsadresser ändras när tjänsten flyttas till noder med olika IP-adresser och öppnas på olika portar om tjänsten använder dynamiskt valda portar.

![Distribution av tjänster][7]

Service Fabric tillhandahåller en tjänst för identifiering och lösning som kallas namngivning av tjänsten. Namngivningstjänsten upprätthåller en tabell som mappar med namnet tjänstinstanser att slutpunktsadresser de lyssna på. Alla namngivna tjänstinstanser i Service Fabric har unika namn som visas som URI: er, till exempel `"fabric:/MyApplication/MyService"`. Namnet på tjänsten ändras inte under livslängden för tjänsten, är det bara slutpunktsadresser kan ändras när tjänsterna flytta. Det här är detsamma som webbplatser som har konstant URL: er men där IP-adressen kan ändras. Och liknar DNS på webben, som matchar webbplatsadresser IP-adresser, Service Fabric har en registrator som mappar tjänstnamn till sina slutpunktsadress.

![Tjänsteslutpunkter][2]

Lösa och ansluta till tjänster omfattar följande steg körs i en slinga:

* **Lösa**: Hämta den slutpunkt som en tjänst har publicerat från Naming-tjänsten.
* **Ansluta**: Ansluta till tjänsten via de protokoll som används på slutpunkten.
* **Försök**: Ett anslutningsförsök misslyckas av olika orsaker, för exempel om tjänsten har flyttats sedan senast slutpunktsadressen löstes. I så fall kan det föregående lösa och ansluta steg måste göras, och den här cykeln upprepas tills anslutningen lyckas.

## <a name="connecting-to-other-services"></a>Ansluta till andra tjänster
Tjänster som ansluter till varandra i ett kluster Allmänt kan direkt åtkomst till slutpunkterna för andra tjänster eftersom noderna i ett kluster i samma lokala nätverk. Om du vill göra är enklare att ansluta mellan tjänster, Service Fabric tillhandahåller ytterligare tjänster som använder Naming-tjänsten. En DNS-tjänst och en omvänd proxy-tjänst.


### <a name="dns-service"></a>DNS-tjänst
Eftersom många tjänster, särskilt container services, kan ha ett befintligt URL-namn, att kunna lösa dessa med hjälp av standard-DNS-protokollet (i stället för protokollet Namngivningstjänsten) är mycket praktiskt, särskilt i program ”lift and shift”-scenarier. Detta är exakt det DNS-tjänsten. Det kan du mappa DNS-namn till ett tjänstnamn och kan därför matcha IP-adresser för slutpunkt. 

I följande diagram visas mappar DNS-namn till tjänstnamn som sedan kan matchas med Namngivningstjänsten för att returnera slutpunktsadresser att ansluta till med hjälp av DNS-tjänsten som körs i Service Fabric-kluster. DNS-namn för tjänsten tillhandahålls vid tidpunkten för skapandet. 

![Tjänsteslutpunkter][9]

För mer information om hur du använder DNS-tjänsten finns i [DNS-tjänsten i Azure Service Fabric](service-fabric-dnsservice.md) artikeln.

### <a name="reverse-proxy-service"></a>Omvänd proxy-tjänst
Omvänd proxy löser tjänster i klustret som exponerar HTTP-slutpunkter, inklusive HTTPS. Omvänd proxy avsevärt förenklar anropa andra tjänster och deras metoder genom att använda ett specifikt URI-format och hanterar Lös, ansluta, försök steg som krävs för en tjänst att kommunicera med en annan med hjälp av tjänsten Naming. Med andra ord döljer namngivningstjänst från dig vid anrop av andra tjänster genom att göra det lika enkelt som att anropa en URL.

![Tjänsteslutpunkter][10]

Mer information om hur du använder omvänd proxy-tjänsten finns [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md) artikeln.

## <a name="connections-from-external-clients"></a>Anslutningar från externa klienter
Tjänster som ansluter till varandra i ett kluster Allmänt kan direkt åtkomst till slutpunkterna för andra tjänster eftersom noderna i ett kluster i samma lokala nätverk. I vissa miljöer kan kan ett kluster dock finnas bakom en belastningsutjämnare som dirigerar externa inkommande trafik via en begränsad uppsättning portar. I dessa fall kan tjänster fortfarande kan kommunicera med varandra och matcha adresser med hjälp av tjänsten namngivning, men ytterligare åtgärder måste vidtas för att tillåta externa klienter att ansluta till tjänster.

## <a name="service-fabric-in-azure"></a>Service Fabric i Azure
Service Fabric-kluster i Azure är placerad bakom en belastningsutjämnare för Azure. All extern trafik till klustret måste klara via belastningsutjämnaren. Belastningsutjämnaren automatiskt vidarebefordrar trafik inkommande på en viss port till ett slumpmässigt *noden* som har samma port öppna. Azure Load Balancer endast vet om portar öppna på de *noder*, den inte vet om portar öppna av enskilda *services*.

![Azure Load Balancer och Service Fabric-topologi][3]

Till exempel för att kunna acceptera extern trafik på port **80**, konfigureras följande saker:

1. Skriv en tjänst som lyssnar på port 80. Konfigurera port 80 i tjänstens ServiceManifest.xml och öppna en lyssnare i tjänsten, till exempel en lokal webbserver.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Skapa ett Service Fabric-kluster i Azure och ange port **80** som en anpassad slutpunkt-port för nodtypen som är värd för tjänsten. Om du har mer än en nodtyp, du kan ställa in en *placering begränsningen* på tjänsten för att kontrollera att den körs bara på vilken nod som har den anpassade slutpunktsport öppnas.

    ![Öppna en port på en nodtyp][4]
3. När klustret har skapats kan du konfigurera Azure Load Balancer i klustrets resursgrupp att vidarebefordra trafik på port 80. När du skapar ett kluster via Azure-portalen kan ställs detta in automatiskt för varje anpassad slutpunkt-port som har konfigurerats.

    ![Vidarebefordra trafik i Azure Load Balancer][5]
4. Azure Load Balancer använder en avsökning för att avgöra om att skicka trafik till en viss nod eller inte. Avsökningen söker regelbundet en slutpunkt på varje nod för att avgöra huruvida noden svarar. Om det inte går att få ett svar efter det konfigurerade antalet gånger avsökningen, slutar belastningsutjämnaren att skicka trafik till noden. När du skapar ett kluster via Azure-portalen måste konfigureras automatiskt en avsökning för varje anpassad slutpunkt-port som har konfigurerats.

    ![Vidarebefordra trafik i Azure Load Balancer][8]

Det är viktigt att komma ihåg att Azure Load Balancer och avsökningen känner bara till den *noder*, inte den *services* körs på noderna. Azure Load Balancer kommer alltid att skicka trafik till noder som svarar på avsökningen, så måste du vara noggrann så tjänster är tillgängliga på de noder som kommer svara på avsökningen.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Alternativ för inbyggd kommunikation API
Reliable Services-ramverk levereras med flera alternativ för kommunikation från färdiga. Beslutet om vilka något som fungerar bäst för dig är beroende av valet av programmeringsmiljö ramen för kommunikation och det programmeringsspråk som dina tjänster är skrivna i.

* **Ingen specifik protokoll:**  Om du inte har ett visst val av kommunikation framework, men du vill hämta något upp och snabbt, så är perfekt alternativ för dig [fjärrtjänst](service-fabric-reliable-services-communication-remoting.md), vilket gör att starkt typifierade RPC-anrop för Reliable Services och Reliable Actors. Det här är det enklaste och snabbaste sättet att komma igång med tjänst-kommunikation. Fjärrtjänst hanterar lösning av postadresser, anslutning, försök igen och felhantering. Det här är tillgänglig för både C# och Java-program.
* **HTTP**: För språkoberoende kommunikation ger HTTP ett branschstandard val med verktygen och HTTP-servrar som är tillgängliga i många olika språk, bibehållet stöd från Service Fabric. Tjänster kan använda valfri HTTP-stack som är tillgängliga, däribland [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) för C# program. Klienter som skrivits i C# kan dra nytta av den `ICommunicationClient` och `ServicePartitionClient` klasserna, medan för Java, använder den `CommunicationClient` och `FabricServicePartitionClient` klasser, [för tjänst-lösning, HTTP-anslutningar och försök igen slingor](service-fabric-reliable-services-communication.md).
* **WCF**: Om du har befintlig kod som använder WCF som din kommunikation framework så kan du använda den `WcfCommunicationListener` för serversidan och `WcfCommunicationClient` och `ServicePartitionClient` klasser för klienten. Detta men är bara tillgängligt för C# program på Windows-baserade kluster. Mer information finns i den här artikeln [WCF-baserad implementering av kommunikationsstack](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Med hjälp av anpassade protokoll och andra ramverk för kommunikation
Tjänster kan använda alla protokoll eller ramverk för kommunikation, oavsett om det är ett anpassat protokoll för binära via TCP-sockets eller direktuppspelning av händelser via [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric tillhandahåller kommunikation API: er som du ansluter din kommunikationsstack till, medan allt arbete att upptäcka och ansluta har abstraherats från dig. Se den här artikeln den [tillförlitlig tjänst modell](service-fabric-reliable-services-communication.md) för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om begrepp och API: erna i den [Reliable Services modell](service-fabric-reliable-services-communication.md), Kom sedan igång snabbt med [fjärrtjänst](service-fabric-reliable-services-communication-remoting.md) eller gå på djupet för att lära dig hur du skriver ett meddelande lyssnaren med [webb-API med OWIN lokal värd](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
