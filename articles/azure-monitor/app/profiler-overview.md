---
title: Profilera program i produktion i Azure med Application Insights Profiler | Microsoft Docs
description: Identifiera den heta sökvägen i serverkoden web med en låg fotavtryck profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: c07b325f3de6cd2cf3aaa436736786d2cdc42881
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498136"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilen produktionsprogram i Azure med Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Aktivera Application Insights Profiler för ditt program

Azure Application Insights Profiler tillhandahåller prestandaspårningar för program som körs i produktion i Azure. Profiler samlar in data automatiskt i stor skala utan att negativt påverka dina användare. Profiler kan du identifiera den ”heta” sökväg som tar längst tid när den hanterar en viss begäran. 

Profiler fungerar med .NET-program som distribueras på följande Azure-tjänster. Specifika anvisningar för att aktivera Profiler för varje typ av tjänst finns i länkarna nedan.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure virtuella datorer och VM-skalningsuppsättningar](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**FÖRHANDSGRANSKA** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Om du har aktiverat Profiler men inte ser spårningar, kontrollera våra [felsökningsguide för](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Visa Profiler data

För Profiler att ladda upp spårningar ditt program måste vara aktivt hantering av begäranden. Om du gör ett experiment, du kan generera begäranden till webbappen med hjälp av [Application Insights prestandatestning](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Om du har nyligen aktiverat Profiler, kan du köra ett kort belastningstest. När belastningstestet körs, Välj den **profil nu** knappen på den [ **Profiler inställningar** fönstret](profiler-settings.md#profiler-settings-pane). När Profiler körs Profileringen slumpmässigt ungefär en gång i timmen och under en period på två minuter. Om ditt program hanterar en stadig ström av begäranden, överför Profiler spårningar varje timme.

När ditt program tar emot en del trafik och Profiler har fått tid att ladda upp spårningarna, bör du ha spårningarna för att se. Den här processen kan ta 5 – 10 minuter. Visa spårningar, i den **prestanda** väljer **vidta åtgärder**, och välj sedan den **Profiler-spårningar** knappen.

![Förhandsversion av Application Insights prestanda fönstret Profiler-spårningar][performance-blade]

Välj ett exempel för att visa en kod på servernivå uppdelning av tid kör på begäran.

![Application Insights trace-Utforskaren][trace-explorer]

Spårningen explorer visar följande information:

* **Visa frekvent sökväg**: Öppnar det största löv noden eller Stäng minst något. I de flesta fall kan är den här noden nära en flaskhals för prestanda.
* **Etiketten**: Namnet på funktionen eller händelse. Trädet visar en blandning av kod och händelser som inträffat, till exempel SQL och HTTP-händelser. Översta händelsen representerar varaktighet för begäran.
* **Förfluten tid**: Tidsintervallet mellan början av åtgärden och den igen.
* **När**: Den tid när funktionen eller händelse kördes i förhållande till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestandadata

Profiler för Microsoft-tjänst använder en kombination av sampling metoder och instrumentation för att analysera prestanda för ditt program. När detaljerad samling pågår samplar service profiler instruktion pekare på varje dator CPU varje millisekund. Varje exempel samlar in fullständiga anropsstacken för tråden som körs för tillfället. Det ger detaljerad information om vad den tråden gjorde, på både hög och låg abstraktionsnivå. Tjänsten profileraren samlar även in andra händelser för att spåra aktivitet korrelation och orsakssamband, inklusive vilket sammanhang växlar händelser, uppgift parallella bibliotek (TPL) händelser och tråd pool händelser.

Anropsstacken som visas i tidslinjevyn är resultatet av sampling och instrumentering. Eftersom varje exempel samlar in fullständiga anropsstacken för tråden, innehåller koden från Microsoft .NET Framework och andra ramverk som du hänvisar till.

### <a id="jitnewobj"></a>Objektet allokering (clr! JIT\_New eller clr! JIT\_Newarr1)

**CLR! JIT\_New** och **clr! JIT\_Newarr1** är hjälpfunktioner i .NET Framework som en hanterad heap för att allokera minne. **CLR! JIT\_New** anropas när ett objekt har allokerats. **CLR! JIT\_Newarr1** anropas när en objektmatris som har allokerats. De här två funktionerna är vanligtvis snabba och ta relativt lite tid. Om **clr! JIT\_New** eller **clr! JIT\_Newarr1** tar mycket tid på din tidslinje koden kan allokera många objekt och förbrukar stora mängder minne.

### <a id="theprestub"></a>Läser in koden (clr! ThePreStub)

**CLR! ThePreStub** är en hjälpfunktionen i .NET Framework som förbereder koden som körs för första gången. Den här körningen vanligtvis inkluderar, men inte begränsat till, kompilering för just-in-time-(åtkomst JIT). För varje C# metoden **clr! ThePreStub** ska anropas en gång under en process.

Om **clr! ThePreStub** tar lång tid en begäran begäran är den första att köra metoden. Tiden för körningen av .NET Framework att läsa in den första metoden är betydande. Du kan använda en värma upp processen som kör den delen av koden innan dina användare åtkomst till den eller Överväg att köra Native Image Generator (ngen.exe) på dina sammansättningar.

### <a id="lockcontention"></a>Låskonkurrens (clr! JITutil\_MonContention eller clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** eller **clr! JITutil\_MonEnterWorker** anger att den aktuella tråden väntar en låsas upp. Den här texten visas ofta när du kör en C# **Lås** -instruktionen, anropa den **Monitor.Enter** metoden eller anropa en metod med den **MethodImplOptions.Synchronized**attribut. Låskonkurrens uppstår vanligen när tråden _A_ skaffar en lock och tråd _B_ försöker hämta samma Lås innan tråd _A_ släpper den.

### <a id="ngencold"></a>Läser in koden ([kall])

Om metodnamnet innehåller **[KALLA]**, till exempel **mscorlib.ni! [ COLD]system.Reflection.CustomAttribute.IsDefined**, körningen av .NET Framework kod körs för första gången som inte är optimerad med [profil guidad optimering](/cpp/build/profile-guided-optimizations). För varje metod ska det visas högst en gång under processen.

Om du läser in koden tar lång tid för en begäran, är den första att köra icke-optimerad delen av metoden i begäran. Överväg att använda en värma upp processen som kör den delen av koden innan dina användare åtkomst till den.

### <a id="httpclientsend"></a>Skicka HTTP-begäran

Metoder som **HttpClient.Send** tyda på att koden väntar en HTTP-begäran att slutföra.

### <a id="sqlcommand"></a>Databasåtgärden

Metoder som **SqlCommand.Execute** tyda på att koden väntar en databasåtgärd ska slutföras.

### <a id="await"></a>Väntar på (AWAIT\_tid)

**AWAIT\_tid** anger att koden är att en annan aktivitet ska slutföras. Den här fördröjningen händer vanligtvis med den C# **AWAIT** instruktionen. När koden har en C# **AWAIT**tråden unwinds och returnerar kontrollen till trådpoolen och det finns ingen tråd som har blockerats väntar på den **AWAIT** ska slutföras. Dock logiskt tråden som gjorde den **AWAIT** är ”blockerad” och det väntar på att åtgärden slutförs. Den **AWAIT\_tid** instruktionen anger blockerade tiden att vänta tills åtgärden har slutförts.

### <a id="block"></a>Blockerade tid

**BLOCKED_TIME** anger att koden väntar på en annan resurs ska vara tillgängliga. Det kan till exempel vänta för ett synkroniseringsobjekt, för en tråd ska vara tillgängliga eller för att slutföra en begäran.

### <a name="unmanaged-async"></a>Ohanterad asynkron

.NET framework genererar ETW-händelser och skickar ett aktivitets-ID mellan trådar så att asynkrona anrop kan spåras i trådar. Ohanterad kod (intern kod) och vissa äldre format asynkron kod saknar dessa händelser och ett aktivitets-ID, så profiler inte kan avgöra vilken tråd och funktioner som körs i tråden. Detta är märkt ”ohanterade Async' i anropsstacken. Om du har hämtat ETW-fil kan du använda [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) att få överblick över vad som händer.

### <a id="cpu"></a>CPU-tid

Processorn är upptagen med att köra instruktionerna.

### <a id="disk"></a>Disktid i procent

Programmet utför diskåtgärder.

### <a id="network"></a>Nätverkstid

Programmet utför nätverksåtgärder.

### <a id="when"></a>När kolumnen

Den **när** kolumnen är en visualisering av hur INKLUDERANDE exemplen som samlats in för en nod kan variera över tid. Det totala intervallet för begäran är uppdelad i 32 tid buckets. Inkluderande exemplen för noden ackumuleras i dessa 32 buckets. Varje bucket representeras som en stapel. Höjden på fältet representerar ett skalade värde. För noder som är markerade **CPU_TIME** eller **BLOCKED_TIME**, eller där det finns en uppenbar relation som förbrukar en resurs (till exempel CPU, disk eller tråd), i fältet representerar användningen av en av resurser under en bucket. Det är möjligt att hämta ett värde är större än 100 procent av det förbrukande flera resurser för de här måtten. Om du använder, i genomsnitt två processorer under ett intervall får du till exempel 200 procent.

## <a name="limitations"></a>Begränsningar

Standardkvarhållningsperioden för data är fem dagar. Den maximala data som matas in per dag är 10 GB.

Det kostar inget att använda Profiler-tjänsten. Om du vill använda den måste din webbapp måste vara finns i basic-nivån av funktionen Web Apps i Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Omkostnader och samplingsalgoritmen

Profiler körs slumpmässigt två minuter varje timme på varje virtuell dator som är värd för programmet som har aktiverat för att samla in spårningar Profiler. När Profiler körs läggs från 5 till 15 procent processoråtgång till servern.

## <a name="next-steps"></a>Nästa steg
Aktivera Application Insights Profiler för dina Azure-program. Se även:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure virtuella datorer och VM-skalningsuppsättningar](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
