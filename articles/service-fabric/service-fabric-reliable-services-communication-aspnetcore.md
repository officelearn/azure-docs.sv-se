---
title: Kommunikation med ASP.NET Core-tjänst | Microsoft Docs
description: Lär dig hur du använder ASP.NET Core tillståndslösa och tillståndskänsliga Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/29/2018
ms.author: vturecek
ms.openlocfilehash: 384d0fa32b64706c9d9d9baa0e2e0bbb2ac3c522
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719604"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core i Service Fabric Reliable Services

ASP.NET Core är en ny öppen källkod och plattformsoberoende ramverk för att skapa moderna molnbaserade Internet-anslutna program, till exempel webbappar, IoT-appar och mobila serverdelar. 

Den här artikeln är utförliga instruktioner för värdtjänster för ASP.NET Core i Service Fabric Reliable Services med hjälp av den **Microsoft.ServiceFabric.AspNetCore.** * uppsättning NuGet-paket.

En inledande självstudiekurs om ASP.NET Core i Service Fabric och anvisningar om din installation av utvecklingsmiljö finns i [skapa ett .NET-program](service-fabric-tutorial-create-dotnet-app.md).

Resten av den här artikeln förutsätter att du redan är bekant med ASP.NET Core. Om inte, vi rekommenderar att du läser igenom den [grunderna i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core i Service Fabric-miljö

Både ASP.NET Core och Service Fabric-appar kan köras på .NET Core samt fullständiga .NET Framework. ASP.NET Core kan användas på två olika sätt i Service Fabric:
 - **Värdbaserad som en körbar gäst**. Det här används främst för att köra befintliga ASP.NET Core-program i Service Fabric utan kodändringar.
 - **Körs i en tillförlitlig tjänst**. Detta möjliggör bättre integrering med Service Fabric-körningen och tillåter tillståndskänslig ASP.NET Core-tjänster.

Resten av den här artikeln beskriver hur du använder ASP.NET Core i en tillförlitlig tjänst med hjälp av ASP.NET Core integrationskomponenterna som levereras med Service Fabric SDK. 

## <a name="service-fabric-service-hosting"></a>Service Fabric som är värd för tjänsten

I Service Fabric kan köra en eller flera instanser och/eller repliker av din tjänst i en *tjänsten värdprocess*, en körbar fil som kör koden för tjänsten. Du, som en tjänst författare äger värdprocessen service och Service Fabric aktiveras och övervakar det åt dig.

Traditionella ASP.NET (upp till MVC 5) är direkt kopplade till IIS via System.Web.dll. ASP.NET Core ger en åtskillnad mellan webbservern och ditt webbprogram. Detta tillåter webbprogram kan flyttas mellan olika servrar och kan också till att *egenvärdbaserade*, vilket innebär att du kan starta en webbserver i en egen process, till skillnad från en process som ägs av dedikerade web serverprogram, till exempel IIS. 

För att kombinera ett Service Fabric-tjänst och ASP.NET, antingen som en körbar Gäst eller i en tillförlitlig tjänst måste du kunna starta ASP.NET i din tjänst värdprocess. ASP.NET Core som egen värd kan du göra detta.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Som är värd för ASP.NET Core i en tillförlitlig tjänst
Vanligtvis egenhanterad ASP.NET Core-program skapar en WebHost i startpunkten för ett program, till exempel den `static void Main()` -metod i `Program.cs`. I det här fallet är livscykeln för WebHost bunden till livscykeln för processen.

![Värd för ASP.NET Core i en process][0]

Startpunkt för programmet är dock inte rätt ställe för att skapa en WebHost i en tillförlitlig tjänst eftersom startpunkten program bara används för att registrera en tjänsttyp med Service Fabric-körningen så att den kan skapa instanser av den typ av tjänst. WebHost ska skapas i en tillförlitlig tjänst själva. Instanser av tjänsten och/eller repliker kan gå igenom flera livscykler inom värdprocess service. 

En tillförlitlig tjänstinstans representeras av din tjänstklass som härleds från `StatelessService` eller `StatefulService`. Kommunikationsstack för en tjänst finns i en `ICommunicationListener` implementering i din tjänstklass. Den `Microsoft.ServiceFabric.AspNetCore.*` NuGet-paket som innehåller implementeringar av `ICommunicationListener` som starta och hantera ASP.NET Core WebHost för Kestrel eller HttpSys i en tillförlitlig tjänst.

![Som är värd för ASP.NET Core i en tillförlitlig tjänst][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Den `ICommunicationListener` implementeringar för Kestrel och HttpSys i den `Microsoft.ServiceFabric.AspNetCore.*` NuGet-paket har liknande användningsmönster men utföra något annorlunda åtgärder som är specifika för varje webbserver. 

Båda kommunikationslyssnarna innehåller en konstruktor som använder följande argument:
 - **`ServiceContext serviceContext`**: Det `ServiceContext` objekt som innehåller information om tjänsten som körs.
 - **`string endpointName`**: namnet på en `Endpoint` konfigurationen i ServiceManifest.xml. Detta är främst där två kommunikationslyssnarna skiljer sig åt: HttpSys **kräver** en `Endpoint` konfiguration, men inte av Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: en lambda som du implementerar i som du skapar och returnera ett `IWebHost`. På så sätt kan du konfigurera `IWebHost` på sätt som du vanligtvis använder i ett ASP.NET Core-program. Lambda innehåller en URL som genereras för du beroende på Service Fabric-integrering alternativ du använder och `Endpoint` konfiguration som du anger. Att URL: en sedan kan ändras eller används som – är att starta webbservern.

## <a name="service-fabric-integration-middleware"></a>Mellanprogram för Service Fabric-integrering
Den `Microsoft.ServiceFabric.AspNetCore` NuGet-paketet innehåller den `UseServiceFabricIntegration` tilläggsmetod på `IWebHostBuilder` som lägger till Service Fabric-medvetna mellanprogram. Den här mellanprogram konfigurerar Kestrel eller HttpSys `ICommunicationListener` att registrera en unik tjänst-URL med Service Fabric Naming-tjänst och sedan validerar klientbegäranden för att se till att klienter ansluter till en tjänst. Detta är nödvändigt i en miljö med delad värden, till exempel Service Fabric, där flera webbprogram kan köras på samma fysiska eller virtuella datorn men inte använder unika värdnamn, för att förhindra att klienter ansluter av misstag till fel-tjänsten. Det här scenariot beskrivs närmare i nästa avsnitt.

### <a name="a-case-of-mistaken-identity"></a>Ett ärende med felaktiga identitet
Tjänsten repliker, oavsett protokoll, lyssna på en unik IP:port kombination. När en replik för tjänsten har startats lyssnar på en slutpunkt för IP:port rapporterar den slutpunktsadressen till den namngivning i Service Fabric där den kan identifieras av klienter eller andra tjänster. Om tjänster använder dynamiskt tilldelade programportar, kan en tjänsterepliken tillfälligtvis använda samma IP:port slutpunkten för en annan tjänst som tidigare fanns på samma fysiska eller virtuella datorn. Detta kan medföra att en klient till mistakely ansluta till tjänsten fel. Detta kan inträffa om följande sekvens av händelser inträffar:

 1. Tjänsten A lyssnar på 10.0.0.1:30000 via HTTP. 
 2. Klienten matchar tjänst A och hämtar adress 10.0.0.1:30000
 3. Tjänsten A flyttar till en annan nod.
 4. Tjänsten B är placerad på 10.0.0.1 och använder tillfälligtvis samma port 30000.
 5. Klienten försöker ansluta till tjänsten A med cachelagrade adress 10.0.0.1:30000.
 6. Klienten är nu anslutits till tjänsten B förverkliga den inte är ansluten till tjänsten fel.

Detta kan orsaka fel vid slumpmässigt tidpunkter som kan vara svårt att diagnostisera. 

### <a name="using-unique-service-urls"></a>Med hjälp av unik tjänst-URL: er
Om du vill förhindra detta kan tjänster publicera en slutpunkt på Naming Service med en unik identifierare och sedan validera den unika identifieraren under klientbegäranden. Det här är en samordnad åtgärd mellan tjänster i en betrodd miljö för icke-fientlig-klient. Detta ger inte säker tjänst-autentisering i en miljö fientlig organisationer.

I en betrodd miljö, mellanprogrammet som läggs till av den `UseServiceFabricIntegration` metoden lägger automatiskt till en unik identifierare till den adress som publiceras till tjänsten namngivning och verifierar den identifieraren för varje begäran. Om inte matchar det ID: t, returnerar mellanprogrammet omedelbart ett svar med HTTP 410 sidan finns inte.

Tjänster som använder en dynamiskt tilldelad port bör se användning av den här mellanprogram.

Tjänster som använder en fast unik port har inte det här problemet i en integrerad miljö. En fast unik port används vanligtvis för utåtriktade tjänster som behöver en känd port för klientprogram att ansluta till. Till exempel använder de flesta Internet-riktade webbprogram port 80 eller 443 för web browser anslutningar. I detta fall bör den unika identifieraren inte är aktiverat.

Följande diagram visar begärandeflödet med mellanprogram aktiverat:

![Service Fabric ASP.NET Core-integrering][2]

Både Kestrel och HttpSys `ICommunicationListener` implementeringar använder den här mekanismen i exakt samma sätt. Även om HttpSys internt kan skilja förfrågningar baserat på unika URL-sökvägar med hjälp av den underliggande *http.sys* port funktionen, som fungerar för delning *inte* används av HttpSys `ICommunicationListener` implementeringen eftersom det leder HTTP 503-fel och HTTP 404-fel statuskoder i scenariot ovan. Som i sin tur gör det svårt för klienter för att fastställa syftet med felet, som HTTP 503-fel och HTTP 404 redan brukar användas för att indikera andra fel. Därför både Kestrel och HttpSys `ICommunicationListener` implementeringar som standard mellanprogram som tillhandahålls av den `UseServiceFabricIntegration` tilläggsmetod så att klienter behöver bara utföra en tjänstslutpunkt igen lösa åtgärd på 410 för HTTP-svar.

## <a name="httpsys-in-reliable-services"></a>HttpSys Reliable Services
HttpSys kan användas i en tillförlitlig tjänst genom att importera den **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-paketet. Det här paketet innehåller `HttpSysCommunicationListener`, en implementering av `ICommunicationListener`, som gör det möjligt att skapa en ASP.NET Core WebHost i en tillförlitlig tjänst med HttpSys som webbservern.

HttpSys bygger på den [Windows http-Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Här används den *http.sys* kerneldrivrutinen används av IIS för att bearbeta begäranden för HTTP och vidarebefordrar dem till processer som körs webbprogram. På så sätt kan flera processer på samma fysiska eller virtuella datorn för värdbaserade webbprogram på samma port skiljas åt av en unik URL-sökvägen eller värdnamn. De här funktionerna är användbara i Service Fabric som värd för flera webbplatser i samma kluster.

>[!NOTE]
>HttpSys implementering fungerar endast på Windows-plattformen.

Följande diagram illustrerar hur HttpSys använder den *http.sys* kerneldrivrutinen på Windows för delning av port:

![HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys i en tillståndslös tjänst
Att använda `HttpSys` i en tillståndslös tjänst åsidosätta den `CreateServiceInstanceListeners` metoden och returnera en `HttpSysCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HttpSys i en tillståndskänslig tjänst

`HttpSysCommunicationListener` för närvarande är inte avsedd för användning i tillståndskänsliga tjänster på grund av problem med den underliggande *http.sys* port funktionen för delning. Mer information finns i följande avsnitt på dynamisk porttilldelning med HttpSys. För tillståndskänsliga tjänster är Kestrel rekommenderade webbservern.

### <a name="endpoint-configuration"></a>Konfiguration av tjänstslutpunkt

En `Endpoint` krävs konfiguration för webbservrar som använder Windows http-Server API: et, inklusive HttpSys. Webbservrar som använder API: et Windows http-Server måste först reservera URL-Adressen med *http.sys* (detta görs normalt med den [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) verktyget). Den här åtgärden kräver förhöjd behörighet som inte har dina tjänster som standard. ”Http” eller ”https” alternativ för den `Protocol` egenskapen för den `Endpoint` konfigurationen i *ServiceManifest.xml* används specifikt för att instruera Service Fabric-körning för att registrera en URL med  *HTTP.sys* på din räkning med hjälp av den [ *starkt jokertecken* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-prefix.

Till exempel för att reservera `http://+:80` för en tjänst följande konfiguration ska användas i ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Och namnet på slutpunkten måste skickas till den `HttpSysCommunicationListener` konstruktorn:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Använda HttpSys med en statisk port
Om du vill använda en statisk port med HttpSys, ange portnumret i den `Endpoint` konfiguration:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Använda HttpSys med en dynamisk port
Om du vill använda en dynamiskt tilldelad port med HttpSys utelämna den `Port` -egenskapen i den `Endpoint` konfiguration:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

En dynamisk port som allokerats av en `Endpoint` konfigurationen ger endast en port *per värdprocess*. Den aktuella Service Fabric-värdmodellen gör att flera instanser av tjänsten och/eller repliker ska finnas i samma process, vilket innebär att var och en delar samma port när tilldelade rakt igenom den `Endpoint` konfiguration. Flera HttpSys instanser kan dela en port med den underliggande *http.sys* port delning av funktionen, men som inte stöds av `HttpSysCommunicationListener` på grund av komplikationer införs för klientbegäranden. För användning av dynamisk port är Kestrel rekommenderade webbservern.

## <a name="kestrel-in-reliable-services"></a>Kestrel Reliable Services
Kestrel kan användas i en tillförlitlig tjänst genom att importera den **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-paketet. Det här paketet innehåller `KestrelCommunicationListener`, en implementering av `ICommunicationListener`, som gör det möjligt att skapa en ASP.NET Core WebHost i en tillförlitlig tjänst med hjälp av Kestrel som webbservern.

Kestrel är en plattformsoberoende webbserver för ASP.NET Core utifrån libuv, ett plattformsoberoende asynkrona i/o-bibliotek. Till skillnad från HttpSys, Kestrel använder inte en centraliserad endpoint-hanteraren som *http.sys*. Och till skillnad från HttpSys, Kestrel har inte stöd för portdelning mellan flera processer. Varje instans av Kestrel måste använda en unik port.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel i en tillståndslös tjänst
Att använda `Kestrel` i en tillståndslös tjänst åsidosätta den `CreateServiceInstanceListeners` metoden och returnera en `KestrelCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel i en tillståndskänslig tjänst
Att använda `Kestrel` i en tillståndskänslig tjänst åsidosätta den `CreateServiceReplicaListeners` metoden och returnera en `KestrelCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

I det här exemplet är en singleton-instans av `IReliableStateManager` har angetts till behållaren WebHost beroende inmatning. Detta inte är absolut nödvändigt, men du kan använda `IReliableStateManager` och tillförlitliga samlingar i åtgärden för MVC kontrollantmetoder.

En `Endpoint` namn **inte** till `KestrelCommunicationListener` i en tillståndskänslig tjänst. Detta förklaras i detalj i följande avsnitt.

### <a name="endpoint-configuration"></a>Konfiguration av tjänstslutpunkt
En `Endpoint` konfiguration inte krävs för att använda Kestrel. 

Kestrel är en enkel fristående webbserver; till skillnad från HttpSys (eller HttpListener) behöver inte en `Endpoint` konfigurationen i *ServiceManifest.xml* eftersom det inte kräver URL-registreringen innan du börjar. 

#### <a name="use-kestrel-with-a-static-port"></a>Använda Kestrel med en statisk port
En statisk port kan konfigureras i den `Endpoint` konfigurationen av ServiceManifest.xml för användning med Kestrel. Även om det inte är absolut nödvändigt, finns det två potentiella fördelar:
 1. Om porten inte faller inom programportsintervallet, har filen öppnats genom brandväggen OS av Service Fabric.
 2. URL: en som tillhandahålls via `KestrelCommunicationListener` kommer att använda den här porten.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Om en `Endpoint` är konfigurerad, dess namn måste skickas till den `KestrelCommunicationListener` konstruktorn: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Om en `Endpoint` används inte konfigureringen, utelämnar namn i den `KestrelCommunicationListener` konstruktor. I det här fallet används en dynamisk port. Se nästa avsnitt för mer information.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Använda Kestrel med en dynamisk port
Kestrel kan inte använda automatisk porttilldelning från den `Endpoint` konfigurationen i ServiceManifest.xml, eftersom automatisk porttilldelning från en `Endpoint` konfigurationen tilldelar en unik port per *värd processen*, och en enda värdprocess kan innehålla flera Kestrel instanser. Eftersom Kestrel inte har stöd för delning av port, fungerar det inte som varje Kestrel-instans måste vara öppna på en unik port.

Om du vill använda dynamisk porttilldelning med Kestrel utelämna den `Endpoint` konfigurationen i ServiceManifest.xml helt och hållet, och väljer att inte skicka ett slutpunktnamn för att den `KestrelCommunicationListener` konstruktorn:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

I den här konfigurationen `KestrelCommunicationListener` väljer automatiskt en ledig port från portintervallet för programmet.

## <a name="scenarios-and-configurations"></a>Scenarier och konfigurationer
Det här avsnittet beskriver följande scenarier och innehåller rekommenderade kombinationen av webbserver, portkonfiguration, Service Fabric-integreringsalternativ och övriga inställningar för att uppnå en korrekt fungerande tjänst:
 - Externt exponeras tillståndslös ASP.NET Core-tjänst
 - Endast internt ASP.NET Core tillståndslös tjänst
 - Endast internt ASP.NET Core tillståndskänslig tjänst

En **externt exponeras** service är en som Exponerar en slutpunkt som kan nås från utanför klustret, vanligtvis via en belastningsutjämnare.

En **interna** service är en vars slutpunkten är endast kan nås från inom klustret.

> [!NOTE]
> Tillståndskänsliga Tjänsteslutpunkter bör Allmänt inte exponeras för Internet. Kluster som finns bakom en belastningsutjämnare som inte känner till Service Fabric service lösning, till exempel Azure Load Balancer kommer inte att exponera tillståndskänsliga tjänster eftersom belastningsutjämnaren inte kommer att kunna hitta och vidarebefordra trafik till lämplig repliken är tillståndskänslig tjänst. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externt exponeras tillståndslös ASP.NET Core-tjänster
Kestrel är den rekommenderade webbservern för frontend-tjänster som visar extern, Internet-ansluten HTTP-slutpunkter. På Windows, kan HttpSys användas för att tillhandahålla port delningsapplikationen kapacitet som kan du ha flera webbtjänster på samma uppsättning noder som använder samma port, med hjälp av värdnamn eller sökväg, utan att behöva en frontend proxy eller gateway för att tillhandahålla routning av HTTP.
 
När du är exponerade för Internet, bör en tillståndslös tjänst använda en välkänd och stabil slutpunkt som kan nås via en belastningsutjämnare. Detta är den URL som du tillhandahåller till användare av ditt program. Det rekommenderas följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | kestrel | Kestrel är prioriterade webbservern eftersom den stöds i Windows och Linux. |
| Portkonfiguration | statisk | En känd statisk port ska ställas in på den `Endpoints` konfiguration av ServiceManifest.xml, till exempel 80 för HTTP och port 443 för HTTPS. |
| ServiceFabricIntegrationOptions | Ingen | Den `ServiceFabricIntegrationOptions.None` bör användas när du konfigurerar mellanprogram för Service Fabric-integrering så att tjänsten inte försöker verifiera inkommande förfrågningar för en unik identifierare. Externa användare av ditt program vet inte den unika identifieringsinformation som används av oss mellanprogrammet. |
| Antal instanser | -1 | I vanliga användningsfall bör instansantalet inställningen anges till ”-1” så att en instans är tillgänglig på alla noder som tar emot trafik från en belastningsutjämnare. |

Om flera tjänster som externt exponeras delar samma uppsättning noder, kan HttpSys användas med ett unikt men stabil URL-sökväg. Detta kan åstadkommas genom att modifiera den URL som anges när du konfigurerar IWebHost. Observera att detta endast gäller för HttpSys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Endast internt tillståndslös ASP.NET Core-tjänst
Tillståndslösa tjänster som endast anropas från i klustret ska använda unika URL: er och dynamiskt tilldelade portar för att säkerställa samarbete mellan flera tjänster. Det rekommenderas följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | kestrel | Även om HttpSys kan användas för interna tillståndslösa tjänster, är Kestrel rekommenderade servern så att flera instanser av tjänsten att dela en värd.  |
| Portkonfiguration | dynamiskt tilldelade | Flera kopior av en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystemet och därför måste unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Den här inställningen förhindrar felaktiga identitet problemet som beskrivs ovan med dynamisk porttilldelning. |
| InstanceCount | valfri | Ange instansantalet kan ställas in till ett värde krävs för att driva tjänsten. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Endast internt tillståndskänslig ASP.NET Core-tjänst
Tillståndskänsliga tjänster som endast anropas från i klustret ska använda dynamiskt tilldelade portar för samarbete mellan flera tjänster. Det rekommenderas följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | kestrel | Den `HttpSysCommunicationListener` är inte avsedd för användning av tillståndskänsliga tjänster där repliker dela en värdprocess. |
| Portkonfiguration | dynamiskt tilldelade | Flera kopior av en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystemet och därför måste unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Den här inställningen förhindrar felaktiga identitet problemet som beskrivs ovan med dynamisk porttilldelning. |

## <a name="next-steps"></a>Nästa steg
[Felsöka ditt Service Fabric-program med Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
