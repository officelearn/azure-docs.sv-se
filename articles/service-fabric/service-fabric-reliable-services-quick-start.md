---
title: Skapa ditt första Service Fabric-program i C# | Microsoft Docs
description: Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: eb0fd7e4feb28d60173b638a15dbce598f78e6bf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182960"
---
# <a name="get-started-with-reliable-services"></a>Kom igång med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Ett Azure Service Fabric-program innehåller en eller flera tjänster som körs din kod. Den här guiden visar hur du skapar både tillståndslösa och tillståndskänsliga Service Fabric-program med [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Grundläggande begrepp
Om du vill komma igång med Reliable Services, behöver du bara förstå några grundläggande begrepp:

* **Typ av tjänst**: Det här är din implementering. Den definieras av den klass som du skriver och som utökar `StatelessService` och eventuella andra koden och beroenden som används i, tillsammans med ett namn och ett versionsnummer.
* **Namngiven tjänstinstans**: Om du vill köra tjänsten skapar du namngivna instanser i din tjänsttyp mycket som du skapar objektinstanser av en klasstyp. En tjänstinstans har ett namn i form av en URI: N med hjälp av den ”fabric: /” system, som till exempel ”fabric: / MyApp/MyService”.
* **Tjänstevärden**: Namngiven tjänst-instanser som du skapar måste du köra inuti en värdprocess. Tjänstevärden är bara en process där instanser av din tjänst kan köras.
* **Registrering av tjänst**: Registrering samlar allt. Tjänsttypen måste vara registrerad med Service Fabric-körning i en tjänstevärd så att Service Fabric för att skapa instanser av den att köra.  

## <a name="create-a-stateless-service"></a>Skapa en tillståndslös tjänst
En tillståndslös tjänst är en typ av tjänst som för närvarande är normen i molnprogram. Betraktas den tillståndslösa eftersom själva tjänsten inte innehåller data som måste lagras på ett tillförlitligt sätt eller hög tillgänglighet. Om en instans av en tillståndslös tjänst avslutas, förloras alla det interna tillståndet. I den här typen av tjänsten måste tillstånd sparas till en extern lagring, till exempel Azure Tables eller en SQL-databas för att det ska göras med hög tillgänglighet och tillförlitlighet.

Starta Visual Studio 2015 eller Visual Studio 2017 som administratör och skapa en ny Service Fabric-programprojekt med namnet *HelloWorld*:

