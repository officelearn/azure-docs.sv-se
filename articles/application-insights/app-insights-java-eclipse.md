---
title: Kom igång med Azure Application Insights med Java i Eclipse | Microsoft docs
description: Använda plugin-programmet Eclipse för att lägga till prestanda och övervakning av programanvändning till din Java-webbplats med Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: e88c9f53-cd90-4abc-b097-1f170937908e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: 8e8e63b053cb5bd504a41da9b537354a1dd42968
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795568"
---
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Kom igång med Application Insights med Java i Eclipse
Application Insights SDK skickar telemetri från ditt webbprogram för Java så att du kan analysera användnings- och prestandadata. Plugin-program för Application Insights Eclipse installeras automatiskt SDK i projektet så att du får slut på rutan telemetri plus en API som du kan använda för att skriva en anpassad telemetri.   

## <a name="prerequisites"></a>Förutsättningar
För närvarande pluginprogrammet fungerar för Maven-projekt och dynamiskt webbprojekt i Eclipse.
([Lägg till Application Insights till andra typer av Java-projekt][java].)

Du behöver:

* JRE 1.7 eller 1.8
* En prenumeration på [Microsoft Azure](https://azure.microsoft.com/).
* [Solförmörkelse IDE för Java EE-utvecklare](http://www.eclipse.org/downloads/), Indigo eller senare.
* Windows 7 eller senare och Windows Server 2008 eller senare

Om du föredrar att källan framework försök den [konfigurera en källan Start initieraren app om du vill använda Application Insights-guiden](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)

## <a name="install-the-sdk-on-eclipse-one-time"></a>Installera SDK på Eclipse (en gång)
Du behöver bara göra detta en gång per dator. Det här steget installerar en verktygslåda som sedan kan lägga till SDK i varje dynamiskt webbprojekt.

1. Klicka på Hjälp, installera ny programvara i Eclipse.

    ![Hjälp, installera ny programvara](./media/app-insights-java-eclipse/0-plugin.png)
2. SDK är i http://dl.microsoft.com/eclipse, under Azure Toolkit.
3. Avmarkera **kontakta alla update platser...**

    ![För Application Insights SDK, rensar kontakta uppdatera alla platser](./media/app-insights-java-eclipse/1-plugin.png)

Följ stegen för varje Java-projekt.

## <a name="create-an-application-insights-resource-in-azure"></a>Skapa en Application Insights-resurs i Azure
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Skapa en ny Application Insights-resurs. Ange programtypen till Java-webbapp.  

    ![Klicka på + och välj Application Insights](./media/app-insights-java-eclipse/01-create.png)  

4. Leta upp instrumenteringsnyckeln för den nya resursen. Du ska snart klistra in den i projektkoden.  

    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/app-insights-java-eclipse/03-key.png)  

## <a name="add-application-insights-to-your-project"></a>Lägg till Application Insights i projektet
1. Lägg till Application Insights från snabbmenyn för webbprojektet Java.

    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/app-insights-java-eclipse/02-context-menu.png)
2. Klistra in nyckeln instrumentation som du har fått från Azure-portalen.

    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/app-insights-java-eclipse/03-ikey.png)

Nyckeln skickas tillsammans med alla element på telemetri och talar om Application Insights för att visa den i din resurs.

## <a name="run-the-application-and-see-metrics"></a>Kör programmet och se mått
Kör ditt program.

Gå tillbaka till Application Insights-resurs i Microsoft Azure.

Data om HTTP-begäranden visas på översiktsbladet. (Om informationen inte visas väntar du några sekunder och klickar på Uppdatera.)

