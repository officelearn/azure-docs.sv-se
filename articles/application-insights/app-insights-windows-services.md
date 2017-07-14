---
title: "Azure Application Insights för Windows Server och arbetsroller | Microsoft Docs"
description: "Lägg till Application Insights SDK till ditt ASP.NET-program manuellt för att analysera användning, tillgänglighet och prestanda."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: b18e20ac7742f499cfe4b84a2869077979e354d8
ms.contentlocale: sv-se
ms.lasthandoff: 05/19/2017


---
# Konfigurera Application Insights för .NET manuellt
<a id="manually-configure-application-insights-for-net-applications" class="xliff"></a>

Du kan konfigurera [Application Insights](app-insights-overview.md) för att övervaka en mängd olika program, programroller, komponenter eller mikrotjänster. Visual Studio erbjuder [enstegskonfiguration](app-insights-asp-net.md) för webbappar och tjänster. För andra typer av .NET-program, som till exempel roller för serverdelar eller skrivbordsprogram, kan du konfigurera Application Insights manuellt.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-windows-services/10-perf.png)

#### Innan du börjar
<a id="before-you-start" class="xliff"></a>

Du behöver:

* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).
* Visual Studio 2013 eller senare.

## <a name="add"></a>1. Välj en Application Insights-resurs

Resursen är platsen där dina data samlas in och visas i Azure Portal. Du måste bestämma om du vill skapa en ny eller dela en befintlig.

### En del av en större app: Använd en befintlig resurs
<a id="part-of-a-larger-app-use-existing-resource" class="xliff"></a>

Om din webbapp har flera komponenter, till exempel en klientwebbapp och en eller flera serverdelstjänster så bör du skicka telemetri från alla komponenter till samma resurs. Detta gör det möjligt för dem att visas på en enda programavbildning och gör det möjligt att spåra en begäran från en komponent till en annan.

Så om du redan övervakar andra komponenter i den här appen så är det bara att använda samma resurs.

Öppna resursen i [Azure Portal](https://portal.azure.com/). 

### Fristående app: Skapa en ny resurs
<a id="self-contained-app-create-a-new-resource" class="xliff"></a>

Om den nya appen inte är relaterad till andra program bör den ha en egen resurs.

Logga in på [Azure-portalen](https://portal.azure.com/) och skapa en ny Application Insights-resurs. Välj ASP.NET som programtypen.

![Klicka på Nytt, Application Insights](./media/app-insights-windows-services/01-new-asp.png)

Valet av programtyp anger standardinnehållet i resursbladen.

## 2. Kopiera instrumenteringsnyckeln
<a id="2-copy-the-instrumentation-key" class="xliff"></a>
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

### Så här uppgraderar du till framtida paketversioner
<a id="to-upgrade-to-future-package-versions" class="xliff"></a>
Då och då ger vi ut en ny version av SDK.

Om du vill uppgradera till en [ny paketversion](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.WindowsServer** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.

## 4. Skicka telemetri
<a id="4-send-telemetry" class="xliff"></a>
**Om du endast har installerat API-paketet:**

* Ange instrumenteringsnyckeln i kod, till exempel i `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *din nyckel* `";` 
* [Skriv din egen telemetri med hjälp av API](app-insights-api-custom-events-metrics.md#ikey).

**Om du har installerat andra Application Insights-paket** kan du, om du så önskar, använda .config-filen för att ange instrumenteringsnyckeln:

* Redigera ApplicationInsights.config (som lades till av installationsprogrammet för NuGet). Infoga detta precis före sluttaggen:
  
    `<InstrumentationKey>` *instrumenteringsnyckeln som du kopierade* `</InstrumentationKey>`
* Kontrollera att egenskaperna för ApplicationInsights.config i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.

Det är bra att ange instrumenteringsnyckeln i kod om du vill [byta nyckel för olika versionskonfigurationer](app-insights-separate-resources.md). Om du anger nyckeln i koden behöver du inte ange den i `.config`-filen.

## <a name="run"></a> Köra projektet
Använd **F5** när du vill köra programmet och testa det: öppna olika sidor för att generera telemetri.

I Visual Studio ser du antalet händelser som har skickats.

![Antal händelser i Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Visa telemetrin
Gå tillbaka till [Azure-portalen](https://portal.azure.com/) och bläddra till Application Insights-resursen.

Titta efter data i översiktsdiagrammet. Först ser du bara en eller två punkter. Till exempel:

![Klicka dig vidare till mer data](./media/app-insights-windows-services/12-first-perf.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden. [Lär dig mer om mätvärden.](app-insights-web-monitor-performance.md)

### Ser du inga data?
<a id="no-data" class="xliff"></a>
* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Öppna panelen [Sök](app-insights-diagnostic-search.md) om du vill visa enskilda händelser. Ibland tar det lite längre tid för händelser att passera genom pipelinen för mätvärden.
* Vänta några sekunder och klicka på **Uppdatera**. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan uppdatera dem manuellt om du väntar på att vissa data ska visas.
* Mer information finns i [Felsökning](app-insights-troubleshoot-faq.md).

## Publicera appen
<a id="publish-your-app" class="xliff"></a>
Distribuera ditt program till din server eller Azure och se hur data ackumuleras.

![Använda Visual Studio för att publicera din app](./media/app-insights-windows-services/15-publish.png)

När du kör i felsökningsläge skickas telemetrin genom pipelinen och du bör se data inom några sekunder. När du distribuerar appen i en publiceringskonfiguration ackumuleras data långsammare.

### Visas inga data efter att du har publicerat till servern?
<a id="no-data-after-you-publish-to-your-server" class="xliff"></a>
Öppna portar för utgående trafik i serverns brandvägg. På [den här sidan](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) kan du se listan med begärda adresser 

### Har du problem på build-servern?
<a id="trouble-on-your-build-server" class="xliff"></a>
Mer information finns i [det här felsökningsavsnittet](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Om din app genererar mycket telemetri minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är relaterade till samma begäran markeras eller avmarkeras dock som en grupp, så att du kan navigera mellan relaterade händelser. 
> [Lär dig mer om insamling](app-insights-sampling.md).
> 
> 

## Video
<a id="video" class="xliff"></a>

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## Nästa steg
<a id="next-steps" class="xliff"></a>
* [Lägg till mer telemetri](app-insights-asp-net-more.md)för att få en heltäckande bild av ditt program.


