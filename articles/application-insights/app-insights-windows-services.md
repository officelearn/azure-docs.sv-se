---
title: Azure Application Insights för Windows Server och arbetsroller | Microsoft Docs
description: Lägg till Application Insights SDK till ditt ASP.NET-program manuellt för att analysera användning, tillgänglighet och prestanda.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: f828ebf9131be014d2ea572b3314b1aacd630d14
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050367"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Konfigurera Application Insights för .NET manuellt

Du kan konfigurera [Application Insights](app-insights-overview.md) för att övervaka en mängd olika program, programroller, komponenter eller mikrotjänster. Visual Studio erbjuder [enstegskonfiguration](../azure-monitor/app/asp-net.md) för webbappar och tjänster. För andra typer av .NET-program, som till exempel roller för serverdelar eller skrivbordsprogram, kan du konfigurera Application Insights manuellt.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Innan du börjar

Du behöver:

* En prenumeration på [Microsoft Azure](https://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](https://live.com).
* Visual Studio 2013 eller senare.

## <a name="add"></a>1. Välj en Application Insights-resurs

Resursen är platsen där dina data samlas in och visas i Azure Portal. Du måste bestämma om du vill skapa en ny eller dela en befintlig.

### <a name="part-of-a-larger-app-use-existing-resource"></a>En del av en större app: Använda befintlig resurs

Om din webbapp har flera komponenter, till exempel en klientwebbapp och en eller flera serverdelstjänster så bör du skicka telemetri från alla komponenter till samma resurs. Detta gör det möjligt för dem att visas på en enda programavbildning och gör det möjligt att spåra en begäran från en komponent till en annan.

Så om du redan övervakar andra komponenter i den här appen så är det bara att använda samma resurs.

Öppna resursen i [Azure Portal](https://portal.azure.com/). 

### <a name="self-contained-app-create-a-new-resource"></a>Fristående app: Skapa en ny resurs

Om den nya appen inte är relaterad till andra program bör den ha en egen resurs.

Logga in på [Azure-portalen](https://portal.azure.com/) och skapa en ny Application Insights-resurs. Välj ASP.NET som programtypen.

![Klicka på Nytt, Application Insights](./media/app-insights-windows-services/01-new-asp.png)

Valet av programtyp anger standardinnehållet i resursbladen.

## <a name="2-copy-the-instrumentation-key"></a>2. Kopiera instrumenteringsnyckeln
Nyckeln identifierar resursen. Snart installerar du den i SDK:n för att dirigera data till resursen.

![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C.](./media/app-insights-windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Installera Application Insights-paketet i ditt program
Hur du installerar och konfigurerar Application Insights-paketet varierar beroende på vilken plattform du arbetar på. 

1. I Visual Studio högerklickar du på projektet och väljer **Hantera Nuget-paket**.
   
    ![Högerklicka på projektet och välj Hantera Nuget-paket.](./media/app-insights-windows-services/03-nuget.png)
2. Installera Application Insights-paketet för Windows Server-appar, "Microsoft.ApplicationInsights.WindowsServer."
   
    ![Sök efter ”Application Insights”](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Vilken version?*

    Markera alternativet för att **ta med förhandsversionen** om du vill prova de senaste funktionerna. I de relevanta dokumenten och bloggarna finns information om huruvida du behöver en förhandsversion eller inte.
    
    *Kan jag använda andra paket?*
   
    Ja. Välj "Microsoft.Application Insights" om du vill använda API:et för att skicka din egen telemetri. Windows Server-paketet omfattar API:et plus ett antal andra paket som till exempel en samling med prestandaräknare och beroendeövervakning. 

### <a name="to-upgrade-to-future-package-versions"></a>Så här uppgraderar du till framtida paketversioner
Då och då ger vi ut en ny version av SDK.

Om du vill uppgradera till en [ny paketversion](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.WindowsServer** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.

## <a name="4-send-telemetry"></a>4. Skicka telemetri
**Om du endast har installerat API-paketet:**

* Ange instrumenteringsnyckeln i kod, till exempel i `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *din nyckel* `";` 
* [Skriv din egen telemetri med hjälp av API](../azure-monitor/app/api-custom-events-metrics.md#ikey).

**Om du har installerat andra Application Insights-paket** kan du, om du så önskar, använda .config-filen för att ange instrumenteringsnyckeln:

* Redigera ApplicationInsights.config (som lades till av installationsprogrammet för NuGet). Infoga detta precis före sluttaggen:
  
    `<InstrumentationKey>` *instrumenteringsnyckeln som du kopierade* `</InstrumentationKey>`
* Kontrollera att egenskaperna för ApplicationInsights.config i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.

Det är bra att ange instrumenteringsnyckeln i kod om du vill [byta nyckel för olika versionskonfigurationer](../azure-monitor/app/separate-resources.md). Om du anger nyckeln i koden behöver du inte ange den i `.config`-filen.

## <a name="run"></a> Köra projektet
Använd **F5** när du vill köra programmet och testa det: öppna olika sidor för att generera telemetri.

I Visual Studio ser du antalet händelser som har skickats.

![Antal händelser i Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Visa telemetrin
Gå tillbaka till [Azure-portalen](https://portal.azure.com/) och bläddra till Application Insights-resursen.

Titta efter data i översiktsdiagrammet. Först ser du bara en eller två punkter. Exempel:

![Klicka dig vidare till mer data](./media/app-insights-windows-services/12-first-perf.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden. [Lär dig mer om mätvärden.](../azure-monitor/app/web-monitor-performance.md)

### <a name="no-data"></a>Ser du inga data?
* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Öppna panelen [Sök](../azure-monitor/app/diagnostic-search.md) om du vill visa enskilda händelser. Ibland tar det lite längre tid för händelser att passera genom pipelinen för mätvärden.
* Vänta några sekunder och klicka på **Uppdatera**. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan uppdatera dem manuellt om du väntar på att vissa data ska visas.
* Mer information finns i [Felsökning](../azure-monitor/app/troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicera appen
Distribuera ditt program till din server eller Azure och se hur data ackumuleras.

![Använda Visual Studio för att publicera din app](./media/app-insights-windows-services/15-publish.png)

När du kör i felsökningsläge skickas telemetrin genom pipelinen och du bör se data inom några sekunder. När du distribuerar appen i en publiceringskonfiguration ackumuleras data långsammare.

### <a name="no-data-after-you-publish-to-your-server"></a>Visas inga data efter att du har publicerat till servern?
Öppna portar för utgående trafik i serverns brandvägg. På [den här sidan](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) kan du se listan med begärda adresser 

### <a name="trouble-on-your-build-server"></a>Har du problem på build-servern?
Mer information finns i [det här felsökningsavsnittet](../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Om din app genererar mycket telemetri minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är relaterade till samma begäran markeras eller avmarkeras dock som en grupp, så att du kan navigera mellan relaterade händelser. 
> [Lär dig mer om insamling](../azure-monitor/app/sampling.md).
> 
> 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Lägg till mer telemetri](../azure-monitor/app/asp-net-more.md)för att få en heltäckande bild av ditt program.

