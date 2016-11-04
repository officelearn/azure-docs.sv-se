---
title: Application Insights för Windows-tjänster och arbetsroller | Microsoft Docs
description: Lägg till Application Insights SDK till ditt ASP.NET-program manuellt för att analysera användning, tillgänglighet och prestanda.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: awills

---
# Konfigurera Application Insights för ASP.NET 4-program manuellt
*Application Insights finns endast som förhandsversion.*

[!INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Du kan konfigurera [Visual Studio Application Insights](app-insights-overview.md) manuellt för att övervaka Windows-tjänster, arbetsroller och andra ASP.NET-program. För webbappar är manuell konfigurering ett alternativ till [automatisk installation](app-insights-asp-net.md) som Visual Studio erbjuder.

Application Insights hjälper dig att diagnostisera problem och att övervaka prestanda och användningen av ditt liveprogram.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-windows-services/10-perf.png)

#### Innan du börjar
Du behöver:

* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).
* Visual Studio 2013 eller senare.

## <a name="add"></a>1. Skapa en Application Insights-resurs
Logga in på [Azure-portalen](https://portal.azure.com/) och skapa en ny Application Insights-resurs. Välj ASP.NET som programtypen.

![Klicka på Nytt, Application Insights](./media/app-insights-windows-services/01-new-asp.png)

En [resurs](app-insights-resources-roles-access-control.md) i Azure är en instans av en tjänst. Det är i den här resursen som telemetrin från din app analyseras och visas.

Valet av programtyp anger standardinnehållet i resursbladen och egenskaperna som visas i [Metrics Explorer](app-insights-metrics-explorer.md).

#### Kopiera instrumenteringsnyckeln
Nyckeln identifierar resursen och du ska snart installera den i SDK för att dirigera data till resursen.

![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C.](./media/app-insights-windows-services/02-props-asp.png)

De steg som du precis har utfört för att skapa en ny resurs är ett bra sätt att börja övervaka program. Nu kan du börja skicka data till den.

## <a name="sdk"></a>2. Installera SDK i ditt program
Hur du installerar och konfigurerar Application Insights SDK varierar beroende på vilken plattform du arbetar på. Det är enkelt för ASP.NET-appar.

1. Redigera NuGet-paketet för ditt webbapprojekt i Visual Studio.
   
    ![Högerklicka på projektet och välj Hantera Nuget-paket.](./media/app-insights-windows-services/03-nuget.png)
2. Installera Application Insights SDK för Web Apps.
   
    ![Sök efter ”Application Insights”](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Kan jag använda andra paket?*
   
    Ja. Välj Core API (Microsoft.ApplicationInsights) om du vill använda API:et för att skicka din egen telemetri. Windows Server-paketet inkluderar Core API automatiskt plus ett antal andra paket som till exempel en samling med prestandaräknare och beroendeövervakning. 

#### Så här uppgraderar du till framtida SDK-versioner
Då och då ger vi ut en ny version av SDK.

Om du vill uppgradera till en [ny SDK-version](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.Web** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.

## 3. Skicka telemetri
**Om du endast har installerat Core API-paketet:**

* Ange instrumenteringsnyckeln i kod, till exempel i `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *din nyckel* `";` 
* [Skriv din egen telemetri med hjälp av API](app-insights-api-custom-events-metrics.md#ikey).

**Om du har installerat andra Application Insights-paket** kan du, om du så önskar, använda .config-filen för att ange instrumenteringsnyckeln:

* Redigera ApplicationInsights.config (som lades till av installationsprogrammet för NuGet). Infoga detta precis före sluttaggen:
  
    `<InstrumentationKey>` *instrumenteringsnyckeln som du kopierade* `</InstrumentationKey>`
* Kontrollera att egenskaperna för ApplicationInsights.config i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.

## <a name="run"></a> Köra projektet
Använd **F5** när du vill köra programmet och testa det: öppna olika sidor för att generera telemetri.

I Visual Studio ser du antalet händelser som har skickats.

![Antal händelser i Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Visa telemetrin
Gå tillbaka till [Azure-portalen](https://portal.azure.com/) och bläddra till Application Insights-resursen.

Titta efter data i översiktsdiagrammet. Först ser du bara en eller två punkter. Till exempel:

![Klicka dig vidare till mer data](./media/app-insights-windows-services/12-first-perf.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden. [Lär dig mer om mätvärden.](app-insights-web-monitor-performance.md)

#### Ser du inga data?
* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Öppna panelen [Sök](app-insights-diagnostic-search.md) om du vill visa enskilda händelser. Ibland tar det lite längre tid för händelser att passera genom pipelinen för mätvärden.
* Vänta några sekunder och klicka på **Uppdatera**. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan uppdatera dem manuellt om du väntar på att vissa data ska visas.
* Mer information finns i [Felsökning](app-insights-troubleshoot-faq.md).

## Publicera appen
Distribuera ditt program till din server eller Azure och se hur data ackumuleras.

![Använda Visual Studio för att publicera din app](./media/app-insights-windows-services/15-publish.png)

När du kör i felsökningsläge skickas telemetrin genom pipelinen och du bör se data inom några sekunder. När du distribuerar appen i en publiceringskonfiguration ackumuleras data långsammare.

#### Visas inga data efter att du har publicerat till servern?
Öppna dessa portar för utgående trafik i serverns brandvägg:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### Har du problem på build-servern?
Mer information finns i [det här felsökningsavsnittet](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Om din app genererar mycket telemetri (och du använder ASP.NET SDK version 2.0.0-beta3 eller senare) minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är relaterade till samma begäran markeras eller avmarkeras dock som en grupp, så att du kan navigera mellan relaterade händelser. 
> [Lär dig mer om insamling](app-insights-sampling.md).
> 
> 

## Nästa steg
* [Lägg till mer telemetri](app-insights-asp-net-more.md)för att få en heltäckande bild av ditt program.

<!--HONumber=Sep16_HO3-->


