---
title: "Ansluta och kommunicera med tjänster i Azure Service Fabric | Microsoft Docs"
description: "Lär dig hur du löser ansluta och kommunicera med tjänster i Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/9/2017
ms.author: vturecek
ms.openlocfilehash: 3e61ad19df34c6a57da43e26bd2ab9d7ecdbf98e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Ansluta och kommunicera med tjänster i Service Fabric
I Service Fabric körs en tjänst någonstans i ett Service Fabric-kluster, vanligtvis fördelade på flera virtuella datorer. Den kan flyttas från en plats till en annan, antingen av tjänstens ägare eller automatiskt av Service Fabric. Tjänster är inte statiskt knutna till en viss dator eller en adress.

Ett Service Fabric-program består normalt av många olika tjänster, där varje tjänst utför en särskild aktivitet. Dessa tjänster kan kommunicera med varandra för att bilda en fullständig funktion, till exempel återgivning olika delar av ett webbprogram. Det finns också klientprogram som kan ansluta till och kommunicera med tjänster. Det här dokumentet beskrivs hur du ställer in kommunikation med och mellan dina tjänster i Service Fabric.

Den här Microsoft Virtual Academy videon beskrivs också kommunikation:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=iYFCk76yC_6706218965">  
<img src="./media/service-fabric-connect-and-communicate-with-services/CommunicationVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="bring-your-own-protocol"></a>Ta med egna protokoll
Service Fabric hjälper dig att hantera livscykeln för dina tjänster, men det gör inte beslut om dina tjänster gör. Detta omfattar kommunikation. När tjänsten har öppnats av Service Fabric, som är din tjänst möjlighet att ställa in en slutpunkt för inkommande begäranden som använder oavsett protokoll eller kommunikation stack som du vill. Tjänsten kommer att lyssna på en normal **IP:port** med adresseringsschema, till exempel en URI-adress. Flera instanser av tjänsten eller repliker kan dela en värdprocess, då de antingen måste använda olika portar eller använda en delning av port mekanism, till exempel http.sys kernel-drivrutinen i Windows. I båda fallen måste varje instans av tjänsten eller replik i en värdprocess måste vara unikt adresserbara.

![slutpunkter][1]

## <a name="service-discovery-and-resolution"></a>Identifiering och lösning
I ett distribuerat system, kan tjänster flytta från en dator till en annan över tid. Detta kan inträffa av olika skäl, inklusive resurser, uppgraderingar, redundans och skalbara. Det innebär att tjänsten slutpunkts-adresser ändras när tjänsten flyttas till noder med olika IP-adresser och öppnas på olika portar om tjänsten använder en dynamiskt vald port.

![Distribution av tjänster][7]

Service Fabric är en tjänst för identifiering och lösning som kallas Naming Service. Tjänsten Naming upprätthåller en tabell som mappar namngivna instanser av tjänsten till slutpunkts-adresser som de lyssna på. Alla instanser för tjänsten i Service Fabric har unika namn som visas i form av URI: er, till exempel `"fabric:/MyApplication/MyService"`. Namnet på tjänsten ändras inte under livslängden för tjänsten, är det bara slutpunkts-adresser som kan ändras när tjänsterna flytta. Detta är detsamma som webbplatser som har konstant URL: er men där IP-adress kan ändras. Och liknar DNS på webben, som matchar IP-adresser för webbplats-URL: er, Service Fabric har ett register som mappar tjänstnamn till deras slutpunktsadress.

![slutpunkter][2]

Lösa och ansluta till tjänster omfattar följande steg i en slinga:

* **Lös**: hämta den slutpunkt som har publicerat en tjänst från Naming Service.
* **Ansluta**: ansluta till tjänsten via oavsett protokoll använder på denna slutpunkt.
* **Försök**: ett anslutningsförsök misslyckas av olika orsaker, för om tjänsten har flyttats sedan den senast slutpunktsadressen löstes. I så fall den föregående lösa och ansluta steg måste göras och den här cykeln upprepas tills anslutningen lyckas.

