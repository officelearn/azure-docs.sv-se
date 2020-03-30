---
title: Ansluta och kommunicera med tjänster i Azure Service Fabric
description: Lär dig hur du löser, ansluter och kommunicerar med tjänster i Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458238"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Ansluta och kommunicera med tjänster i Service Fabric
I Service Fabric körs en tjänst någonstans i ett Service Fabric-kluster, vanligtvis distribuerad över flera virtuella datorer. Den kan flyttas från en plats till en annan, antingen av tjänstägaren eller automatiskt av Service Fabric. Tjänsterna är inte statiskt kopplade till en viss dator eller adress.

Ett Service Fabric-program består i allmänhet av många olika tjänster, där varje tjänst utför en specialiserad uppgift. Dessa tjänster kan kommunicera med varandra för att bilda en fullständig funktion, till exempel att göra olika delar av ett webbprogram. Det finns också klientprogram som ansluter till och kommunicerar med tjänster. I det här dokumentet beskrivs hur du ställer in kommunikation med och mellan dina tjänster i Service Fabric.

## <a name="bring-your-own-protocol"></a>Ta med ditt eget protokoll
Service Fabric hjälper till att hantera livscykeln för dina tjänster, men det fattar inga beslut om vad dina tjänster gör. Detta inkluderar kommunikation. När tjänsten öppnas av Service Fabric är det tjänstens möjlighet att skapa en slutpunkt för inkommande begäranden, oavsett vilket protokoll eller vilken kommunikationsstack du vill. Din tjänst lyssnar på en vanlig **IP:port-adress** med ett adresseringsschema, till exempel en URI. Flera tjänstinstanser eller repliker kan dela en värdprocess, i vilket fall de antingen måste använda olika portar eller använda en portdelningsmekanism, till exempel http.sys-kärndrivrutinen i Windows. I båda fallen måste varje tjänstinstans eller replik i en värdprocess vara unikt adresserbar.

![tjänstens slutpunkter][1]

## <a name="service-discovery-and-resolution"></a>Identifiering och lösning av tjänsten
I ett distribuerat system kan tjänster flyttas från en dator till en annan med tiden. Detta kan inträffa av olika skäl, inklusive resursbalansering, uppgraderingar, redundans eller utskalning. Det innebär att tjänstslutpunktsadresser ändras när tjänsten flyttas till noder med olika IP-adresser och kan öppnas på olika portar om tjänsten använder en dynamiskt vald port.

![Distribution av tjänster][7]

Service Fabric tillhandahåller en identifierings- och lösningstjänst som kallas namngivningstjänsten. Namngivningstjänsten underhåller en tabell som mappar namngivna tjänstinstanser till slutpunktsadresserna som de lyssnar på. Alla namngivna tjänstinstanser i Service Fabric har unika `"fabric:/MyApplication/MyService"`namn som representeras som URI:er, till exempel . Namnet på tjänsten ändras inte under tjänstens livstid, det är bara slutpunktsadresserna som kan ändras när tjänster flyttas. Detta är analogt med webbplatser som har konstanta webbadresser men där IP-adressen kan ändras. Och i likhet med DNS på webben, som löser webbadresser till IP-adresser, har Service Fabric en registrator som mappar tjänstnamn till sin slutpunktsadress.

![tjänstens slutpunkter][2]

Att lösa och ansluta till tjänster innebär följande steg som körs i en loop:

* **Lös**: Hämta slutpunkten som en tjänst har publicerat från namngivningstjänsten.
* **Anslut:** Anslut till tjänsten över vilket protokoll den använder på den slutpunkten.
* **Försök igen**: Ett anslutningsförsök kan misslyckas av många orsaker, till exempel om tjänsten har flyttats sedan slutpunktsadressen senast löstes. I så fall måste föregående lösnings- och anslutningssteg göras om och den här cykeln upprepas tills anslutningen lyckas.

## <a name="connecting-to-other-services"></a>Ansluta till andra tjänster
Tjänster som ansluter till varandra i ett kluster kan i allmänhet direkt komma åt slutpunkterna för andra tjänster eftersom noderna i ett kluster finns i samma lokala nätverk. För att göra det enklare att ansluta mellan tjänster tillhandahåller Service Fabric ytterligare tjänster som använder namngivningstjänsten. En DNS-tjänst och en omvänd proxytjänst.


