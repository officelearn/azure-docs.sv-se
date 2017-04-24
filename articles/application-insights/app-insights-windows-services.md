---
title: "Azure Application Insights för Windows Server och arbetsroller | Microsoft Docs"
description: "Lägg till Application Insights SDK till ditt ASP.NET-program manuellt för att analysera användning, tillgänglighet och prestanda."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 29598f052778759ed362e3aa4b997acb799717ef
ms.lasthandoff: 04/13/2017


---
# <a name="manually-configure-application-insights-for-aspnet-applications"></a>Konfigurera Application Insights för ASP.NET manuellt
[Application Insights](app-insights-overview.md) är ett utökningsbart verktyg för webbutvecklare som övervakar prestandan och användningen av ditt liveprogram. Du kan konfigurera det manuellt för att övervaka Windows Server, arbetsroller och andra ASP.NET-program. För webbappar är manuell konfigurering ett alternativ till [automatisk installation](app-insights-asp-net.md) som Visual Studio erbjuder.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Innan du börjar
Du behöver:

* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).
* Visual Studio 2013 eller senare.

## <a name="add"></a>1. Skapa en Application Insights-resurs
Logga in på [Azure-portalen](https://portal.azure.com/) och skapa en ny Application Insights-resurs. Välj ASP.NET som programtypen.

![Klicka på Nytt, Application Insights](./media/app-insights-windows-services/01-new-asp.png)

En [resurs](app-insights-resources-roles-access-control.md) i Azure är en instans av en tjänst. Det är i den här resursen som telemetrin från din app analyseras och visas.

Valet av programtyp anger standardinnehållet i resursbladen och egenskaperna som visas i [Metrics Explorer](app-insights-metrics-explorer.md).

#### <a name="copy-the-instrumentation-key"></a>Kopiera instrumenteringsnyckeln
Nyckeln identifierar resursen och du ska snart installera den i SDK för att dirigera data till resursen.

![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C.](./media/app-insights-windows-services/02-props-asp.png)

De steg som du precis har utfört för att skapa en ny resurs är ett bra sätt att börja övervaka program. Nu kan du börja skicka data till den.

## <a name="sdk"></a>2. Installera Application Insights-paketet i ditt program
Hur du installerar och konfigurerar Application Insights-paketet varierar beroende på vilken plattform du arbetar på. Det är enkelt för ASP.NET-appar.

1. Redigera NuGet-paketet för ditt webbapprojekt i Visual Studio.
   
    ![Högerklicka på projektet och välj Hantera Nuget-paket.](./media/app-insights-windows-services/03-nuget.png)
2. Installera Application Insights-paketet för Windows Server-appar.
   
    ![Sök efter ”Application Insights”](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Kan jag använda andra paket?*
   
    Ja. Välj Core API (Microsoft.ApplicationInsights) om du vill använda API:et för att skicka din egen telemetri. Windows Server-paketet inkluderar Core API automatiskt plus ett antal andra paket som till exempel en samling med prestandaräknare och beroendeövervakning. 

#### <a name="to-upgrade-to-future-package-versions"></a>Så här uppgraderar du till framtida paketversioner
Då och då ger vi ut en ny version av SDK.

Om du vill uppgradera till en [ny paketversion](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.WindowsServer** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.

## <a name="3-send-telemetry"></a>3. Skicka telemetri
**Om du endast har installerat Core API-paketet:**

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

#### <a name="no-data"></a>Ser du inga data?
* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Öppna panelen [Sök](app-insights-diagnostic-search.md) om du vill visa enskilda händelser. Ibland tar det lite längre tid för händelser att passera genom pipelinen för mätvärden.
* Vänta några sekunder och klicka på **Uppdatera**. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan uppdatera dem manuellt om du väntar på att vissa data ska visas.
* Mer information finns i [Felsökning](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicera appen
Distribuera ditt program till din server eller Azure och se hur data ackumuleras.

![Använda Visual Studio för att publicera din app](./media/app-insights-windows-services/15-publish.png)

När du kör i felsökningsläge skickas telemetrin genom pipelinen och du bör se data inom några sekunder. När du distribuerar appen i en publiceringskonfiguration ackumuleras data långsammare.

#### <a name="no-data-after-you-publish-to-your-server"></a>Visas inga data efter att du har publicerat till servern?
Öppna portar för utgående trafik i serverns brandvägg. På [den här sidan](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) kan du se listan med begärda adresser 

#### <a name="trouble-on-your-build-server"></a>Har du problem på build-servern?
Mer information finns i [det här felsökningsavsnittet](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Om din app genererar mycket telemetri (och du använder ASP.NET SDK version 2.0.0-beta3 eller senare) minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är relaterade till samma begäran markeras eller avmarkeras dock som en grupp, så att du kan navigera mellan relaterade händelser. 
> [Lär dig mer om insamling](app-insights-sampling.md).
> 
> 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Lägg till mer telemetri](app-insights-asp-net-more.md)för att få en heltäckande bild av ditt program.


