---
title: Övervaka python-program med Azure Monitor | Microsoft Docs
description: Innehåller instruktioner för att ansluta till openräkningar python med Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293998"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurera Azure Monitor för ditt python-program

Azure Monitor stöder distribuerad spårning, Metric-insamling och loggning av python-program via integrering med [openräkning](https://opencensus.io). I den här artikeln får du stegvisa anvisningar genom processen för att ställa in openräkning för python och hämta övervaknings data till Azure Monitor.

## <a name="prerequisites"></a>Krav

- Du behöver en Azure-prenumeration.
- Python bör installeras, den här artikeln använder [python 3.7.0](https://www.python.org/downloads/), men tidigare versioner kommer förmodligen att fungera med mindre justeringar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Skapa Application Insights resurs i Azure Monitor

Först måste du skapa en Application Insights resurs i Azure Monitor, vilket genererar en Instrumentation-nyckel (iKey). IKey används sedan för att konfigurera openräkning SDK för att skicka telemetridata till Azure Monitor.

1. Välj **skapa en resurs** > **utvecklarverktyg** > **Application Insights**.

   ![lägg till en Application Insights-resurs](./media/opencensus-python/0001-create-resource.png)

   En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Plats** | Östra USA | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentering med python-SDK: n för openräkning för Azure Monitor

1. Installera Azure Monitor-exportörer för Open-räkning:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` förutsätter att du har en Sök VÄGS miljö variabel uppsättning för din python-installation. Om du inte har konfigurerat detta måste du ge fullständig katalog Sök väg till den plats där din python-körbara fil finns, vilket skulle resultera i ett kommando som: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. SDK använder tre Azure Monitor exportörer för att skicka olika typer av telemetri till Azure Monitor: spårning, mått och loggar. Ta en titt på [data plattforms översikten](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) för mer information om de olika typerna. Följ anvisningarna nedan för att se hur du skickar de olika typerna via de tre exportörerna.

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

2. Genom att köra koden uppmanas du att ange ett värde upprepade gånger. Vid varje post skrivs värdet till gränssnittet och en motsvarande del av **SpanData** genereras av python-modulen för openräkning. Projektet openspanion definierar en [_spårning som ett träd över spänner_](https://opencensus.io/core-concepts/tracing/).
    
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

3. Vi kan till exempel vara användbara i demonstrations syfte, och vi vill därför generera `SpanData` till Azure Monitor. Ändra koden från föregående steg baserat på följande kod exempel:

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

4. Nu när du kör python-skriptet bör du fortfarande uppmanas att ange värden, men nu är det bara värdet som skrivs ut i gränssnittet. @No__t-0 som skapats skickas till Azure Monitor. Du kan hitta de data som genereras för varje period under `dependencies`.

### <a name="metrics"></a>Mått

1. Först ska vi generera några lokala mått data. Vi kommer att skapa ett enkelt mått för att spåra hur många gånger användaren trycker på RETUR.

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
2. Genom att köra koden uppmanas du upprepade gånger att trycka på RETUR. Ett mått skapas för att spåra antalet returer som trycks ned. Vid varje post ökas värdet och mått informationen visas i-konsolen, med det aktuella värdet och den aktuella tidsstämpeln när måttet uppdaterades.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Vi kan till exempel vara användbara i demonstrations syfte, och vi vill därför generera mått data till Azure Monitor. Ändra koden från föregående steg baserat på följande kod exempel:

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

4. Export verktyget skickar mått data till Azure Monitor med ett fast intervall, som standard var 15: e sekund. Vi spårar ett enda mått så att dessa mått data, med det värde och den tidsstämpel som den innehåller, skickas varje intervall. Du kan hitta data under `customMetrics`.

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

2.  Koden kommer att fråga efter fråga efter ett värde som ska anges. En loggpost genereras för varje värde som anges och som innehåller det värdet.

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

3. Vi kan till exempel vara användbara i demonstrations syfte, och vi vill därför generera mått data till Azure Monitor. Ändra koden från föregående steg baserat på följande kod exempel:

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

1. Nu kan du öppna sidan Application Insights **Översikt** i Azure Portal för att visa information om ditt program som körs. Välj **Live Metric Stream**.

   ![Skärm bild av översikts fönstret med direkt mått data ström vald i röd ruta](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Gå tillbaka till sidan **Översikt** och välj **program karta** för en visuell layout av beroende relationerna och anropa tiden mellan program komponenterna.

    ![Skärm bild av grundläggande program karta](./media/opencensus-python/0007-application-map.png)

    Eftersom vi bara spårade ett metod anrop är vår program karta inte lika intressant. Men program kartan kan skalas för att visualisera mycket fler distribuerade program:

   ![Programavbildning](media/opencensus-python/application-map.png)

3. Välj **Undersök prestanda** för att utföra detaljerad prestanda analys och fastställ rotor saken till långsamma prestanda.

    ![Skärm bild av fönstret prestanda](./media/opencensus-python/0008-performance.png)

4. Om du väljer **exempel** och sedan klickar på något av de exempel som visas i den högra rutan, startas transaktions information från slut punkt till slut punkt. Vår exempel App visar bara oss en enda händelse, ett mer komplext program som gör att du kan utforska slut punkt till slut punkt på nivån för en enskild händelses anrops stack.

     ![Skärm bild av transaktions gränssnitt från slut punkt till slut punkt](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Visa dina data med frågor

1. Du kan visa telemetri-data som har skickats från ditt program via fliken loggar (Analytics).

    ![Skärm bild av översikts fönstret med loggar (Analytics) markerat i rutan röd](./media/opencensus-python/0010-logs-query.png)

2. För telemetri som skickas med Azure Monitor trace-export ören visas inkommande begär Anden under `requests` och pågående/pågående-begär Anden som visas under `dependencies`.

3. För telemetri som skickas med Azure Monitor Metrics-exporten visas måtten som skickas under `customMetrics`.

4. För telemetri som skickas med Azure Monitor loggar exportör visas loggar under `traces` och undantag visas under `exceptions`.

5. Ta en titt på [loggarna i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) om du vill ha mer detaljerad information om hur du använder frågor och loggar.

## <a name="opencensus-for-python"></a>Openräkning för python

* [Python-räkningar på GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Eventuella](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Mätkolv-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Nästa steg

* [API-Sammanfattning](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Program karta](./../../azure-monitor/app/app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](../../azure-monitor/app/proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Mått aviseringar](../../azure-monitor/app/alerts.md): Ställ in aviseringar för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.