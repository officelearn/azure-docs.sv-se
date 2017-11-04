---
title: "Skapa din första Service Fabric-program i C# | Microsoft Docs"
description: "Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: f67b69e7ad1f7588280de82669040bad5ec6172b
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="get-started-with-reliable-services"></a>Kom igång med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Ett Azure Service Fabric-program innehåller en eller flera tjänster som körs din kod. Den här guiden visar hur du skapar både tillståndslösa och tillståndskänsliga Service Fabric-program med [Reliable Services](service-fabric-reliable-services-introduction.md).  Den här Microsoft Virtual Academy video visar även hur du skapar en tillståndslös tillförlitlig tjänst:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=s39AO76yC_7206218965">  
<img src="./media/service-fabric-reliable-services-quick-start/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="basic-concepts"></a>Grundläggande begrepp
Om du vill komma igång med Reliable Services, behöver du bara förstå några grundläggande begrepp:

* **Typen tjänst**: det här är din implementering. Den definieras av den klass som utökar du skriver `StatelessService` och annan kod eller beroenden, används tillsammans med ett namn och ett versionsnummer.
* **Med namnet tjänstinstans**: för att köra din tjänst måste du skapa namngivna instanser av service-typen mycket som du skapar objektinstanser av en klasstyp. En tjänstinstans har ett namn i form av en URI med hjälp av den ”fabric: /” schemat, till exempel ”fabric: / MyApp/MyService”.
* **Tjänstvärden**: namngiven tjänstinstanser som du skapar måste du köra inom en värdprocess. Tjänstevärden är bara en process där instanser av tjänsten kan köras.
* **Registrering av tjänst**: registrering sammanför allt. Tjänsttypen måste registreras med Service Fabric-körning i en tjänstevärd för att tillåta Service Fabric skapar instanser av den att köra.  

## <a name="create-a-stateless-service"></a>Skapa en tillståndslös tjänst
En tillståndslös tjänst är en typ av tjänst som används för närvarande normen i molnprogram. Anses tillståndslös eftersom själva tjänsten inte innehåller data som behöver lagras på ett tillförlitligt sätt eller hög tillgänglighet. Om en instans av en tillståndslösa tjänsten stängs av, förloras alla det interna tillståndet. I den här typen av tjänsten måste tillstånd sparas till en extern butik, till exempel Azure-tabeller eller en SQL-databas för att den ska göras hög tillgänglighet och tillförlitlig.

Starta Visual Studio 2015 eller Visual Studio 2017 som administratör och skapa ett nytt Service Fabric application projekt med namnet *HelloWorld*:

![Använd dialogrutan Nytt projekt för att skapa ett nytt Service Fabric-program](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Skapa ett projekt för tillståndslösa tjänsten med namnet *HelloWorldStateless*:

![Skapa ett projekt för tillståndslösa tjänsten i den andra dialogrutan](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Din lösning innehåller nu två projekt:

* *HelloWorld*. Det här är den *programmet* projekt som innehåller din *services*. Den innehåller också programmanifestet som beskriver programmet, samt ett antal PowerShell-skript som hjälper dig att distribuera ditt program.
* *HelloWorldStateless*. Detta är service-projekt. Den innehåller den tillståndslösa tjänstimplementeringen.

## <a name="implement-the-service"></a>Implementera tjänsten
Öppna den **HelloWorldStateless.cs** filen i service-projekt. En tjänst kan köra all affärslogik i Service Fabric. API för tjänsten tillhandahåller två startpunkter för din kod:

* En öppen metoden, kallas *RunAsync*, där du kan börja köra alla arbetsbelastningar, inklusive tidskrävande beräkning av arbetsbelastningar.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* En kommunikation startpunkt där du kan ansluta din kommunikation stack föredrar, till exempel ASP.NET Core. Det är där du kan börja ta emot begäranden från användare och andra tjänster.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

I den här självstudiekursen kommer vi fokusera på den `RunAsync()` metoden. Det är där du kan starta direkt köra din kod.
Projektmallen innehåller ett exempel på implementering av `RunAsync()` som ökar en löpande uppräkning.

> [!NOTE]
> Mer information om hur du arbetar med en stack kommunikation finns [Service Fabric Web API-tjänster med OWIN själva värd](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>RunAsync
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Plattformen som anropar den här metoden när en instans av en tjänst är monterade och redo att köra. För en tillståndslös tjänst innebär som bara när tjänstinstansen öppnas. En token för annullering tillhandahålls koordinera när service-instans måste stängas. I Service Fabric inträffa cykeln Öppna/stänga av en tjänstinstans många gånger under livslängden för tjänsten som helhet. Detta kan inträffa av olika orsaker, bland annat:

* Systemet flyttar instanser av tjänsten för resurser.
* Fel uppstår i koden.
* Programmets eller systemets uppgraderas.
* Den underliggande maskinvaran skulle få ett avbrott.

Den här orchestration hanteras av systemet för att hålla din tjänst hög tillgänglighet och korrekt belastningsutjämnade.

`RunAsync()`bör inte blockera synkront. Implementeringen av RunAsync ska returnera en uppgift eller await på alla tidskrävande eller blockerar åtgärder för att fortsätta körningen. Observera i den `while(true)` loop i föregående exempel är en åtgärdsreturnerande `await Task.Delay()` används. Om din arbetsbelastning måste blockerar synkront kan du schemalägga en ny uppgift med `Task.Run()` i din `RunAsync` implementering.

Annullering av din arbetsbelastning är en samverkande ansträngning styrd av den angivna annullering-token. Systemet väntar på uppgiften till slutet (genom lyckades, avbokning eller fel) innan den flyttas på. Det är viktigt att respektera cancellation-token, Slutför allt arbete och avsluta `RunAsync()` så snabbt som möjligt när systemet begär annullering.

I det här exemplet tillståndslösa tjänsten lagras antalet i en lokal variabel. Men eftersom detta är en tillståndslös tjänst är det värde som lagras finns bara för den aktuella livscykeln för dess tjänstinstansen. När tjänsten startas om flyttar går värdet förlorad.

## <a name="create-a-stateful-service"></a>Skapa en tillståndskänslig tjänst
Service Fabric introducerar en ny typ av tjänst som är tillståndskänslig. En tillståndskänslig service kan upprätthålla tillstånd på ett tillförlitligt sätt inom tjänsten samordnad med kod som använder den. Tillstånd görs högtillgänglig av Service Fabric utan att behöva spara tillstånd för att en extern butik.

Om du vill konvertera ett värde för prestandaräknaren från tillståndslös till hög tillgänglighet och beständiga, även när tjänsten flyttas eller startas om, behöver du en tillståndskänslig tjänst.

I samma *HelloWorld* programmet, du kan lägga till en ny tjänst genom att högerklicka på tjänsterna referenser i projektet för konsolprogrammet och välja **Lägg till ny tjänst för Service Fabric ->**.

![Lägga till en tjänst till Service Fabric-program](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Välj **tillståndskänslig Service** och ger den namnet *HelloWorldStateful*. Klicka på **OK**.

![Använd dialogrutan Nytt projekt för att skapa en ny tillståndskänslig Service Fabric-tjänst](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Ditt program bör nu ha två tjänster: tillståndslösa tjänsten *HelloWorldStateless* och tillståndskänsliga tjänsten *HelloWorldStateful*.

En tillståndskänslig tjänst har samma startpunkter som en tillståndslös tjänst. Den största skillnaden är tillgängligheten för en *tillståndsprovidern* som kan lagra tillstånd på ett tillförlitligt sätt. Service Fabric medföljer en implementering av provider för tillstånd kallas [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md), vilket låter dig skapa replikeras datastrukturer via Hanteraren för tillförlitlig tillstånd. En tillståndskänslig tillförlitlig tjänst använder den här tillståndsprovidern som standard.

Öppna **HelloWorldStateful.cs** i *HelloWorldStateful*, som innehåller följande RunAsync metod:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync
`RunAsync()`fungerar på samma sätt i tillståndskänsliga och tillståndslösa tjänster. Dock i en tillståndskänslig service plattformen utför ytterligare arbete för din räkning innan den kör `RunAsync()`. Detta verk kan inkludera att säkerställa att tillförlitliga Tillståndshanterare och tillförlitlig samlingar är redo att användas.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och hanteraren för tillförlitlig tillstånd
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) är en dictionary-implementering som du kan använda för att lagra på ett tillförlitligt sätt tillstånd i tjänsten. Med Service Fabric och tillförlitlig samlingar, kan du lagra data direkt i din tjänst utan behov av en extern beständiga arkivet. Tillförlitliga samlingar gör att dina data är hög tillgänglighet. Service Fabric åstadkommer detta genom att skapa och hantera flera *repliker* av tjänsten för dig. Det ger också en API som avlägsnar direkt svårigheter för att hantera dessa repliker och deras tillståndsövergångar.

Tillförlitliga samlingar kan lagra alla .NET-typ, inklusive din anpassade typer med några varningar.

* Service Fabric gör ditt tillstånd hög tillgänglighet av *replikerar* tillstånd mellan noder och tillförlitlig samlingar lagra data på en lokal disk på varje replik. Det innebär att allt som lagras i tillförlitliga samlingar måste vara *serialiserbara*. Som standard använder tillförlitliga samlingar [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) för serialisering, så det är viktigt att se till att din typer är [stöds av datakontraktserialiserren](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) när du använder standard-serialisering.
* Objekt replikeras för hög tillgänglighet när du gör transaktioner på tillförlitliga samlingar. Objekt som lagras i tillförlitliga samlingar sparas i lokalt minne i din tjänst. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte mutera lokala instanser av dessa objekt utan att utföra en Uppdateringsåtgärd i den tillförlitliga samlingen i en transaktion. Det beror på att ändringar i lokala instanser av objekt inte replikeras automatiskt. Du måste infoga objektet tillbaka till ordlistan igen eller Använd en av de *uppdatera* metoder i ordlistan.

Tillförlitliga Tillståndshanterarens hanterar tillförlitliga samlingar åt dig. Du kan bara be tillförlitliga Tillståndshanterarens för en tillförlitlig samling med namnet när som helst och var som helst i din tjänst. Den tillförlitliga tillstånd Manager ser till att du får en referens tillbaka. Inte rekommenderar vi att du sparar referenser till tillförlitliga samling instanser i klassmedlem egenskaper eller variabler. Särskild försiktighet måste vidtas för att säkerställa att referensen har angetts till en instans alltid i livscykeln för tjänsten. Tillförlitliga Tillståndshanterarens hanterar det här fungerar och har optimerats för Upprepa besök.

### <a name="transactional-and-asynchronous-operations"></a>Transaktionell och asynkrona åtgärder
```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Tillförlitliga samlingar har många av åtgärderna som deras `System.Collections.Generic` och `System.Collections.Concurrent` motsvarigheter göra, förutom LINQ. Åtgärder på tillförlitliga samlingar är asynkron. Det beror på att skrivåtgärder med tillförlitlig samlingar utföra i/o-åtgärder för att replikera och spara data till disk.

Åtgärder för insamling av tillförlitliga är *transaktionella*, så att du kan behålla tillstånd konsekvent över flera tillförlitliga samlingar och åtgärder. Du kan till exempel ett arbetsobjekt från en tillförlitlig kö har status Created, utföra en åtgärd på det och spara resultatet i en tillförlitlig ordlista alla inom en enskild transaktion. Detta behandlas som en atomisk åtgärd och det garanterar att hela åtgärden lyckas eller hela åtgärden återställs. Om ett fel inträffar efter att objektet har status Created men innan du sparar resultatet hela transaktionen återställs och artikeln finns kvar i kön för bearbetning.

## <a name="run-the-application"></a>Köra programmet
Vi Gå nu tillbaka till den *HelloWorld* program. Du kan nu skapa och distribuera tjänster. När du trycker på **F5**, ditt program kommer skapats och distribuerats till det lokala klustret.

När tjänsten startas, kan du visa de genererade ETW Event Tracing for Windows ()-händelserna i en **diagnostiska händelser** fönster. Observera att de händelser som visas är från både tjänsten tillståndslösa och tillståndskänsliga tjänsten i programmet. Du kan pausa dataströmmen genom att klicka på den **pausa** knappen. Du kan granska information om ett meddelande genom att expandera meddelandet.

> [!NOTE]
> Kontrollera att du har en lokal utveckling kluster som kör innan du kör programmet. Kolla in den [Kom igång med](service-fabric-get-started.md) information om hur du konfigurerar din lokala miljö.
> 
> 

![Visa diagnostik händelser i Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Nästa steg
[Felsöka Service Fabric-program i Visual Studio](service-fabric-debugging-your-application.md)

[Komma igång: Service Fabric Web API-tjänster med OWIN själva värd](service-fabric-reliable-services-communication-webapi.md)

[Mer information om tillförlitlig samlingar](service-fabric-reliable-services-reliable-collections.md)

[Distribuera ett program](service-fabric-deploy-remove-applications.md)

[Uppgradering av programmet](service-fabric-application-upgrade.md)

[För utvecklare för Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