### <a name="dns-service"></a>DNS-tjänst
Eftersom många tjänster, särskilt containeriserade tjänster, kan ha ett befintligt URL-namn, är det mycket bekvämt att kunna lösa dessa med hjälp av standard-DNS-protokollet (i stället för Naming Service-protokollet) särskilt i scenarierna "lyft och skift". Detta är precis vad DNS-tjänsten gör. Det gör att du kan mappa DNS-namn till ett tjänstnamn och därmed lösa slutpunkts-IP-adresser. 

Som visas i följande diagram mappar DNS-tjänsten, som körs i service fabric-klustret, DNS-namn till tjänstnamn som sedan matchas av namngivningstjänsten för att returnera slutpunktsadresserna som ska anslutas till. DNS-namnet för tjänsten anges när den skapas. 

![tjänstens slutpunkter][9]

Mer information om hur du använder DNS-tjänsten finns [i DNS-tjänsten i](service-fabric-dnsservice.md) azure service fabric-artikeln.

### <a name="reverse-proxy-service"></a>Omvänd proxytjänst
Den omvända proxyadresser tjänster i klustret som exponerar HTTP-slutpunkter inklusive HTTPS. Den omvända proxyn förenklar i hög grad att anropa andra tjänster och deras metoder genom att ha ett specifikt URI-format och hanterar de steg för att lösa, ansluta, försöka igen för att en tjänst ska kunna kommunicera med en annan med hjälp av namngivningstjänsten. Med andra ord döljer den namngivningstjänsten för dig när du ringer andra tjänster genom att göra detta så enkelt som att anropa en webbadress.

![tjänstens slutpunkter][10]

Mer information om hur du använder den omvända proxytjänsten finns [i omvänd proxy i](service-fabric-reverseproxy.md) azure service fabric-artikeln.

## <a name="connections-from-external-clients"></a>Anslutningar från externa klienter
Tjänster som ansluter till varandra i ett kluster kan i allmänhet direkt komma åt slutpunkterna för andra tjänster eftersom noderna i ett kluster finns i samma lokala nätverk. I vissa miljöer kan dock ett kluster ligga bakom en belastningsutjämnare som dirigerar extern inkommande trafik genom en begränsad uppsättning portar. I dessa fall kan tjänster fortfarande kommunicera med varandra och lösa adresser med hjälp av namngivningstjänsten, men extra åtgärder måste vidtas för att externa klienter ska kunna ansluta till tjänster.

## <a name="service-fabric-in-azure"></a>Tjänstinfrastruktur i Azure
Ett Service Fabric-kluster i Azure placeras bakom en Azure Load Balancer. All extern trafik till klustret måste passera genom belastningsutjämnaren. Belastningsutjämnaren vidarebefordrar automatiskt trafik inkommande på en viss port till en slumpmässig *nod* som har samma port öppen. Azure Load Balancer känner bara till portar som är öppna på *noderna*, den vet inte om portar som är öppna för enskilda *tjänster*.

![Azure Belastningsutjämnare och service fabric-topologi][3]

För att till exempel kunna acceptera extern trafik på port **80**måste följande saker konfigureras:

1. Skriv en tjänst som lyssnar på port 80. Konfigurera port 80 i tjänstens ServiceManifest.xml och öppna en lyssnare i tjänsten, till exempel en självvärd webbserver.

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
2. Skapa ett Service Fabric-kluster i Azure och ange port **80** som en anpassad slutpunktsport för nodtypen som ska vara värd för tjänsten. Om du har mer än en nodtyp kan du ställa in en *placeringsbegränsning* för tjänsten så att den bara körs på nodtypen som har den anpassade slutpunktsporten öppen.

    ![Öppna en port på en nodtyp][4]
