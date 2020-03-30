---
title: 'Skapa ditt första Service Fabric-program i C #'
description: Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083761"
---
# <a name="get-started-with-reliable-services"></a>Kom igång med Reliable Services

> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)

Ett Azure Service Fabric-program innehåller en eller flera tjänster som kör din kod. Den här guiden visar hur du skapar både tillståndslösa och tillståndskänsliga Service Fabric-program med [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Grundläggande begrepp

För att komma igång med Reliable Services behöver du bara förstå några grundläggande begrepp:

* **Tjänsttyp**: Det här är din tjänstimplementering. Den definieras av den klass du `StatelessService` skriver som utökar och alla andra kod eller beroenden som används där, tillsammans med ett namn och ett versionsnummer.
* **Namngiven tjänstinstans:** Om du vill köra tjänsten skapar du namngivna instanser av tjänsttypen, ungefär som du skapar objektinstanser av en klasstyp. En tjänstinstans har ett namn i form av en URI med hjälp av "tyg:/" "fabric:/MyApp/MyService".
* **Tjänstvärd**: De namngivna tjänstinstanser som du skapar måste köras i en värdprocess. Tjänstvärden är bara en process där instanser av tjänsten kan köras.
* **Serviceregistrering**: Registrering samlar allt. Tjänsttypen måste vara registrerad med Service Fabric-körningen i en tjänstvärd för att Service Fabric ska kunna skapa instanser av den för att köras.  

## <a name="create-a-stateless-service"></a>Skapa en tillståndslös tjänst

En tillståndslös tjänst är en typ av tjänst som för närvarande är normen i molnprogram. Det anses tillståndslöst eftersom själva tjänsten inte innehåller data som behöver lagras på ett tillförlitligt sätt eller göras mycket tillgängliga. Om en instans av en tillståndslös tjänst stängs av går alla dess interna tillstånd förlorade. I den här typen av tjänst måste tillståndet sparas i ett externt arkiv, till exempel Azure-tabeller eller en SQL-databas, för att den ska göras tillgänglig och tillförlitlig.

Starta Visual Studio 2017 eller Visual Studio 2019 som administratör och skapa ett nytt programprojekt för Service Fabric med namnet *HelloWorld:*

![Använda dialogrutan Nytt projekt för att skapa ett nytt Service Fabric-program](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Skapa sedan ett tillståndslöst serviceprojekt med **.NET Core 2.0** med namnet *HelloWorldStateless:*

![Skapa ett tillståndslöst serviceprojekt i den andra dialogrutan](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Din lösning innehåller nu två projekt:

* *HejWorld*. Det här är *programprojektet* som innehåller dina *tjänster*. Den innehåller också programmanifestet som beskriver programmet, samt ett antal PowerShell-skript som hjälper dig att distribuera ditt program.
* *HelloWorldStateless*. Det här är serviceprojektet. Den innehåller den tillståndslösa tjänsteimplementeringen.

## <a name="implement-the-service"></a>Implementera tjänsten

Öppna **HelloWorldStateless.cs** filen i serviceprojektet. I Service Fabric kan en tjänst köra vilken affärslogik som helst. Tjänst-API:et innehåller två startpunkter för koden:

* En öppen startpunktsmetod, kallad *RunAsync*, där du kan börja köra alla arbetsbelastningar, inklusive tidskrävande beräkningsarbetsbelastningar.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* En kommunikationsinträde där du kan koppla in din kommunikationsstack, till exempel ASP.NET Core. Här kan du börja ta emot förfrågningar från användare och andra tjänster.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

I den här självstudien `RunAsync()` kommer vi att fokusera på ingångsmetoden. Här kan du omedelbart börja köra koden.
Projektmallen innehåller ett `RunAsync()` exempel på implementering av den ökar antalet rullande.

> [!NOTE]
> Mer information om hur du arbetar med en kommunikationsstack finns i [Servicekommunikation med ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync (KörAsync)

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

Plattformen anropar den här metoden när en instans av en tjänst är placerad och redo att köras. För en tillståndslös tjänst betyder det helt enkelt när tjänstinstansen öppnas. En avbokningstoken tillhandahålls för att samordna när din tjänstinstans måste stängas. I Service Fabric kan den här öppna/nära cykeln för en tjänstinstans inträffa många gånger under tjänstens livstid som helhet. Detta kan hända av olika skäl, inklusive:

* Systemet flyttar dina tjänstinstanser för resursbalansering.
* Fel uppstår i koden.
* Programmet eller systemet uppgraderas.
* Den underliggande maskinvaran upplever ett avbrott.

Denna orkestrering hanteras av systemet för att hålla din tjänst mycket tillgänglig och korrekt balanserad.

`RunAsync()`bör inte blockera synkront. Implementeringen av RunAsync bör returnera en aktivitet eller vänta på långvariga eller blockerande åtgärder så att körningen kan fortsätta. Observera i `while(true)` loopen i föregående exempel används `await Task.Delay()` en aktivitetsretur. Om din arbetsbelastning måste blockeras synkront bör `Task.Run()` du `RunAsync` schemalägga en ny aktivitet med i implementeringen.

Annullering av din arbetsbelastning är ett samarbete som iscensatts av den angivna annulleringstoken. Systemet väntar på att aktiviteten ska avslutas (genom att slutföra, avbryta eller fel) innan den går vidare. Det är viktigt att du respekterar annulleringstoken, slutför allt arbete och avslutar `RunAsync()` så snabbt som möjligt när systemet begär annullering.

I det här tillståndslösa serviceexemplet lagras antalet i en lokal variabel. Men eftersom detta är en tillståndslös tjänst finns det värde som lagras endast för den aktuella livscykeln för tjänstinstansen. När tjänsten flyttas eller startas om går värdet förlorat.

## <a name="create-a-stateful-service"></a>Skapa en tillståndskänslig tjänst

Service Fabric introducerar en ny typ av tjänst som är tillståndskänslig. En tillståndskänslig tjänst kan underhålla tillståndet på ett tillförlitligt sätt inom själva tjänsten, samlokaliserade med koden som använder den. Tillståndet görs mycket tillgängligt av Service Fabric utan att behöva spara tillstånd till ett externt arkiv.

Om du vill konvertera ett räknarvärde från tillståndsfritt till högtillgängligt och beständigt, även när tjänsten flyttas eller startas om, behöver du en tillståndskänslig tjänst.

I samma *HelloWorld-program* kan du lägga till en ny tjänst genom att högerklicka på tjänsternas referenser i programprojektet och välja **Lägg till -> New Service Fabric Service**.

![Lägga till en tjänst i ditt Service Fabric-program](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Välj **.NET Core 2.0 -> Stateful Service** och namnge den *HelloWorldStateful*. Klicka på **OK**.

![Använd dialogrutan Nytt projekt för att skapa en ny tjänst för serviceinfrastruktur](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Din ansökan bör nu ha två tjänster: den statslösa tjänsten *HelloWorldStateless* och den tillståndskänsliga tjänsten *HelloWorldStateful*.

En tillståndskänslig tjänst har samma startpunkter som en tillståndslös tjänst. Den största skillnaden är tillgängligheten för en *tillståndsprovider* som kan lagra tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en tillståndsprovider implementering som kallas [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md), som låter dig skapa replikerade datastrukturer via Reliable State Manager. En tillståndskänslig tillförlitlig tjänst använder den här tillståndsprovidern som standard.

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

### <a name="runasync"></a>RunAsync (KörAsync)

`RunAsync()`fungerar på samma sätt i statskänsliga och statslösa tjänster. Men i en tillståndskänslig tjänst utför plattformen ytterligare arbete för `RunAsync()`din räkning innan den körs. Det här arbetet kan omfatta att se till att Reliable State Manager och tillförlitliga samlingar är redo att användas.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och den pålitliga statschefen

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) är en ordlisteimplementering som du kan använda för att lagra tillståndet i tjänsten på ett tillförlitligt sätt. Med Service Fabric och Reliable Collections kan du lagra data direkt i din tjänst utan att behöva ett externt beständigt arkiv. Tillförlitliga samlingar gör dina data mycket tillgängliga. Service Fabric åstadkommer detta genom att skapa och hantera flera *repliker* av din tjänst åt dig. Det ger också ett API som abstraherar bort komplexiteten i att hantera dessa repliker och deras tillståndsövergångar.

Tillförlitliga samlingar kan lagra alla .NET-typer, inklusive dina anpassade typer, med ett par varningar:

* Service Fabric gör ditt tillstånd mycket tillgängligt genom *att replikera* tillstånd över noder och tillförlitliga samlingar lagrar dina data till lokal disk på varje replik. Detta innebär att allt som lagras i tillförlitliga samlingar måste vara *serialisable*. Som standard använder tillförlitliga samlingar [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) för serialisering, så det är viktigt att se till att dina typer [stöds av DataKontrakt Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) när du använder standardserieizern.
* Objekt replikeras för hög tillgänglighet när du genomför transaktioner på tillförlitliga samlingar. Objekt som lagras i tillförlitliga samlingar lagras i det lokala minnet i din tjänst. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte muterar lokala instanser av dessa objekt utan att utföra en uppdateringsåtgärd på den tillförlitliga samlingen i en transaktion. Detta beror på att ändringar i lokala instanser av objekt inte replikeras automatiskt. Du måste återinsätta objektet i ordlistan igen eller använda någon av *uppdateringsmetoderna* i ordlistan.

Reliable State Manager hanterar tillförlitliga samlingar åt dig. Du kan helt enkelt be Reliable State Manager om en tillförlitlig samling vid namn när som helst och var som helst i din tjänst. Reliable State Manager ser till att du får en referens tillbaka. Vi rekommenderar inte att du sparar referenser till tillförlitliga samlingsinstanser i klassmedlemsvariabler eller egenskaper. Särskild försiktighet måste iakttas för att säkerställa att referensen alltid är inställd på en instans under tjänstens livscykel. Reliable State Manager hanterar det här arbetet åt dig och är optimerad för upprepade besök.

### <a name="transactional-and-asynchronous-operations"></a>Transaktionella och asynkrona åtgärder

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Tillförlitliga samlingar har många av `System.Collections.Generic` samma `System.Collections.Concurrent` åtgärder som deras och motsvarigheter gör, med undantag för Language Integrated Query (LINQ). Åtgärder på tillförlitliga samlingar är asynkrona. Detta beror på att skrivåtgärder med tillförlitliga samlingar utför I/O-åtgärder för att replikera och bevara data till disken.

Tillförlitliga inkassoåtgärder är *transaktionella*, så att du kan hålla tillståndet konsekvent över flera tillförlitliga samlingar och åtgärder. Du kan till exempel dequeue ett arbetsobjekt från en tillförlitlig kö, utföra en åtgärd på den och spara resultatet i en tillförlitlig ordlista, allt inom en enda transaktion. Detta behandlas som en atomoperation och garanterar att antingen hela operationen lyckas eller att hela operationen återställs. Om ett fel uppstår efter att du har dequeue objektet men innan du sparar resultatet, hela transaktionen återställs och objektet finns kvar i kön för bearbetning.

## <a name="run-the-application"></a>Köra appen
Vi återvänder nu till *HelloWorld* ansökan. Du kan nu skapa och distribuera dina tjänster. När du trycker på **F5**skapas programmet och distribueras till det lokala klustret.

När tjänsterna har börjat köras kan du visa de genererade ETW-händelserna (Event Tracing for Windows) i ett **fönster för diagnostikhändelser.** Observera att de händelser som visas kommer från både den tillståndslösa tjänsten och den tillståndskänsliga tjänsten i programmet. Du kan pausa strömmen genom att klicka på **pausknappen.** Du kan sedan granska information om ett meddelande genom att expandera det meddelandet.

> [!NOTE]
> Innan du kör programmet kontrollerar du att du har ett lokalt utvecklingskluster som körs. I komma [igång-guiden](service-fabric-get-started.md) finns information om hur du konfigurerar din lokala miljö.
> 
> 

![Visa diagnostikhändelser i Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Nästa steg
[Felsöka ditt Service Fabric-program i Visual Studio](service-fabric-debugging-your-application.md)

[Kom igång: Api-tjänster för Service Fabric Web API med OWIN-självhosting](service-fabric-reliable-services-communication-webapi.md)

[Läs mer om tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)

[Distribuera ett program](service-fabric-deploy-remove-applications.md)

[Uppgradering av programmet](service-fabric-application-upgrade.md)

[Utvecklarreferens för reliable services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

