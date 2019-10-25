---
title: Övervaka python-program med Azure Monitor (för hands version) | Microsoft Docs
description: Innehåller instruktioner för att ansluta till openräkningar python med Azure Monitor
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0d848027d6c754df371b4d87cf01c5b2fdbc8c02
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820732"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Konfigurera Azure Monitor för din python-app (för hands version)

Azure Monitor stöder distribuerad spårning, Metric-insamling och loggning av python-program via integrering med [openräkning](https://opencensus.io). Den här artikeln vägleder dig genom processen med att konfigurera openräkning för python och skicka övervaknings data till Azure Monitor.

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Python-installation. Den här artikeln använder [python 3.7.0](https://www.python.org/downloads/), men tidigare versioner kommer förmodligen att fungera med mindre ändringar.



## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Skapa en Application Insights resurs i Azure Monitor

Först måste du skapa en Application Insights resurs i Azure Monitor, vilket genererar en Instrumentation-nyckel (iKey). IKey används sedan för att konfigurera openräkning SDK för att skicka telemetridata till Azure Monitor.

1. Välj **Skapa en resurs** > **Utvecklarverktyg** > **Application Insights**.

   ![Lägga till en Application Insights resurs](./media/opencensus-python/0001-create-resource.png)

1. En konfigurations ruta visas. Använd följande tabell för att fylla i inmatade fält.

   | Inställning        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar den app som du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namn för den nya resurs gruppen som ska vara värd för Application Insights data |
   | **Plats** | USA, östra | En plats nära dig, eller nära var din app finns |

1. Välj **Skapa**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument med python SDK för openräkning för Azure Monitor

Installera Azure Monitor-exportörer för Open-räkning:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> Kommandot `python -m pip install opencensus-ext-azure` förutsätter att du har en variabel för `PATH`-miljövariabeln som har angetts för din python-installation. Om du inte har konfigurerat den här variabeln måste du ge fullständig katalog Sök väg till den plats där din python-körbara fil finns. Resultatet är ett kommando som detta: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

SDK använder tre Azure Monitor exportörer för att skicka olika typer av telemetri till Azure Monitor: spårning, mått och loggar. Mer information om dessa typer av telemetri finns i [Översikt över data plattformen](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Använd följande instruktioner för att skicka dessa typer av telemetri via de tre exportörerna.

### <a name="trace"></a>Spårning

1. Först ska vi generera vissa spårnings data lokalt. I python-inaktivitet, eller valfritt redigerings program, anger du följande kod.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Genom att köra koden uppmanas du att ange ett värde upprepade gånger. Vid varje post skrivs värdet till gränssnittet och python-modulen för openräkning genererar en motsvarande `SpanData`. Projektet openspanion definierar en [spårning som ett träd över spänner](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera `SpanData` till Azure Monitor. Ändra koden från föregående steg baserat på följande kod exempel:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Nu när du kör python-skriptet bör du fortfarande uppmanas att ange värden, men bara värdet skrivs ut i gränssnittet. Den skapade `SpanData` skickas till Azure Monitor. Du kan hitta de data som genereras för varje period under `dependencies`.

### <a name="metrics"></a>Mått

1. Först ska vi generera några lokala mått data. Vi ska skapa ett enkelt mått för att spåra hur många gånger användaren trycker på RETUR.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Genom att köra koden uppmanas du upprepade gånger att trycka på RETUR. Ett mått skapas för att spåra antalet gånger som retur trycks ned. Vid varje post ökas värdet och mått informationen visas i-konsolen. Informationen omfattar det aktuella värdet och den aktuella tidsstämpeln när måttet uppdaterades.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera mått data till Azure Monitor. Ändra koden från föregående steg baserat på följande kod exempel:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. Export verktyget skickar mått data till Azure Monitor med ett fast intervall. Standardvärdet är var 15: e sekund. Vi spårar ett enda mått, så dessa mått data, med det värde och den tidsstämpel som den innehåller, kommer att skickas varje intervall. Du kan hitta data under `customMetrics`.

### <a name="logs"></a>Loggar

1. Först ska vi generera vissa lokala loggdata.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  Koden kommer kontinuerligt att fråga efter ett värde som ska anges. En loggpost genereras för varje angivet värde.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera mått data till Azure Monitor. Ändra koden från föregående steg baserat på följande kod exempel:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. Export verktyget kommer att skicka loggdata till Azure Monitor. Du kan hitta data under `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna Application Insights **översikts** fönstret i Azure Portal för att visa information om ditt program som körs. Välj **Live Metrics Stream**.

   ![Skärm bild av översikts fönstret med "Live Metrics Stream" markerat i en röd ruta](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Gå tillbaka till **översikts** fönstret. Välj **program karta** för en visuell layout av beroende relationerna och anropa tiden mellan program komponenterna.

   ![Skärm bild av en grundläggande program karta](./media/opencensus-python/0007-application-map.png)

   Eftersom vi bara spårade ett metod anrop är vår program karta inte intressant. Men en program karta kan skalas för att visualisera mycket fler distribuerade program:

   ![Programkarta](media/opencensus-python/application-map.png)

3. Välj **Undersök prestanda** för att analysera prestanda i detalj och fastställ rotor saken till långsamma prestanda.

   ![Skärm bild av prestanda information](./media/opencensus-python/0008-performance.png)

4. Om du vill öppna den heltäckande upplevelsen för transaktions information väljer du **exempel**och sedan något av de exempel som visas i den högra rutan. 

   Även om vår app-app bara visar en enda händelse, kan ett mer komplext program hjälpa dig att utforska slutpunkt-till-slutpunkt-transaktionen ned till nivån för en enskild händelses anrops stack.

   ![Skärm bild av transaktions gränssnittet från slut punkt till slut punkt](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Visa dina data med frågor

Du kan visa telemetri-data som har skickats från ditt program via fliken **loggar (Analytics)** .

![Skärm bild av översikts fönstret med "loggar (analyser)" markerade i en röd ruta](./media/opencensus-python/0010-logs-query.png)

I listan under **Active**:

- För telemetri som skickas med Azure Monitor trace-export ören visas inkommande begär Anden under `requests`. Utgående eller aktiva begär Anden visas under `dependencies`.
- För telemetri som skickas med Azure Monitor Metrics-exporten visas de skickade måtten under `customMetrics`.
- För telemetri som skickas med Azure Monitor loggar exportör visas loggar under `traces`. Undantag visas under `exceptions`.

Mer detaljerad information om hur du använder frågor och loggar finns [i loggar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Läs mer om openräkning för python

* [Python-räkningar på GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Eventuella](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Mätkolv-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Nästa steg

* [Program karta](./../../azure-monitor/app/app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](../../azure-monitor/app/proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Mått aviseringar](../../azure-monitor/app/alerts.md): Ställ in aviseringar för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.