## <a name="connecting-to-other-services"></a>Ansluter till andra tjänster
Tjänster vanligtvis ansluta till varandra i ett kluster kan komma åt direkt slutpunkter av andra tjänster eftersom noder i ett kluster finns i samma lokala nätverk. Om du vill göra är enklare att ansluta olika tjänster, Service Fabric ger ytterligare tjänster som använder Naming Service. En DNS-tjänst och en omvänd proxy-tjänst.


### <a name="dns-service"></a>DNS-tjänst
Eftersom många tjänster, särskilt av tjänster kan ha ett befintligt URL-namn, att kunna lösa dessa med hjälp av standard-DNS-protokollet (i stället för protokollet Naming Service) är mycket praktiskt, särskilt i programmet ”lyfta och flytta” scenarier. Detta är exakt det DNS-tjänsten. På så sätt kan du mappa DNS-namn till ett namn och därför matcha IP-adresser för slutpunkt. 

I följande diagram visas matchar DNS-namn till tjänstnamn som sedan kan matchas med namngivningstjänst att returnera slutpunkts-adresser för att ansluta till DNS-tjänsten som körs i Service Fabric-klustret. DNS-namn för tjänsten tillhandahålls vid tidpunkten för skapandet. 

![slutpunkter][9]

För mer information om hur du använder DNS-tjänsten finns [DNS-tjänsten i Azure Service Fabric](service-fabric-dnsservice.md) artikel.

### <a name="reverse-proxy-service"></a>Tjänsten omvänd proxy
Omvänd proxy adresser tjänster i klustret som exponerar http-slutpunkter, inklusive HTTPS. Omvänd proxy avsevärt förenklar anropa andra tjänster och deras metoder genom att ha ett specifikt URI-format och hanterar lösas, ansluta, försök steg som krävs för en tjänst att kommunicera med varandra med namngivning av tjänsten. Med andra ord döljer namngivningstjänst från dig vid anrop av andra tjänster genom att göra detta så enkelt som anropar en URL.

![slutpunkter][10]

Mer information om hur du använder omvänd proxy-tjänst finns [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md) artikel.

## <a name="connections-from-external-clients"></a>Anslutningar från externa klienter
Tjänster vanligtvis ansluta till varandra i ett kluster kan komma åt direkt slutpunkter av andra tjänster eftersom noder i ett kluster finns i samma lokala nätverk. I vissa miljöer med kan ett kluster dock finnas bakom en belastningsutjämnare som skickar externa ingång trafik via en begränsad uppsättning portar. I dessa fall tjänster kan kommunicera med varandra och matcha adresser med att använda fortfarande, men måste du vidta ytterligare åtgärder för att tillåta externa klienter att ansluta till tjänster.

## <a name="service-fabric-in-azure"></a>Service Fabric i Azure
Ett Service Fabric-kluster i Azure är placerad bakom en belastningsutjämnare i Azure. Alla externa trafik till klustret måste gå via belastningsutjämnaren. Belastningsutjämnaren kommer automatiskt att vidarebefordra trafik inkommande på en viss port till ett slumpmässigt *nod* som har samma port öppna. Azure belastningsutjämnare bara medveten om portar som är öppna på de *noder*, öppnar du portar genom att individuella inte känner *services*.

![Azure belastningsutjämnare och Service Fabric-topologi][3]

Till exempel för att kunna acceptera extern trafik på port **80**, konfigureras följande saker:

1. Skriv en tjänst som lyssnar på port 80. Konfigurera port 80 i tjänstens ServiceManifest.xml och öppna en lyssnare i tjänsten, till exempel en egen värdbaserade webbserver.

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
2. Skapa ett Service Fabric-kluster i Azure och ange port **80** som en anpassad endpoint-port för nodtypen som är värd för tjänsten. Om du har mer än en nodtyp kan du skapa en *placering begränsningen* på tjänsten så att den kan bara köras på nodtypen som har anpassade endpoint porten öppnas.

    ![Öppna en port på en nodtyp][4]