![Använd dialogrutan Nytt projekt för att skapa ett nytt Service Fabric-program](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Skapa sedan en tillståndslös tjänst-projekt med **.Net Core 2.0** med namnet *HelloWorldStateless*:

![Skapa en tillståndslös tjänst-projekt i den andra dialogrutan](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Din lösning innehåller nu två projekt:

* *HelloWorld*. Det här är den *programmet* projekt som innehåller din *services*. Den innehåller också applikationsmanifestet som beskriver programmet, samt ett antal PowerShell-skript som hjälper dig att distribuera ditt program.
* *HelloWorldStateless*. Det här är i tjänstens projekt. Den innehåller den tillståndslösa tjänst implementeringen.

## <a name="implement-the-service"></a>Implementera tjänsten
Öppna den **HelloWorldStateless.cs** filen i tjänstens projekt. En tjänst kan köra all affärslogik i Service Fabric. Tjänst-API: et tillhandahåller två ställen för din kod:

* En kunskapsuppsättning metoden, kallas *RunAsync*, där du kan börja köra alla arbetsbelastningar, inklusive tidskrävande beräkning av arbetsbelastningar.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* En kommunikation startpunkt där du kan ansluta din kommunikationsstack föredrar, till exempel ASP.NET Core. Det här är där du kan börja ta emot begäranden från användare och andra tjänster.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

I den här självstudiekursen fokuserar vi på den `RunAsync()` metoden. Det här är där du kan omedelbart börja köra din kod.
Projektmallen innehåller ett exempel på implementering av `RunAsync()` som ökar en löpande uppräkning.

> [!NOTE]
> Mer information om hur du arbetar med en kommunikationsstack för finns [webb-API för Service Fabric-tjänster med egen värd för OWIN](service-fabric-reliable-services-communication-webapi.md)
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

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Plattformen anropar den här metoden när en instans av en tjänst är placerade och redo att köra. För en tillståndslös tjänst som avses när tjänstinstansen öppnas. En annullering token har angetts för samordning när din tjänstinstans måste stängas. I Service Fabric, kan den här första/sista cykeln av en tjänstinstans inträffa flera gånger under livslängden för tjänsten som helhet. Detta kan inträffa av olika anledningar, inklusive:

* Systemet flyttar dina service-instanser för resurser.
* Fel uppstår i din kod.
* Programmets eller systemets uppgraderas.
* Den underliggande maskinvaran uppstår ett avbrott.

Denna orkestrering hanteras av systemet att hålla din tjänst med hög tillgänglighet och korrekt belastningsutjämnade.

`RunAsync()` bör inte blockera synkront. Implementeringen av RunAsync bör returnera en uppgift eller await på tidskrävande eller blockerar åtgärder att tillåta körning för att fortsätta. Observera att i den `while(true)` loop i föregående exempel, en uppgift returnerar `await Task.Delay()` används. Om din arbetsbelastning måste blockera synkront, bör du schemalägga en ny uppgift med `Task.Run()` i din `RunAsync` implementering.

Annullering av din arbetsbelastning är en samverkande ansträngning dirigeras genom den tillhandahållna annullering-token. Systemet ska vänta tills uppgiften att avsluta (efter slutförande, uppsägning eller fel) innan flyttas på. Det är viktigt att respektera annullering token, Slutför allt arbete och avsluta `RunAsync()` så snabbt som möjligt när systemet begär annullering.

I det här exemplet tillståndslös tjänst lagras antalet i en lokal variabel. Men eftersom detta är en tillståndslös tjänst kan det värde som lagras finns bara för den aktuella livscykeln för dess tjänstinstans. När tjänsten flyttar eller startar om kan förloras värdet.

## <a name="create-a-stateful-service"></a>Skapa en tillståndskänslig tjänst
Service Fabric introducerar en ny typ av tjänst som är tillståndskänslig. En tillståndskänslig tjänst kan underhålla tillståndet på ett tillförlitligt sätt inom tjänsten samordnad med kod som använder den. Tillstånd görs med hög tillgänglighet av Service Fabric utan att behöva bevara tillstånd till en extern lagring.

Om du vill konvertera ett värde för prestandaräknaren från tillståndslösa till hög tillgänglighet och beständig, även när tjänsten flyttar eller startar om, måste en tillståndskänslig tjänst.

I samma *HelloWorld* program, du kan lägga till en ny tjänst genom att högerklicka på tjänsterna referenser i programprojektet och välja **Lägg till ny Service Fabric-tjänst ->**.

![Lägga till en tjänst till ditt Service Fabric-program](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Välj **.Net Core 2.0 -> tillståndskänslig tjänst** och ge den namnet *HelloWorldStateful*. Klicka på **OK**.

![Använd dialogrutan Nytt projekt för att skapa en ny tillståndskänslig Service Fabric-tjänst](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Ditt program bör nu ha två tjänster: den tillståndslösa tjänsten *HelloWorldStateless* och den tillståndskänsliga tjänsten *HelloWorldStateful*.

En tillståndskänslig tjänst har samma startpunkter som en tillståndslös tjänst. Den största skillnaden är tillgängligheten för en *tillståndsprovider* som kan lagra tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en implementering av databasleverantör som tillståndet kallas [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), vilket låter dig skapa replikeras datastrukturer via Reliable State Manager. En tillståndskänslig tillförlitlig tjänst använder den här tillståndsprovider som standard.

Öppna **HelloWorldStateful.cs** i *HelloWorldStateful*, som innehåller följande RunAsync-metod:

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

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
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
`RunAsync()` fungerar på samma sätt i tillståndskänsliga och tillståndslösa tjänster. Men i en tillståndskänslig tjänst plattformen utför ytterligare arbete för din räkning innan den kan köras `RunAsync()`. Arbetet kan inkludera vilket säkerställer att Reliable State Manager och Reliable Collections är redo att använda.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och Reliable State Manager
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) är en ordlista-implementering som du kan använda för att på ett tillförlitligt sätt lagrar tillstånd i tjänsten. Du kan lagra data direkt i din tjänst utan att behöva en extern beständigt Arkiv med Service Fabric och tillförlitliga samlingar. Tillförlitliga samlingar gör dina data med hög tillgänglighet. Service Fabric åstadkommer detta genom att skapa och hantera flera *repliker* av din tjänst för dig. Det ger också ett API som avlägsnar komplex hantering av dessa repliker och ändrar deras status.

Tillförlitliga samlingar kan lagra alla .NET-typer, inklusive din anpassade typer med ett par varningar:

* Service Fabric gör din delstat med hög tillgänglighet av *replikera* tillstånd på noder och tillförlitliga samlingar lagra dina data till lokal disk på varje replik. Det innebär att allt som är lagrad i Reliable Collections måste vara *serialiserbara*. Som standard använder för tillförlitliga samlingar [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) för serialisering, så det är viktigt att se till att din typer är [stöds av Data kontrakt serialiserare](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) när du använder standard-serialiserare.
* Objekt replikeras för hög tillgänglighet när du genomför transaktioner på tillförlitliga samlingar. Objekt som lagras i Reliable Collections hålls i lokalt minne i din tjänst. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte mutera lokala instanser av dessa objekt utan att utföra en Uppdateringsåtgärd i tillförlitlig samling i en transaktion. Det beror på att ändringar i lokala instanser av objekt inte replikeras automatiskt. Du måste infoga objektet tillbaka till ordlistan igen eller Använd någon av de *uppdatera* metoder i ordlistan.

Reliable State Manager hanterar Reliable Collections åt dig. Du kan helt enkelt ställa Reliable State Manager för en tillförlitlig samling med namnet när som helst och var som helst i din tjänst. Reliable State Manager ser till att du får en referens tillbaka. Inte rekommenderar vi att du sparar referenser till tillförlitlig samling instanser i klassmedlem variabler eller egenskaper. Särskild försiktighet måste vidtas för att se till att referensen har angetts till en instans vid alla tidpunkter i livscykeln för tjänsten. Reliable State Manager hanterar arbetet åt dig och tjänsten är optimerad för upprepningar besök.

### <a name="transactional-and-asynchronous-operations"></a>Transaktions- och asynkrona åtgärder
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Tillförlitliga samlingar har många av åtgärderna som deras `System.Collections.Generic` och `System.Collections.Concurrent` motsvarigheter göra, förutom LINQ. Åtgärder för tillförlitliga samlingar är asynkrona. Det beror på att skrivåtgärder med Reliable Collections utföra i/o-åtgärder för att replikera och bevara data till disk.

Tillförlitlig samling åtgärder är *transaktionell*, så att du kan hålla tillstånd konsekvent över flera tillförlitliga samlingar och åtgärder. Du kan till exempel ta bort från kön ett arbetsobjekt från en tillförlitlig kö, utföra en åtgärd på den och spara resultatet i en tillförlitlig ordlista, allt inom en enda transaktion. Detta kommer att behandlas som en atomisk åtgärd och det garanterar att hela åtgärden lyckas eller hela åtgärden återställs. Om felet inträffar efter att du ta bort från kön objektet, men innan du sparar resultatet, återställs hela transaktionen och objektet som finns kvar i kön för bearbetning.

## <a name="run-the-application"></a>Köra programmet
Vi nu gå tillbaka till den *HelloWorld* program. Du kan nu skapa och distribuera dina tjänster. När du trycker på **F5**, ditt program ska byggas och distribueras till ditt lokala kluster.

När tjänsterna har startat kan du visa händelserna som genereras för Windows ETW (Event Tracing) i en **diagnostikhändelser** fönster. Observera att händelser visas från både den tillståndslösa tjänsten och den tillståndskänsliga tjänsten i programmet. Du kan pausa dataströmmen genom att klicka på den **pausa** knappen. Du kan granska information om ett meddelande genom att expandera meddelandet.

> [!NOTE]
> Kontrollera att du har ett lokalt utvecklingskluster som körs innan du kör programmet. Kolla in den [Kom igång med](service-fabric-get-started.md) information om hur du konfigurerar den lokala miljön.
> 
> 

![Visa diagnostiska händelser i Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Nästa steg
[Felsöka Service Fabric-programmet i Visual Studio](service-fabric-debugging-your-application.md)

[Kom igång: Webb-API för Service Fabric-tjänster med egen värd för OWIN](service-fabric-reliable-services-communication-webapi.md)

[Läs mer om Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Distribuera ett program](service-fabric-deploy-remove-applications.md)

[Programuppgradering](service-fabric-application-upgrade.md)

[Utvecklarreferens för Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

