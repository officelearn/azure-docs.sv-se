---
title: Profilproduktionsappar i Azure med Application Insights Profiler
description: Identifiera den aktiva sökvägen i webbserverkoden med en profiler med lågt fotavtryck.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671655"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilproduktionsprogram i Azure med Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Aktivera Application Insights Profiler för ditt program

Azure Application Insights Profiler tillhandahåller prestandaspårningar för program som körs i produktion i Azure. Profiler samlar in data automatiskt i stor skala utan att påverka användarna negativt. Profiler hjälper dig att identifiera den "heta" kodsökvägen som tar längst tid när den hanterar en viss webbbegäran. 

Profiler fungerar med .NET-program som distribueras på följande Azure-tjänster. Specifika instruktioner för att aktivera Profiler för varje tjänsttyp finns i länkarna nedan.

* [Azure App-tjänst](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalningsuppsättningar för virtuella datorer](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**FÖRHANDSGRANSKA** ASP.NET Azure Linux-webbappar för kärna](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Om du har aktiverat Profiler men inte ser spårningar kan du läsa vår [felsökningsguide](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Visa profilera data

För att Profiler ska kunna överföra spårningar måste ditt program aktivt hantera förfrågningar. Om du gör ett experiment kan du generera begäranden till webbappen med hjälp av [prestandatestning](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)av Application Insights . Om du nyligen har aktiverat Profiler kan du köra ett kort belastningstest. När belastningstestet körs väljer du knappen **Profil nu** i fönstret [ **Profilerinställningar** ](profiler-settings.md). När Profiler körs profilerar den slumpmässigt ungefär en gång per timme och under en varaktighet på två minuter. Om ditt program hanterar en stadig ström av begäranden laddar Profiler upp spårningar varje timme.

När programmet har tagit emot en del trafik och Profiler har huntats med att ladda upp spårningarna bör du ha spårningar att visa. Denna process kan ta 5 till 10 minuter. Om du vill visa spårningar väljer du **Vidta åtgärder**i **fönstret Prestanda** och väljer sedan knappen **Profilerspårningar.**

![Förhandsgranskning av profilspårningar i fönstret Application Insights][performance-blade]

Välj ett exempel om du vill visa en uppdelning på kodnivå av den tid som lagts ned på att utföra begäran.

![Programinsiktsspårningsutforskare][trace-explorer]

Spårningsutforskaren visar följande information:

* **Visa het bana:** Öppnar den största lövnoden, eller åtminstone något nära. I de flesta fall är den här noden nära en flaskhals i prestanda.
* **Etikett**: Namnet på funktionen eller händelsen. Trädet visar en blandning av kod och händelser som inträffade, till exempel SQL- och HTTP-händelser. Den översta händelsen representerar den totala begäranvaraktigheten.
* **Förflutit**: Tidsintervallet mellan operationens början och operationens.
* **När**: Den tid då funktionen eller händelsen kördes i förhållande till andra funktioner.

## <a name="how-to-read-performance-data"></a>Så här läser du prestandadata

Microsoft-tjänstprofileraren använder en kombination av samplingsmetoder och instrumentering för att analysera programmets prestanda. När detaljerad samling pågår tar serviceprofilerningsanvisningen för varje datorprocessor varje millisekund. Varje exempel fångar hela anropsstacken för tråden som körs för tillfället. Den ger detaljerad information om vad tråden gjorde, på både en hög nivå och en låg abstraktionsnivå. Serviceprofiler samlar också in andra händelser för att spåra aktivitetskorrelation och orsakssamband, inklusive sammanhangsbyteshändelser, TPL-händelser (Task Parallel Library) och trådpoolhändelser.

Anropsstacken som visas i tidslinjevyn är resultatet av sampling och instrumentering. Eftersom varje exempel fångar den fullständiga anropsstacken för tråden innehåller det kod från Microsoft .NET Framework och från andra ramverk som du refererar till.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Objektallokering (clr! JIT\_Nya eller clr! JIT\_Newarr1)

**Clr! JIT\_Nya** och **CLR! JIT\_Newarr1** är hjälpfunktioner i .NET Framework som allokerar minne från en hanterad heap. **clr! JIT\_Nytt** anropas när ett objekt allokeras. **clr! JIT\_Newarr1** anropas när en objektmatris allokeras. Dessa två funktioner är oftast snabba och tar relativt små mängder tid. Om **CLR! JIT\_Nya** eller **clr! JIT\_Newarr1** tar mycket tid i din tidslinje, kan koden allokera många objekt och konsumerar stora mängder minne.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Laddar kod (clr! DenPreStub)

**clr! ThePreStub** är en hjälpfunktion i .NET Framework som förbereder koden så att den körs för första gången. Den här körningen innehåller vanligtvis, men är inte begränsad till, jit-kompilering (just-in-time). För varje C# metod, **clr! ThePreStub** bör anropas högst en gång under en process.

Om **CLR! ThePreStub** tar lång tid för en begäran, begäran är den första att köra den metoden. Tiden för .NET Framework-körningen att läsa in den första metoden är betydande. Du kan överväga att använda en uppvärmningsprocess som kör den delen av koden innan användarna öppnar den, eller överväg att köra Native Image Generator (ngen.exe) på dina sammansättningar.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Lås påstående (clr! JITutil\_MonContention eller clr! JITutil\_MonEnterWorker)

**Clr! JITutil\_MonContention** eller **clr! JITutil\_MonEnterWorker** anger att den aktuella tråden väntar på att ett lås ska släppas. Den här texten visas ofta när du kör en C# **LOCK-sats,** anropar metoden **Monitor.Enter** eller anropar en metod med **attributet MethodImplOptions.Synchronized.** Låskonkurrens uppstår vanligtvis när tråd _A_ förvärvar ett lås och tråd _B_ försöker skaffa samma lås innan tråd _A_ släpper det.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Laddar kod ([COLD])

Om metodnamnet innehåller **[COLD]**, till exempel **mscorlib.ni![ COLD]System.Reflection.CustomAttribute.IsDefined**, .NET Framework runtime kör kod för första gången som inte optimeras av [profilstyrd optimering](/cpp/build/profile-guided-optimizations). För varje metod bör den visas högst en gång under processen.

Om inläsningskoden tar lång tid för en begäran är begäran den första som kör den ooptimerade delen av metoden. Överväg att använda en uppvärmningsprocess som kör den delen av koden innan användarna öppnar den.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Skicka HTTP-begäran

Metoder som **HttpClient.Send** anger att koden väntar på att en HTTP-begäran ska slutföras.

### <a name="database-operation"></a><a id="sqlcommand"></a>Databasåtgärd

Metoder som **SqlCommand.Execute** anger att koden väntar på att en databasåtgärd ska slutföras.

### <a name="waiting-await_time"></a><a id="await"></a>Väntar (VÄNTAR PÅ\_TID)

**VÄNTA\_TID** anger att koden väntar på att en annan aktivitet ska slutföras. Denna fördröjning händer vanligtvis med C # **AWAIT** uttalande. När koden gör en C# **AWAIT,** tråden avskakning och returnerar kontroll till trådpoolen, och det finns ingen tråd som är blockerad väntar **på att VÄNTA** att avsluta. Men logiskt sett är tråden som gjorde **AWAIT** "blockerad" och den väntar på att åtgärden ska slutföras. **Satsen\_VÄNTA TID** anger den blockerade tid som väntar på att aktiviteten ska slutföras.

### <a name="blocked-time"></a><a id="block"></a>Blockerad tid

**BLOCKED_TIME** anger att koden väntar på att en annan resurs ska vara tillgänglig. Den kanske till exempel väntar på att ett synkroniseringsobjekt ska vara tillgängligt eller att en begäran ska slutföras.

### <a name="unmanaged-async"></a>Ohanterat Async

.NET-ramverket avger ETW-händelser och skickar aktivitets-ID mellan trådar så att asynkronanrop kan spåras över trådar. Ohanterade kod (ursprunglig kod) och vissa äldre stilar av asynkron kod saknar dessa händelser och aktivitets-ID, så profileret kan inte säga vilken tråd och vilka funktioner som körs på tråden. Detta är märkt "Ohanterad Async" i anropsstacken. Om du laddar ner ETW-filen kanske du kan använda [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) för att få mer insikt i vad som händer.

### <a name="cpu-time"></a><a id="cpu"></a>CPU-tid

Processorn är upptagen med att köra instruktionerna.

### <a name="disk-time"></a><a id="disk"></a>Disktid

Programmet utför diskåtgärder.

### <a name="network-time"></a><a id="network"></a>Nätverkstid

Programmet utför nätverksåtgärder.

### <a name="when-column"></a><a id="when"></a>När kolumn

Kolumnen **När** är en visualisering av hur inklusiveexempel som samlas in för en nod varierar över tiden. Det totala intervallet för begäran är indelat i 32 tidshinkar. De inkluderande proverna för den noden ackumuleras i dessa 32 hinkar. Varje hink representeras som en stapel. Stapelns höjd representerar ett skalat värde. För noder som är markerade **CPU_TIME** eller **BLOCKED_TIME**, eller där det finns en uppenbar relation till att förbruka en resurs (till exempel en CPU, disk eller tråd), representerar stapeln förbrukningen av en av resurserna under bucketen. För dessa mått är det möjligt att få ett värde på mer än 100 procent genom att förbruka flera resurser. Om du till exempel använder två processorer under ett intervall får du 200 procent.

## <a name="limitations"></a>Begränsningar

Standardlagringsperioden för datalagring är fem dagar. Den maximala data som intas per dag är 10 GB.

Det finns inga avgifter för att använda profiler-tjänsten. För att du ska kunna använda den måste din webbapp finnas i åtminstone den grundläggande nivån för webbappfunktionen i Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Overhead- och samplingsalgoritm

Profiler körs slumpmässigt två minuter varje timme på varje virtuell dator som är värd för programmet som har Profiler aktiverat för att samla in spårningar. När Profiler körs läggs den till från 5 till 15 procent CPU-omkostnader till servern.

## <a name="next-steps"></a>Nästa steg
Aktivera Application Insights Profiler för ditt Azure-program. Se även:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalningsuppsättningar för virtuella datorer](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
