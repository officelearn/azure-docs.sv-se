---
title: Ögonblicksbildfelsökare för Azure Application Insights för .NET-appar
description: Felsökningsögonblicksbilder samlas in automatiskt när undantag genereras i produktion .NET-appar
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275768"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ögonblicksbilder för felsökning av undantag i .NET-appar
När ett undantag inträffar kan du automatiskt samla in en felsökningsögonblicksbild från ditt live-webbprogram. Ögonblicksbilden visar tillståndet för källkod och variabler vid den tidpunkt då undantaget kastades. Felsökaren för ögonblicksbilder i [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) övervakar undantagstelemetri från din webbapp. Den samlar in ögonblicksbilder på dina toppkastande undantag så att du har den information du behöver för att diagnostisera problem i produktionen. Inkludera [Paketet Snapshot collector NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) i ditt program och konfigurera insamlingsparametrar i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Ögonblicksbilder visas på [undantag](../../azure-monitor/app/asp-net-exceptions.md) i application insights-portalen.

Du kan visa de här ögonblicksbilderna i portalen, se anropsstacken och inspektera variablerna på varje nivå av stacken. För att få en mer kraftfull felsökningsupplevelse med källkod öppnar du ögonblicksbilder med Visual Studio 2019 Enterprise. I Visual Studio kan du också [ange Snappoints för att interaktivt ta ögonblicksbilder](https://aka.ms/snappoint) utan att vänta på ett undantag.

Felsökningsögonblicksbilder lagras i 15 dagar. Den här bevarandeprincipen anges per program. Om du behöver öka det här värdet kan du begära en ökning genom att öppna ett supportärende i Azure-portalen.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Aktivera ögonblicksbildfelsökare för programinsikter för ditt program
Snapshot-samling är tillgänglig för:
* .NET Framework och ASP.NET program som kör .NET Framework 4.5 eller senare.
* .NET Core 2.0- och ASP.NET Core 2.0-program som körs på Windows.

Följande miljöer stöds:

* [Azure App-tjänst](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör OS-familjen 4 eller senare
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som körs på Windows Server 2012 R2 eller senare
* [Azure Virtual Machines och virtual machine scale set](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör Windows Server 2012 R2 eller senare
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör Windows Server 2012 R2 eller senare eller Windows 8.1 eller senare

> [!NOTE]
> Klientprogram (till exempel WPF, Windows Forms eller UWP) stöds inte.

Om du har aktiverat felsökaren för ögonblicksbilder men inte ser ögonblicksbilder kontrollerar du vår [felsökningsguide](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Bevilja behörigheter

Åtkomst till ögonblicksbilder skyddas av rollbaserad åtkomstkontroll (RBAC). Om du vill granska en ögonblicksbild måste du först läggas till i den nödvändiga rollen av en prenumerationsägare.

> [!NOTE]
> Ägare och bidragsgivare har inte automatiskt den här rollen. Om de vill visa ögonblicksbilder måste de lägga till sig i rollen.

Prenumerationsägare bör `Application Insights Snapshot Debugger` tilldela rollen till användare som ska inspektera ögonblicksbilder. Den här rollen kan tilldelas enskilda användare eller grupper av prenumerationsägare för mål application insights-resursen eller dess resursgrupp eller prenumeration.

1. Navigera till application insights-resursen i Azure-portalen.
1. Klicka på **Åtkomstkontroll (IAM)**.
1. Klicka på knappen **+Lägg till rolltilldelning.**
1. Välj **Ögonblicksbildfelsökare för programinsikter** i listrutan **Roller.**
1. Sök efter och ange ett namn som användaren kan lägga till.
1. Klicka på knappen **Spara** om du vill lägga till användaren i rollen.


> [!IMPORTANT]
> Ögonblicksbilder kan potentiellt innehålla personlig och annan känslig information i variabel- och parametervärden.

## <a name="view-snapshots-in-the-portal"></a>Visa ögonblicksbilder i portalen

När ett undantag har inträffat i ditt program och en ögonblicksbild har skapats, bör du ha ögonblicksbilder att visa. Det kan ta 5 till 10 minuter från ett undantag som inträffar till en ögonblicksbild som är klar och synlig från portalen. Om du vill visa ögonblicksbilder väljer du knappen **Operationer** i fönstret **Fel** när du visar fliken **Operationer** eller väljer knappen **Undantag** när du visar fliken **Undantag:**

![Sida om fel](./media/snapshot-debugger/failures-page.png)

Välj en åtgärd eller ett undantag i den högra rutan för att öppna fönstret **Heltäckande transaktionsinformation** och välj sedan undantagshändelsen. Om en ögonblicksbild är tillgänglig för det angivna undantaget visas en knapp för **öppen felsökningsögonblicksbild** i den högra rutan med information om [undantaget](../../azure-monitor/app/asp-net-exceptions.md).

![Knappen Öppna felsökningsögonblickssökning vid undantag](./media/snapshot-debugger/e2e-transaction-page.png)

I vyn Felsökningsögonblicksbild visas en anropsstack och en variabelruta. När du väljer ramar för anropsstacken i samtalsstapelfönstret kan du visa lokala variabler och parametrar för det funktionsanropet i variabelfönstret.

![Visa felsökningsögonblicksbild i portalen](./media/snapshot-debugger/open-snapshot-portal.png)

Ögonblicksbilder kan innehålla känslig information och som standard kan de inte visas. Om du vill visa ögonblicksbilder måste du ha rollen `Application Insights Snapshot Debugger` tilldelad till dig.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visa ögonblicksbilder i Visual Studio 2017 Enterprise eller senare
1. Klicka på knappen **Hämta** `.diagsession` ögonblicksbild om du vill hämta en fil som kan öppnas av Visual Studio Enterprise.

2. Om du `.diagsession` vill öppna filen måste du ha visual studio-komponenten Snapshot Debugger installerad. Komponenten Ögonblicksbildfelsökare är en obligatorisk komponent i ASP.net arbetsbelastningen i Visual Studio och kan väljas från listan Enskild komponent i Visual Studio-installationsprogrammet. Om du använder en version av Visual Studio före Visual Studio 2017 version 15.5 måste du installera tillägget från [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. När du har öppnat ögonblicksbildfilen visas sidan Minidump-felsökning i Visual Studio. Klicka på **Felsöka hanterad kod** om du vill börja felsöka ögonblicksbilden. Ögonblicksbilden öppnas till den kodrad där undantaget har kastats så att du kan felsöka processens aktuella tillstånd.

    ![Visa felsökningsögonblicksbild i Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Den hämtade ögonblicksbilden innehåller alla symbolfiler som hittades på webbprogramservern. Dessa symbolfiler krävs för att associera ögonblicksbilddata med källkoden. För App Service-appar måste du aktivera symboldistribution när du publicerar dina webbappar.

## <a name="how-snapshots-work"></a>Så här fungerar ögonblicksbilder

Snapshot Collector implementeras som en [programinsiktsmetryprocessor](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). När programmet körs läggs Videoinsamlare telemetriprocessorn till i programmets telemetripipeline.
Varje gång ditt program anropar [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions)beräknar Snapshot Collector ett problem-ID från den typ av undantag som genereras och kastmetoden.
Varje gång ditt program anropar TrackException ökas en räknare för rätt problem-ID. När räknaren når `ThresholdForSnapshotting` värdet läggs problem-ID:n till i en samlingsplan.

Snapshot Collector övervakar också undantag när de genereras genom att prenumerera på händelsen [AppDomain.CurrentDomain.FirstChanceException.](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) När den händelsen utlöses beräknas problem-ID:t för undantaget och jämförs med problem-ID:n i insamlingsplanen.
Om det finns en matchning skapas en ögonblicksbild av den pågående processen. Ögonblicksbilden tilldelas en unik identifierare och undantaget stämplas med den identifieraren. När FirstChanceException-hanteraren har returnerats bearbetas det inkastade undantaget som vanligt. Så småningom når undantaget TrackException-metoden igen där den, tillsammans med ögonblicksbildidentifieraren, rapporteras till Application Insights.

Huvudprocessen fortsätter att köra och betjäna trafik till användare med lite avbrott. Under tiden överlämnas ögonblicksbilden till processen för ögonblicksbilduppladdning. Snapshot Uploader skapar en minidump och laddar upp den till Application Insights tillsammans med alla relevanta symbolfiler (.pdb).

> [!TIP]
> - En ögonblicksbild av processen är en pausad klon av den pågående processen.
> - Det tar cirka 10 till 20 millisekunder att skapa ögonblicksbilden.
> - Standardvärdet för `ThresholdForSnapshotting` är 1. Detta är också det lägsta värdet. Därför måste appen utlösa samma undantag **två gånger** innan en ögonblicksbild skapas.
> - Ställ `IsEnabledInDeveloperMode` in på true om du vill generera ögonblicksbilder medan du felsöker i Visual Studio.
> - Skapandegraden för ögonblicksbilder begränsas av `SnapshotsPerTenMinutesLimit` inställningen. Som standard är gränsen en ögonblicksbild var tionde minut.
> - Högst 50 ögonblicksbilder per dag får laddas upp.

## <a name="limitations"></a>Begränsningar

Standardlagringsperioden för datalagring är 15 dagar. För varje Application Insights-instans tillåts ett maximalt antal 50 ögonblicksbilder per dag.

### <a name="publish-symbols"></a>Publicera symboler
Felsökaren för ögonblicksbild kräver symbolfiler på produktionsservern för att avkoda variabler och för att ge en felsökningsupplevelse i Visual Studio.
Version 15.2 (eller högre) av Visual Studio 2017 publicerar symboler för versionsversioner som standard när den publiceras i App Service. I tidigare versioner måste du lägga till följande `.pubxml` rad i publiceringsprofilfilen så att symbolerna publiceras i utgivningsläge:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

För Azure Compute och andra typer kontrollerar du att symbolfilerna finns i samma `wwwroot/bin`mapp i huvudprogrammet .dll (vanligtvis) eller är tillgängliga på den aktuella sökvägen.

> [!NOTE]
> Mer information om de olika symbolalternativen finns i [Visual Studio-dokumentationen](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). För bästa resultat rekommenderar vi att du använder "Full", "Portable" eller "Embedded".

### <a name="optimized-builds"></a>Optimerade byggen
I vissa fall kan lokala variabler inte visas i versionsversioner på grund av optimeringar som tillämpas av JIT-kompilatorn.
I Azure App Services kan dock Snapshot Collector deoptimize-avkastningsmetoder som ingår i samlingsplanen.

> [!TIP]
> Installera programstatistikplatstillägget i apptjänsten för att få stöd för deoptimisering.

## <a name="next-steps"></a>Nästa steg
Aktivera Snapshot-felsökning av programinsikter för ditt program:

* [Azure App-tjänst](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalningsuppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Utöver ögonblicksbildsökare för programinsikter:
 
* [Ange fästpunkter i koden](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) för att hämta ögonblicksbilder utan att vänta på ett undantag.
* Genom att [diagnostisera undantag i webbapparna](../../azure-monitor/app/asp-net-exceptions.md) kan du göra fler undantag synliga för Application Insights.
* [Smart identifiering](../../azure-monitor/app/proactive-diagnostics.md) identifierar automatiskt prestandaavvikelser.