3. När klustret har skapats konfigurerar du Azure Load Balancer i klustrets resursgrupp för att vidarebefordra trafik på port 80. När du skapar ett kluster via Azure-portalen ställs detta in automatiskt för varje anpassad slutpunktsport som har konfigurerats.

    ![Vidarebefordra trafik i Azure Load Balancer][5]
4. Azure Load Balancer använder en avsökning för att avgöra om trafik ska skickas till en viss nod eller inte. Avsökningen kontrollerar regelbundet en slutpunkt på varje nod för att avgöra om noden svarar eller inte. Om avsökningen inte får ett svar efter ett konfigurerat antal gånger, slutar belastningsutjämnaren att skicka trafik till den noden. När du skapar ett kluster via Azure-portalen ställs en avsökning automatiskt in för varje anpassad slutpunktsport som har konfigurerats.

    ![Vidarebefordra trafik i Azure Load Balancer][8]

Det är viktigt att komma ihåg att Azure Load Balancer och avsökningen bara känner till *noderna*, inte de *tjänster* som körs på noderna. Azure Load Balancer skickar alltid trafik till noder som svarar på avsökningen, så försiktighet måste vidtas för att säkerställa att tjänster är tillgängliga på noderna som kan svara på avsökningen.

## <a name="reliable-services-built-in-communication-api-options"></a>Tillförlitliga tjänster: Inbyggda alternativ för kommunikations-API
Reliable Services-ramverket levereras med flera färdiga kommunikationsalternativ. Beslutet om vilken som fungerar bäst för dig beror på valet av programmeringsmodell, kommunikationsramverket och programmeringsspråket som dina tjänster är skrivna i.

* **Inget specifikt protokoll:**  Om du inte har ett visst val av kommunikationsramverk, men du vill få igång något snabbt, är det perfekta alternativet för dig [att anpassa tjänsten,](service-fabric-reliable-services-communication-remoting.md)vilket möjliggör starkt skrivna fjärrprocedursamtal för tillförlitliga tjänster och tillförlitliga aktörer. Detta är det enklaste och snabbaste sättet att komma igång med servicekommunikation. Serviceåterbehållning hanterar lösning av serviceadresser, anslutning, återförsök och felhantering. Detta är tillgängligt för både C# och Java-program.
* **HTTP**: För språkoberoende kommunikation ger HTTP ett branschstandardval med verktyg och HTTP-servrar som är tillgängliga på många olika språk, som alla stöds av Service Fabric. Tjänster kan använda valfri HTTP-stack, inklusive [ASP.NET webb-API](service-fabric-reliable-services-communication-webapi.md) för C#-program. Klienter som är skrivna `ICommunicationClient` `ServicePartitionClient` i C# kan utnyttja `CommunicationClient` `FabricServicePartitionClient` och klasser, medan för Java, använda och klasser, [för servicematchning, HTTP-anslutningar och retry loopar](service-fabric-reliable-services-communication.md).
* **WCF**: Om du har befintlig kod som använder WCF `WcfCommunicationListener` som ditt kommunikationsramverk kan du använda för serversidan och `WcfCommunicationClient` och `ServicePartitionClient` klasserna för klienten. Detta är dock endast tillgängligt för C#-program i Windows-baserade kluster. Mer information finns i den här artikeln om [WCF-baserad implementering av kommunikationsstacken](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Använda anpassade protokoll och andra kommunikationsramverk
Tjänster kan använda alla protokoll eller ramverk för kommunikation, oavsett om det är ett anpassat binärt protokoll över TCP-sockets eller strömmande händelser via [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric tillhandahåller kommunikations-API:er som du kan ansluta din kommunikationsstack till, medan allt arbete för att upptäcka och ansluta är abstraherat från dig. Mer information finns i den här artikeln om kommunikationsmodellen för [tillförlitliga tjänster.](service-fabric-reliable-services-communication.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om de begrepp och API:er som finns i [kommunikationsmodellen För tillförlitliga tjänster](service-fabric-reliable-services-communication.md)och kom sedan igång snabbt med [serviceåterbetering](service-fabric-reliable-services-communication-remoting.md) eller gå in på djupet för att lära dig hur du skriver en kommunikationsavlyssnare med hjälp av [webb-API med OWIN-självvärd](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