![Svaret från servern och begäran antal fel ](./media/app-insights-java-eclipse/5-results.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden.

![Begär antal efter namn](./media/app-insights-java-eclipse/6-barchart.png)

[Lär dig mer om mätvärden.][metrics]

Och när du visar egenskaperna för en begäran, du kan se de telemetriska händelser som är kopplade till den, till exempel begäranden och undantag.

![Alla spårningar för den här begäran](./media/app-insights-java-eclipse/7-instance.png)

## <a name="client-side-telemetry"></a>Telemetri för klientsidan
Klicka på Hämta koden för att övervaka webbplatser från Snabbstart-bladet:

![På översiktsbladet för appen väljer du Snabbstart, Hämta kod för att övervaka webbplatser. Kopiera skriptet.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Infoga kodstycket i toppen av HTML-filer.

#### <a name="view-client-side-data"></a>Visa data för klientsidan
Öppna din uppdaterade webbsidor och använder dem. Vänta en minut eller två, och sedan tillbaka till Application Insights och öppna bladet användning. (Översikt över-bladet rulla nedåt och klicka på användning.)

Sidan Visa, användare och session mått visas i bladet användning:

![Sessioner, användare och sidvyer](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Mer information om hur du konfigurerar telemetri för klientsidan.][usage]

## <a name="publish-your-application"></a>Publicera programmet
Publicera appen på servern, låt användarna använda den och se hur telemetrin visas på portalen.

* Kontrollera att din brandvägg tillåter programmet att skicka telemetri till följande portar:

  * dc.services.visualstudio.com:443
  * dc.services.visualstudio.com:80
  * f5.services.visualstudio.com:443
  * f5.services.visualstudio.com:80
* Installera följande på Windows-servrar:

  * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Gör att du kan använda prestandaräknare.)

## <a name="exceptions-and-request-failures"></a>Fel relaterade till begäranden och undantag
Ohanterade undantag samlas in automatiskt:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Om du vill samla in data om andra undantag kan du välja mellan två alternativ:

* [Infoga anrop till TrackException i koden](app-insights-api-custom-events-metrics.md#trackexception).
* [Installera Java-agenten på servern](app-insights-java-agent.md). Du anger de metoder som du vill övervaka.

## <a name="monitor-method-calls-and-external-dependencies"></a>Övervaka metodanrop och externa beroenden
[Installera Java-agenten](app-insights-java-agent.md) om du vill logga angivna interna metoder och anrop som görs via JDBC, med tidsinställningsdata.

## <a name="performance-counters"></a>Prestandaräknare
Bläddra nedåt på Översikt-bladet och klicka på **servrar** panelen. Ser du ett antal prestandaräknare.

![Rulla klickar du på panelen servrar](./media/app-insights-java-eclipse/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Anpassa samlingen med prestandaräknare
Om du vill inaktivera datainsamlingen från standarduppsättningen med prestandaräknare lägger du till följande kod under rotnoden i filen ApplicationInsights.xml:

```XML

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Samla in data från fler prestandaräknare
Du kan ange ytterligare prestandaräknare som du vill samla in data från.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-räknare (exponeras av Java Virtual Machine)

```XML

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – Namnet visas på Application Insights-portalen.
* `objectName` – JMX-objektnamnet.
* `attribute` – Attributet för JMX-objektnamnet som ska hämtas
* `type` (valfritt) – Typen för JMX-objektets attribut:
  * Standardvärde: en enkel typ, till exempel int eller long.
  * `composite`: prestandaräknardata har formatet ”Attribute.Data”
  * `tabular`: prestandaräknardata har tabellradsformat

#### <a name="windows-performance-counters"></a>Windows-prestandaräknare
Varje [Windows-prestandaräknare](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) är medlem i en kategori (på samma sätt som ett fält är medlem i en klass). Kategorier kan antingen vara globala eller ha numrerade eller namngivna instanser.

```XML

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Namnet som visas på Application Insights-portalen.
* categoryName – Kategorin av prestandaräknare (prestandaobjekt) som den här prestandaräknaren är associerad med.
* counterName – Namnet på prestandaräknaren.
* instanceName – Namnet på instansen av prestandaräknarkategorin eller en tom sträng ("") om kategorin innehåller en enda instans. Om categoryName är Process och den prestandaräknare som du vill samla in data från hör till den aktuella JVM-processen som din app körs i anger du `"__SELF__"`.

Dina prestandaräknare visas som anpassade mått i [Metrics Explorer][metrics].

![](./media/app-insights-java-eclipse/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Unix-prestandaräknare
* [Installera collectd med Application Insights-plugin-programmet](app-insights-java-collectd.md) om du vill samla in en mängd olika system- och nätverksdata.

## <a name="availability-web-tests"></a>Webbtester för tillgänglighet
Application Insights kan testa din webbplats med jämna mellanrum för att kontrollera att tjänsten är tillgänglig och att den svarar. [Att ställa in][availability], rulla ned till på tillgänglighet.

![Rulla ned, klicka på Tillgänglighet och sedan på Lägg till webbtest.](./media/app-insights-java-eclipse/31-config-web-test.png)

Du kan visa diagram över svarstider, samt få e-postaviseringar om platsen kraschar.

![Exempel på webbtest](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Läs mer om webbtester för tillgänglighet.][availability]

## <a name="diagnostic-logs"></a>Diagnostikloggar
Om du använder Logback eller Log4J (version 1.2 eller v2.0) för spårning, kan du ha spårloggarna skickas automatiskt till Application Insights där du kan utforska och söka i dem.

[Mer information om diagnostikloggar][javalogs]

## <a name="custom-telemetry"></a>Anpassad telemetri
Infoga några rader med kod i ditt webbprogram för Java ta reda på vad användarna gör med den eller för att diagnostisera problem.

Du kan infoga kod både webbsida JavaScript och Java serversidan.

[Lär dig mer om anpassad telemetri][track]

## <a name="next-steps"></a>Nästa steg
#### <a name="detect-and-diagnose-issues"></a>Identifiera och diagnostisera problem
* [Lägg till webbplats klienten telemetri] [ usage] att hämta prestanda telemetri från webbklienten.
* [Konfigurera webbtester][availability] för att se till att ditt program är aktivt och effektivt.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.
* [Fånga in spårningar Log4J eller Logback][javalogs]

#### <a name="track-usage"></a>Spåra användning
* [Lägg till webbplats klienten telemetri] [ usage] till övervakaren sidvisningar och grundläggande mått.
* [Spåra anpassade händelser och mått](app-insights-web-track-usage.md) att lära dig hur programmet används, både på klienten och servern.

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md
