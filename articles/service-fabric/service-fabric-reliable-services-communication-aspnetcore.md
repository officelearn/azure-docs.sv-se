---
title: "Tjänsten kommunikation med ASP.NET Core | Microsoft Docs"
description: "Lär dig hur du använder ASP.NET Core i tillståndslösa och tillståndskänsliga Reliable Services."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: a98e9ad891fcfaf02ca7df5d10d5b310445c9d34
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core i Service Fabric Reliable Services

ASP.NET Core är ett nytt öppen källkod och plattformsoberoende ramverk för att bygga moderna molnbaserade Internetanslutna program, till exempel webbappar, IoT-appar och mobila serverdelar. 

Den här artikeln är utförliga instruktioner för hur ASP.NET Core värdtjänster i Service Fabric Reliable Services med hjälp av den **Microsoft.ServiceFabric.AspNetCore.** * uppsättning NuGet-paket.

En inledande självstudiekurs om ASP.NET Core i Service Fabric och anvisningar om hur din utvecklingsmiljö konfigurera finns i [bygga en webbklientdel för ditt program med hjälp av ASP.NET Core](service-fabric-add-a-web-frontend.md).

Resten av den här artikeln förutsätter att du redan är bekant med ASP.NET Core. Om inte, vi rekommenderar att läsa igenom den [ASP.NET grunderna](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core i Service Fabric-miljö

När ASP.NET Core appar kan köra på .NET Core eller fullständig .NET Framework, Service Fabric tjänster kan för närvarande endast köras på fullständig .NET Framework. Det innebär när du skapar en ASP.NET Core Service Fabric-tjänsten måste du fortfarande ha fullständig .NET Framework.

ASP.NET Core kan användas på två olika sätt i Service Fabric:
 - **Värdbaserad som en gäst körbar fil**. Främst används för att köra befintliga ASP.NET Core program på Service Fabric utan ändringar i koden.
 - **Körs i en tillförlitlig tjänst**. Detta kan bättre integration med Service Fabric-runtime och tillståndskänsliga ASP.NET Core services.

Resten av den här artikeln förklarar hur du använder ASP.NET Core inuti en tillförlitlig tjänst med ASP.NET Core integrationskomponenterna som levereras med Fabric-SDK. 

## <a name="service-fabric-service-hosting"></a>Tjänsten värd för Service Fabric

I Service Fabric en eller flera instanser och/eller repliker av din tjänst köras i en *tjänsten värdprocess*, en körbar fil som körs koden för tjänsten. Du, som en tjänst författare äger värdprocessen för service och Service Fabric aktiveras och övervakar du.

Traditionella ASP.NET (upp till MVC 5) är direkt kopplade till IIS via System.Web.dll. ASP.NET Core ger en åtskillnad mellan webbservern och ditt webbprogram. Detta gör webbprogram kan flyttas mellan olika webbservrar och kan också webbservrarna ska *egenvärdbaserat*, vilket betyder att du kan starta en webbserver i en egen process, till skillnad från en process som ägs av dedicated programvara för webbserver, till exempel IIS. 

För att kombinera ett Service Fabric-tjänsten och ASP.NET, antingen som en gäst körbar fil eller i en tillförlitlig tjänst måste du kunna starta ASP.NET i din serverprocess. ASP.NET Core värd själv kan du göra detta.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Värd för ASP.NET Core i en tillförlitlig tjänst
Vanligtvis själva ASP.NET Core program skapar ett vid startpunkten för ett program, som den `static void Main()` metod i `Program.cs`. I det här fallet är livscykeln för Webbvärden bunden till livscykeln för processen.

![Värd för ASP.NET Core i en process][0]

Startpunkt för programmet är dock inte på rätt plats för att skapa ett i en tillförlitlig tjänst eftersom startpunkt för programmet bara används för att registrera en typ med Service Fabric-körningsmiljön så att den kan skapa instanser av den typ av tjänst. Webbvärden ska skapas i en tillförlitlig tjänst sig själv. Instanser av tjänsten och/eller repliker kan gå igenom flera livscykler inom värdprocess service. 

En tillförlitlig tjänstinstans representeras av din tjänstklass som härleds från `StatelessService` eller `StatefulService`. Kommunikation-stack för en tjänst som finns i en `ICommunicationListener` implementering service-klassen. Den `Microsoft.ServiceFabric.Services.AspNetCore.*` NuGet-paket som innehåller implementeringar av `ICommunicationListener` som starta och hantera ASP.NET Core Webbvärden för Kestrel eller HttpSys i en tillförlitlig tjänst.

![Värd för ASP.NET Core i en tillförlitlig tjänst][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Den `ICommunicationListener` implementeringar för Kestrel och HttpSys i den `Microsoft.ServiceFabric.Services.AspNetCore.*` NuGet-paket har liknande användningsmönster men utföra lite olika åtgärder som är specifika för varje webbserver. 

Både kommunikationslyssnarna ger en konstruktor som tar följande argument:
 - **`ServiceContext serviceContext`**: `ServiceContext` Objekt som innehåller information om tjänsten körs.
 - **`string endpointName`**: namnet på en `Endpoint` konfigurationen i ServiceManifest.xml. Detta är främst där två kommunikationslyssnarna skiljer sig: HttpSys **kräver** en `Endpoint` konfiguration, men inte av Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: ett lambda-uttryck som du implementerar i som du skapar och returnera ett `IWebHost`. Detta kan du konfigurera `IWebHost` på sätt som vanligt i ett program för ASP.NET Core. Lambda innehåller en URL som har genererats för du beroende på Service Fabric-integration alternativ du använder och `Endpoint` konfiguration som du anger. Att URL: en sedan kan ändras eller används som-är att börja på webbservern.

## <a name="service-fabric-integration-middleware"></a>Mellanprogram för Service Fabric-integrering
Den `Microsoft.ServiceFabric.Services.AspNetCore` NuGet-paketet innehåller den `UseServiceFabricIntegration` tilläggsmetoden på `IWebHostBuilder` som lägger till Service Fabric-medvetna mellanprogram. Den här mellanprogram konfigurerar Kestrel eller HttpSys `ICommunicationListener` att registrera en unik URL med Service Fabric Naming Service och verifierar klienten begär att se till att klienter ansluter till en tjänst. Detta är nödvändigt i en miljö med delad värden, till exempel Service Fabric där flera webbprogram kan köra på samma fysiska eller virtuella datorn men inte använder unika värdnamn, för att förhindra att klienter ansluter av misstag till fel tjänst. Det här scenariot beskrivs närmare i nästa avsnitt.

### <a name="a-case-of-mistaken-identity"></a>Ett fall av felaktig identitet
Tjänsten repliker, oavsett protokoll, lyssna på en unik IP:port kombination. När en replik för tjänsten har börjat lyssna på en IP:port slutpunkt, rapporterar den slutpunktsadressen till tjänsten Fabric Naming Service där den kan identifieras av klienter eller andra tjänster. Om tjänster använder dynamiskt tilldelade programmet portar, kan tjänsten replik tillfälligtvis använda samma IP:port slutpunkten för en annan tjänst som tidigare var på samma fysiska eller virtuella datorn. Detta kan medföra att en klient till mistakely ansluta till tjänsten fel. Detta kan inträffa om följande sekvens av händelser inträffar:

 1. Tjänsten A lyssnar på 10.0.0.1:30000 via HTTP. 
 2. Klienten matchar A Service och hämtar adress 10.0.0.1:30000
 3. Tjänsten A flyttar till en annan nod.
 4. Tjänsten B är placerad 10.0.0.1 och använder tillfälligtvis samma port 30000.
 5. Klienten försöker ansluta till tjänsten A med cachelagrade adress 10.0.0.1:30000.
 6. Klienten är nu har ansluten till tjänsten B märker det. inte är ansluten till tjänsten fel.

Detta kan orsaka fel med slumpmässiga som kan vara svårt att felsöka. 

### <a name="using-unique-service-urls"></a>Med unika URL: er
Om du vill förhindra detta services efter en slutpunkt på Naming Service med en unik identifierare och sedan validera den unika identifieraren under klientbegäranden. Detta är en samordnad åtgärd mellan tjänster i en betrodd miljö för icke-farliga-klient. Detta ger inte säkra autentiseringsuppgifter i en miljö med farliga klient.

I en betrodd miljö, mellanprogram som läggs till av den `UseServiceFabricIntegration` metoden lägger automatiskt till en unik identifierare för den adress som skickas till tjänsten namngivning och validerar den identifieraren för varje begäran. Om ID inte matchar returnerar mellanprogram omedelbart ett HTTP 410 rest-svar.

Tjänster som använder en dynamiskt tilldelad port bör se användning av den här mellanprogram.

Tjänster som använder en fast unika port har inte det här problemet i en integrerad miljö. En fast unika port används vanligtvis för externt riktade som behöver en välkända port för program att ansluta till. Till exempel använder de flesta Internet-riktade webbprogram port 80 eller 443 för web webbläsare anslutningar. I det här fallet ska den unika identifieraren inte aktiveras.

Följande diagram visar begäran flödar med mellanprogram aktiverad:

![Service Fabric ASP.NET Core-integrering][2]

Både Kestrel och HttpSys `ICommunicationListener` implementeringar använder den här mekanismen i exakt samma sätt. Även om HttpSys internt kan skilja förfrågningar baserat på unika URL-sökvägar med hjälp av den underliggande *http.sys* portdelning funktion som funktionen är *inte* används av HttpSys `ICommunicationListener` implementeringen eftersom som leder HTTP 503- och HTTP 404-fel statuskoder i scenariot ovan. Som i sin tur gör det svårt för klienter att identifiera syftet med fel, eftersom HTTP 503- och HTTP 404 är redan används vanligtvis för att ange andra fel. Därför både Kestrel och HttpSys `ICommunicationListener` implementeringar standardisera mellanprogram som tillhandahålls av den `UseServiceFabricIntegration` tilläggsmetoden så att klienter behöver bara göra en tjänstslutpunkt nytt lösa åtgärd på 410 HTTP-svar.

## <a name="httpsys-in-reliable-services"></a>HttpSys i Reliable Services
HttpSys kan användas i en tillförlitlig tjänst genom att importera den **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-paketet. Det här paketet innehåller `HttpSysCommunicationListener`, en implementering av `ICommunicationListener`, där du kan skapa en ASP.NET Core Webbvärden inuti en tillförlitlig tjänst med HttpSys som webbservern.

HttpSys bygger på den [Windows http-Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Här används den *http.sys* kernel-drivrutinen används av IIS för att bearbeta begäranden för HTTP och vidarebefordra dem till processer som körs webbprogram. Detta gör att flera processer på samma fysiska eller virtuella datorn till värden webbprogram på samma port skiljas åt av en unik URL-sökvägen eller värdnamn. Dessa funktioner är användbara i Service Fabric som värd för flera webbplatser i samma kluster.

Följande diagram illustrerar hur HttpSys använder den *http.sys* kernel-drivrutinen i Windows för delning av port:

![HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys i en tillståndslös tjänst
Att använda `HttpSys` i en tillståndslös tjänst åsidosätta den `CreateServiceInstanceListeners` metod och returnera ett `HttpSysCommunicationListener` instans:

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

### <a name="httpsys-in-a-stateful-service"></a>HttpSys i en tillståndskänslig service

`HttpSysCommunicationListener`för närvarande är inte avsedd för användning i tillståndskänsliga tjänster på grund av problem med den underliggande *http.sys* portdelning funktionen. Mer information finns i följande avsnitt på dynamisk porttilldelning med HttpSys. För tillståndskänsliga tjänster är Kestrel rekommenderade webbservern.

### <a name="endpoint-configuration"></a>Slutpunktskonfiguration

En `Endpoint` konfiguration krävs för webbservrar som använder Windows http-Server API, inklusive HttpSys. Webbservrar som använder API: et Windows HTTP-servern måste först reservera URL-Adressen med *http.sys* (detta görs normalt med den [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) tool). Den här åtgärden kräver förhöjd behörighet som inte har dina tjänster som standard. ”Http” eller ”https” alternativ för den `Protocol` -egenskapen för den `Endpoint` konfigurationen i *ServiceManifest.xml* används specifikt för att instruera Service Fabric runtime att registrera en URL med *http.sys* på din räkning genom att använda den [ *starkt jokertecken* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-prefix.

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
Om du vill använda en dynamiskt tilldelad port med HttpSys utelämna den `Port` egenskap i den `Endpoint` konfiguration:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Observera att en dynamisk port allokerats av en `Endpoint` konfiguration innehåller endast en port *per värdprocess*. Den aktuella Service Fabric-värdmodellen gör att flera instanser av tjänsten och/eller repliker ska finnas i samma process, vilket innebär att var och en delar samma port vid via den `Endpoint` konfiguration. Flera instanser av HttpSys kan dela en port med hjälp av den underliggande *http.sys* portdelning funktionen, men som inte stöds av `HttpSysCommunicationListener` på grund av problem som införs för klientbegäranden. För användning av dynamisk port är Kestrel rekommenderade webbservern.

## <a name="kestrel-in-reliable-services"></a>Kestrel i Reliable Services
Kestrel kan användas i en tillförlitlig tjänst genom att importera den **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-paketet. Det här paketet innehåller `KestrelCommunicationListener`, en implementering av `ICommunicationListener`, där du kan skapa en ASP.NET Core Webbvärden inuti en tillförlitlig tjänst med Kestrel som webbservern.

Kestrel är en plattformsoberoende webbserver för ASP.NET Core utifrån libuv, ett plattformsoberoende asynkrona i/o-bibliotek. Till skillnad från HttpSys, Kestrel inte använder en centraliserad endpoint-hanteraren som *http.sys*. Och till skillnad från HttpSys, Kestrel stöder inte portdelning mellan flera processer. Varje instans av Kestrel måste använda en unik port.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel i en tillståndslös tjänst
Att använda `Kestrel` i en tillståndslös tjänst åsidosätta den `CreateServiceInstanceListeners` metod och returnera ett `KestrelCommunicationListener` instans:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel i en tillståndskänslig service
Att använda `Kestrel` i en tillståndskänslig service åsidosätta den `CreateServiceReplicaListeners` metod och returnera ett `KestrelCommunicationListener` instans:

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

I det här exemplet är en singleton-instans av `IReliableStateManager` har angetts för behållaren Webbvärden beroende injection. Detta är inte absolut nödvändigt, men du kan använda `IReliableStateManager` och tillförlitlig samlingar i åtgärden för MVC kontrollantmetoder.

Observera att en `Endpoint` Konfigurationsnamnet **inte** till `KestrelCommunicationListener` i en tillståndskänslig service. Detta förklaras i detalj i följande avsnitt.

### <a name="endpoint-configuration"></a>Slutpunktskonfiguration
En `Endpoint` konfiguration krävs inte att använda Kestrel. 

Kestrel är en enkel fristående webbservern. till skillnad från HttpSys (eller HttpListener), det måste inte en `Endpoint` konfigurationen i *ServiceManifest.xml* eftersom det inte kräver URL-registrering innan du startar. 

#### <a name="use-kestrel-with-a-static-port"></a>Använda Kestrel med en statisk port
En statisk port kan konfigureras i den `Endpoint` konfigurationen av ServiceManifest.xml för användning med Kestrel. Även om detta inte är absolut nödvändigt har två potentiella fördelar:
 1. Om porten inte ingår i programmet portintervall, används som i OS-brandväggen av Service Fabric.
 2. URL: en som tillhandahålls via `KestrelCommunicationListener` kommer att använda den här porten.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Om en `Endpoint` är konfigurerat, filnamnet måste skickas till den `KestrelCommunicationListener` konstruktorn: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Om en `Endpoint` används inte konfigurering, utelämna namn i den `KestrelCommunicationListener` konstruktor. I det här fallet används en dynamisk port. Finns i nästa avsnitt för mer information.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Använda Kestrel med en dynamisk port
Kestrel kan inte använda automatisk porttilldelning från den `Endpoint` konfigurationen i ServiceManifest.xml, eftersom automatisk porttilldelning från en `Endpoint` konfigurationen tilldelar en unik port per *värd processen*, och en enda värdprocess kan innehålla flera Kestrel instanser. Eftersom Kestrel inte stöder portdelning är fungerar detta inte som varje Kestrel-instans måste öppnas på en unik port.

Om du vill använda dynamisk porttilldelning med Kestrel bara utelämna den `Endpoint` konfigurationen i ServiceManifest.xml helt, och inte ange ett slutpunktnamn på ska den `KestrelCommunicationListener` konstruktorn:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

I den här konfigurationen `KestrelCommunicationListener` väljer automatiskt en ledig port från portintervall för programmet.

## <a name="scenarios-and-configurations"></a>Scenarier och konfigurationer
Det här avsnittet beskriver följande scenarier och innehåller rekommenderade kombinationen av webbserver, portkonfiguration, alternativ för Service Fabric-integrering och övriga inställningar för att få en korrekt fungerande tjänst:
 - Externt exponeras ASP.NET Core tillståndslösa tjänsten
 - Endast internt ASP.NET Core tillståndslösa tjänsten
 - Endast internt ASP.NET Core tillståndskänslig service

En **externt exponeras** service är en som Exponerar en slutpunkt som kan nås från utanför klustret, vanligtvis via en belastningsutjämnare.

En **endast är interna** service är en vars endpoint kan endast nås från inom klustret.

> [!NOTE]
> Tillståndskänsliga Tjänsteslutpunkter bör normalt inte exponeras för Internet. Kluster som finns bakom en belastningsutjämnare som inte känner till Service Fabric-tjänsten upplösning, till exempel den Azure belastningsutjämnare, kommer att kunna exponera tillståndskänsliga tjänster eftersom belastningsutjämnaren inte kan hitta och dirigera trafik till lämplig tillståndskänslig tjänst repliken. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externt exponeras ASP.NET Core tillståndslösa tjänster
Kestrel är rekommenderade webbservern för frontend-tjänster som exponerar extern, mot Internet HTTP-slutpunkter. I Windows, kan HttpSys användas med funktioner för port fildelning där du kan vara värd för flera webbtjänster på samma uppsättning noder som använder samma port differentierade av värdnamn eller en sökväg utan att behöva använda en frontend-proxy eller gateway att tillhandahålla routning av HTTP.
 
När exponerad mot Internet, ska en tillståndslösa tjänsten använda en välkänd och stabil slutpunkt som kan nås via en belastningsutjämnare. Detta är den URL som du kommer att ge användare av ditt program. Du rekommenderas följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | kestrel | Kestrel är prioriterade webbservern som stöds i Windows och Linux. |
| Portkonfiguration | Statisk | En välkänd statisk port ska konfigureras i den `Endpoints` konfigurationen av ServiceManifest.xml, till exempel 80 för HTTP och 443 för HTTPS. |
| ServiceFabricIntegrationOptions | Ingen | Den `ServiceFabricIntegrationOptions.None` bör användas när du konfigurerar Service Fabric-integrering mellanprogram så att tjänsten inte försöker verifiera inkommande begäranden för en unik identifierare. Externa användare av ditt program vet inte unikt identifierande information som används av mellanprogram. |
| Antal instanser | -1 | I vanliga användningsområden ska instansantalet inställningen anges för ”1” så att en instans är tillgänglig på alla noder som tar emot trafik från en belastningsutjämnare. |

Om flera tjänster som externt synliga delar samma uppsättning noder, kan HttpSys användas med en unik men stabil URL-sökväg. Detta kan åstadkommas genom att ändra den Webbadress som anges när du konfigurerar IWebHost. Observera att detta gäller enbart för HttpSys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Endast internt tillståndslösa ASP.NET Core-tjänsten
Tillståndslösa tjänster som endast anropas från inom klustret ska använda unika URL: er och dynamiskt tilldelade portar för att säkerställa samarbete mellan flera tjänster. Du rekommenderas följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | kestrel | Även om HttpSys kan användas för interna tillståndslösa tjänster, är Kestrel rekommenderade servern så att flera instanser av tjänsten att dela en värd.  |
| Portkonfiguration | dynamiskt tilldelade | Flera repliker på en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystemet och därför måste unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Den här inställningen förhindrar med dynamisk porttilldelning felaktig identitet problemet som beskrivs ovan. |
| InstanceCount | alla | Instansantalet inställning kan anges till ett värde krävs för att använda tjänsten. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Endast internt tillståndskänslig ASP.NET Core-tjänsten
Tillståndskänsliga tjänster som endast anropas från inom klustret ska använda dynamiskt tilldelade portar för samarbete mellan flera tjänster. Du rekommenderas följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | kestrel | Den `HttpSysCommunicationListener` är inte avsedd för användning av tillståndskänsliga tjänster där repliker delar en värdprocess. |
| Portkonfiguration | dynamiskt tilldelade | Flera repliker på en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystemet och därför måste unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Den här inställningen förhindrar med dynamisk porttilldelning felaktig identitet problemet som beskrivs ovan. |

## <a name="next-steps"></a>Nästa steg
[Felsöka Service Fabric-program med hjälp av Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