3. När klustret har skapats kan du konfigurera Azure belastningsutjämnare i klusterresursgrupp att vidarebefordra trafik på port 80. När du skapar ett kluster via Azure portal, ställs detta in automatiskt för varje anpassad endpoint-port som har konfigurerats.

    ![Vidarebefordra trafik i Azure belastningsutjämnare][5]
4. Azure belastningsutjämnare använder en avsökning för att avgöra om att skicka trafik till en viss nod eller inte. Avsökningen söker regelbundet en slutpunkt på varje nod för att avgöra om noden svarar. Om det inte går att ta emot ett svar efter ett visst antal gånger avsökningen, stoppar belastningsutjämnaren skickar trafik till noden. När du skapar ett kluster via Azure portal, konfigureras automatiskt en avsökning för varje anpassad endpoint-port som har konfigurerats.

    ![Vidarebefordra trafik i Azure belastningsutjämnare][8]

Det är viktigt att komma ihåg att Azure belastningsutjämnare och avsökningen bara känna av *noder*, inte den *tjänster* körs på noderna. Azure belastningsutjämnare kommer alltid att skicka trafik till noder som svarar avsökningen, så måste vara försiktig så att tjänster är tillgängliga på de noder som är kunna svara avsökningen.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Inbyggda API kommunikationsalternativ
Reliable Services framework levereras med flera fördefinierade kommunikationsalternativ. Beslutet om vilka en fungerar bäst för dig beror på valet av programmeringsmiljö, kommunikation framework och programmeringsspråk som dina tjänster har skrivits i.

* **Inget specifikt protokoll:** om du inte har ett visst val av kommunikation framework, men du vill få något dig snabbt, så det bästa alternativet för du [remoting service](service-fabric-reliable-services-communication-remoting.md), vilket gör att strikt typkontroll RPC-anrop för Reliable Services och Reliable Actors. Detta är det enklaste och snabbaste sättet att komma igång med service-kommunikation. Tjänsten fjärrkommunikation hanterar lösning av postadresser, anslutning, försök igen och felhantering. Detta är tillgängligt för både C# och Java-program.
* **HTTP**: för språkoberoende kommunikation HTTP innehåller en branschstandardiserad val med verktyg och HTTP-servrar som är tillgängliga på många olika språk, alla stöds inte av Service Fabric. Tjänster kan använda alla tillgängliga, till exempel HTTP stack [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) för C#-program. Klienter som skrivits i C# kan utnyttja den `ICommunicationClient` och `ServicePartitionClient` klasser, medan Java, Använd den `CommunicationClient` och `FabricServicePartitionClient` klasser, [för tjänsten upplösning, HTTP-anslutningar och försök igen slingor](service-fabric-reliable-services-communication.md).
* **WCF**: Om du har befintliga kod som använder WCF som din kommunikation framework, så du kan använda den `WcfCommunicationListener` för serversidan och `WcfCommunicationClient` och `ServicePartitionClient` klasser för klienten. Detta men är bara tillgängligt för C#-program på Windows-baserade kluster. Mer information finns i den här artikeln [WCF-baserad implementering av kommunikation stacken](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Med hjälp av anpassade protokoll och andra ramverk för kommunikation
Tjänster kan använda alla protokoll eller ramverk för kommunikation, om det är ett anpassat protokoll för binära via TCP-sockets eller händelser via strömning via [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric ger kommunikation API: er som du ansluter din kommunikation stacken, medan allt arbete att upptäcka och ansluta representeras från dig. Se den här artikeln om den [tillförlitlig kommunikation modell](service-fabric-reliable-services-communication.md) för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om begrepp och API: er som är tillgängliga i den [Reliable Services kommunikation modellen](service-fabric-reliable-services-communication.md), sedan komma igång snabbt med [remoting service](service-fabric-reliable-services-communication-remoting.md) eller gå på djupet att lära dig hur du skriver ett meddelande lyssnaren med [Web API med OWIN som värd för automatisk](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
