---
title: Profilera program i produktion i Azure med Application Insights Profiler | Microsoft Docs
description: Identifiera den heta sökvägen i serverkoden web med en låg fotavtryck profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 7780c10233a0ce256ee6e9015f40ea789516c25b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726907"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilen produktionsprogram i Azure med Application Insights
## <a name="enable-profiler-for-your-application"></a>Aktivera Profiler för ditt program

Application Insights Profiler innehåller prestandaspårningar för program som körs i produktion i Azure. Den samlar in data automatiskt i stor skala utan att negativt påverka dina slutanvändare. Profiler kan du identifiera den ”heta” sökväg som tar längst tid när hantering av en viss webbplats begär. 

Profiler fungerar med .net-program som distribuerats på följande Azure-tjänster. Specifika anvisningar för att aktivera profiler för varje typ av tjänst finns i länkarna nedan.

* [App Services](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-program](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuella datorer och VM-Scalesets](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Om du har aktiverat Profiler, men inte ser spårningar, kontrollera våra [Troubleshooting Guide.](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

Kör Profiler lokalt officiellt stöds inte, men vi har några [instruktioner som du kan prova.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>Visa profiler data

För profiler om du vill ladda upp spårningar, begär aktivt hantera programmet. Om du gör ett experiment, du kan generera begäranden till web app med [prestandatestning för Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Om du har nyligen aktiverat Profiler, kan du köra ett kort belastningstest. När belastningstestet körs, trycker du på den **profil nu** knappen i den [ **Profiler inställningssidan**](app-insights-profiler-settings.md#profiler-settings-page). När profiler körs ska den profilera slumpmässigt om en gång i timmen och under en period på två minuter. Om ditt program hanterar en stadig ström av begäranden, Profiler kommer att överföra spårningar varje timme.

När ditt program tar emot en del trafik och profiler har fått tid att ladda upp trances, bör du ha spårningarna för att se. Den här processen kan ta 5 – 10 minuter. Om du vill visa spår, går du till den **prestanda** väljer **vidta åtgärder** att visa profiler-spårningar och välj sedan den **Profiler-spårningar** knappen.

![Förhandsversion av Application Insights prestanda fönstret Profiler-spårningar][performance-blade]

Välj ett exempel för att visa en kod på servernivå uppdelning av tid kör på begäran.

![Application Insights trace-Utforskaren][trace-explorer]

Spårningen explorer visar följande information:

* **Visa frekvent sökväg**: öppnas det största löv noden eller Stäng minst något. I de flesta fall kan är den här noden nära en flaskhals för prestanda.
* **Etiketten**: namnet på funktionen eller händelse. Trädet visar en blandning av kod och händelser som inträffade (till exempel SQL- och HTTP-händelser). Översta händelsen representerar varaktighet för begäran.
* **Förfluten tid**: tidsintervallet mellan början av åtgärden och den igen.
* **När**: den tid när funktionen eller händelse kördes i förhållande till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestandadata

Profiler för Microsoft-tjänst använder en kombination av sampling metoder och instrumentation för att analysera prestanda för ditt program. När detaljerad samling pågår samplar service profiler instruktion pekare på varje dator CPU varje millisekund. Varje exempel samlar in fullständiga anropsstacken för tråden som körs för tillfället. Det ger detaljerad information om vad den tråden gjorde, både på hög nivå och på en låg nivå Abstraktionslager. Tjänsten profileraren samlar även in andra händelser för att spåra aktivitet korrelation och orsakssamband, inklusive vilket sammanhang växlar händelser, uppgift parallella bibliotek (TPL) händelser och tråd pool händelser.

Anropsstacken som visas i tidslinjevyn är resultatet av sampling och instrumentering. Eftersom varje exempel samlar in fullständiga anropsstacken för tråden, innehåller koden från Microsoft .NET Framework och andra ramverk som du hänvisar till.

### <a id="jitnewobj"></a>Objektet allokering (clr! JIT\_New eller clr! JIT\_Newarr1)

**CLR! JIT\_New** och **clr! JIT\_Newarr1** är hjälpfunktioner i .NET Framework som en hanterad heap för att allokera minne. **CLR! JIT\_New** anropas när ett objekt har allokerats. **CLR! JIT\_Newarr1** anropas när en objektmatris som har allokerats. De här två funktionerna är vanligtvis snabba och ta relativt lite tid. Om du ser **clr! JIT\_New** eller **clr! JIT\_Newarr1** ta ganska lång tid din tidslinje, betyder det att koden kan allokera många objekt och förbrukar stora mängder minne.

### <a id="theprestub"></a>Läser in koden (clr! ThePreStub)

**CLR! ThePreStub** är en hjälpfunktionen i .NET Framework som förbereder koden som körs för första gången. Detta vanligtvis inkluderar, men inte begränsat till, just-in-time (JIT) kompilering. För varje C#-metoden **clr! ThePreStub** ska anropas en gång under livslängden för en process.

Om **clr! ThePreStub** tar mycket tid för en begäran, tyder detta på begäran är det första som kör den metoden. Tiden för körningen av .NET Framework att läsa in den första metoden är betydande. Du kan använda en värma upp processen som kör den delen av koden innan dina användare åtkomst till den eller Överväg att köra Native Image Generator (ngen.exe) på dina sammansättningar.

### <a id="lockcontention"></a>Låskonkurrens (clr! JITutil\_MonContention eller clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** eller **clr! JITutil\_MonEnterWorker** anger att den aktuella tråden väntar en låsas upp. Texten visas ofta när du kör ett C# **Lås** -instruktionen, vid den **Monitor.Enter** metod, eller när du startar en metod med det **MethodImplOptions.Synchronized** attribut. Låskonkurrens uppstår vanligen när tråden _A_ skaffar en lock och tråd _B_ försöker hämta samma Lås innan tråd _A_ släpper den.

### <a id="ngencold"></a>Läser in koden ([kall])

Om metodnamnet innehåller **[KALLA]**, till exempel **mscorlib.ni! [ COLD]system.Reflection.CustomAttribute.IsDefined**, .NET Framework-körningen kör kod för första gången som inte har optimerats av <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil guidad optimering</a>. För varje metod ska det visas högst en gång under processen livslängd.

Om du läser in koden tar lång tid för en begäran, indikerar det att begäran är den första mallen för att köra icke-optimerad delen av metoden. Överväg att använda en värma upp processen som kör den delen av koden innan dina användare åtkomst till den.

### <a id="httpclientsend"></a>Skicka HTTP-begäran

Metoder som **HttpClient.Send** tyda på att koden väntar en HTTP-begäran att slutföra.

### <a id="sqlcommand"></a>Databasåtgärden

Metoder som **SqlCommand.Execute** tyda på att koden väntar en databasåtgärd ska slutföras.

### <a id="await"></a>Väntar på (AWAIT\_tid)

**AWAIT\_tid** anger att koden är att en annan aktivitet ska slutföras. Detta inträffar vanligen med C# **AWAIT** instruktionen. När koden har en C# **AWAIT**tråden unwinds och returnerar kontrollen till trådpoolen och det finns ingen tråd som har blockerats väntar på den **AWAIT** ska slutföras. Dock logiskt tråden som gjorde den **AWAIT** är ”blockerad” och väntar på att åtgärden slutförs. Den **AWAIT\_tid** instruktionen anger blockerade tiden att vänta tills åtgärden har slutförts.

### <a id="block"></a>Blockerade tid

**BLOCKED_TIME** anger att koden väntar på en annan resurs ska vara tillgängliga. Det kan till exempel vänta för ett synkroniseringsobjekt, för en tråd ska vara tillgängliga eller för att slutföra en begäran.

### <a id="cpu"></a>CPU-tid

Processorn är upptagen med att köra instruktionerna.

### <a id="disk"></a>Disktid i procent

Programmet utför diskåtgärder.

### <a id="network"></a>Nätverkstid

Programmet utför nätverksåtgärder.

### <a id="when"></a>När kolumnen

Den **när** kolumnen är en visualisering av hur INKLUDERANDE exemplen som samlats in för en nod kan variera över tid. Det totala intervallet för begäran är uppdelad i 32 tid buckets. Inkluderande exemplen för noden ackumuleras i dessa 32 buckets. Varje bucket representeras som en stapel. Höjden på fältet representerar ett skalade värde. För noder som är markerade **CPU_TIME** eller **BLOCKED_TIME**, eller där det finns en uppenbar relation som förbrukar en resurs (till exempel CPU, disk eller tråd), i fältet representerar användningen av en av resurser under perioden för bucket. Det är möjligt att hämta ett värde är större än 100 procent av det förbrukande flera resurser för de här måtten. Om du använder, i genomsnitt två processorer under ett intervall får du till exempel 200 procent.

## <a name="limitations"></a>Begränsningar

Standardkvarhållningsperioden för data är fem dagar. Den maximala data som matas in per dag är 10 GB.

Det kostar inget att använda Profiler-tjänsten. Om du vill använda tjänsten Profiler måste din webbapp måste vara finns i Basic-nivån av Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Omkostnader och samplingsalgoritmen

Profiler körs slumpmässigt två minuter varje timme på varje virtuell dator som är värd för programmet som har aktiverat för att samla in spårningar Profiler. När Profiler körs läggs från 5 procent till 15 procent processoråtgång till servern.

## <a name="next-steps"></a>Nästa steg
Aktivera Application Insights Profiler för dina Azure-program
* [App Services](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-program](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuella datorer och VM-Scalesets](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
