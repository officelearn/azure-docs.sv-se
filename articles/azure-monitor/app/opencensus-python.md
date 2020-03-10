---
title: Övervaka python-program med Azure Monitor (för hands version) | Microsoft Docs
description: Innehåller instruktioner för att ansluta till openräkningar python med Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 61fdc2a4694405d4f56600b2d2b71e9e37232a7a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943251"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurera Azure Monitor för ditt python-program

Azure Monitor stöder distribuerad spårning, Metric-insamling och loggning av python-program via integrering med [openräkning](https://opencensus.io). Den här artikeln vägleder dig genom processen med att konfigurera openräkning för python och skicka övervaknings data till Azure Monitor.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Python-installation. Den här artikeln använder [python 3.7.0](https://www.python.org/downloads/), men tidigare versioner kommer förmodligen att fungera med mindre ändringar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

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

En fullständig lista över paket och integreringar finns i [openräkning-paket](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Kommandot `python -m pip install opencensus-ext-azure` förutsätter att du har en variabel för `PATH`-miljövariabeln som har angetts för din python-installation. Om du inte har konfigurerat den här variabeln måste du ge fullständig katalog Sök väg till den plats där din python-körbara fil finns. Resultatet är ett kommando som detta: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

SDK använder tre Azure Monitor exportörer för att skicka olika typer av telemetri till Azure Monitor: spårning, mått och loggar. Mer information om dessa typer av telemetri finns i [Översikt över data plattformen](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Använd följande instruktioner för att skicka dessa typer av telemetri via de tre exportörerna.

## <a name="telemetry-type-mappings"></a>Typ mappningar för telemetri

Här är de exportörer som openräkning tillhandahåller mappat till de typer av telemetri som visas i Azure Monitor.

![Skärm bild av mappning av typer av telemetri från openräkning till Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Spårning

> [!NOTE]
> `Trace` i openräkning avser [distribuerad spårning](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` skickar `requests` och `dependency` telemetri till Azure Monitor.

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

3. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera `SpanData` till Azure Monitor. Skicka anslutnings strängen direkt till export verktyget eller så kan du ange den i en miljö variabel `APPLICATIONINSIGHTS_CONNECTION_STRING`. Ändra koden från föregående steg baserat på följande kod exempel:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
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

4. Nu när du kör python-skriptet bör du fortfarande uppmanas att ange värden, men bara värdet skrivs ut i gränssnittet. Den skapade `SpanData` skickas till Azure Monitor. Du hittar de utgivna span-data under `dependencies`. Mer information om utgående begär Anden finns i openräkningar python- [beroenden](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency).
Mer information om inkommande begär Anden finns i openräkningar python- [begäranden](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>Samling

Om du vill ha mer information om sampling i openinventering kan du titta på [sampling i openräkning](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Spåra korrelation

Om du vill ha mer information om telemetri-korrelation i dina spårnings data kan du ta en titt på python- [korrelationen](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)i openräkning.

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan det skickas till Azure Monitor finns i openräkningar python [telemetri-processorer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

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

3. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera mått data till Azure Monitor. Skicka anslutnings strängen direkt till export verktyget eller så kan du ange den i en miljö variabel `APPLICATIONINSIGHTS_CONNECTION_STRING`. Ändra koden från föregående steg baserat på följande kod exempel:

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

#### <a name="standard-metrics"></a>Standard mått

Som standard skickar mått export verktyget en uppsättning standard mått till Azure Monitor. Du kan inaktivera detta genom att ställa in flaggan `enable_standard_metrics` till `False` i konstruktorn för mått exporten.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Nedan visas en lista över standard mått som har skickats för närvarande:

- Tillgängligt minne (byte)
- PROCESSOR processor tid (i procent)
- Frekvens för inkommande begäran (per sekund)
- Genomsnittlig körnings tid för inkommande begäran (millisekunder)
- Utgående begär ande frekvens (per sekund)
- Processor användning för processor (procent andel)
- Privata byte för process (byte)

Du bör kunna se dessa mått i `performanceCounters`. Antalet inkommande begär anden skulle vara under `customMetrics`.
#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan det skickas till Azure Monitor finns i openräkningar python [telemetri-processorer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

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

3. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera loggdata till Azure Monitor. Skicka anslutnings strängen direkt till export verktyget eller så kan du ange den i en miljö variabel `APPLICATIONINSIGHTS_CONNECTION_STRING`. Ändra koden från föregående steg baserat på följande kod exempel:

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

> [!NOTE]
> `traces` i den här kontexten är inte samma som `Tracing`. `traces` avser den typ av telemetri som visas i Azure Monitor när du använder `AzureLogHandler`. `Tracing` syftar på ett begrepp i openräkning och relaterar till [distribuerad spårning](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Om du vill formatera logg meddelanden kan du använda `formatters` i det inbyggda python- [loggnings-API: et](https://docs.python.org/3/library/logging.html#formatter-objects).

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Du kan också lägga till anpassade egenskaper till dina logg meddelanden i argumentet *extra* nyckelord med hjälp av fältet custom_dimensions. De visas som nyckel/värde-par i `customDimensions` i Azure Monitor.
> [!NOTE]
> För att den här funktionen ska fungera måste du skicka en ord lista till fältet custom_dimensions. Om du skickar argument av någon annan typ kommer loggaren att ignorera dem.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
    logger.exception('Captured an exception.', extra=properties)
    ```
#### <a name="sampling"></a>Samling

Om du vill ha mer information om sampling i openinventering kan du titta på [sampling i openräkning](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Logg korrelation

Mer information om hur du kan utöka dina loggar med spårnings kontext data finns i openräkningar python [logs integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan det skickas till Azure Monitor finns i openräkningar python [telemetri-processorer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

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
* [Azure Monitor exportörer på GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integreringar med openräkning](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor exempel program](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Nästa steg

* [Spåra inkommande begär Anden](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Spåra pågående förfrågningar](./../../azure-monitor/app/opencensus-python-request.md)
* [Program karta](./../../azure-monitor/app/app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](../../azure-monitor/app/proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Mått aviseringar](../../azure-monitor/app/alerts.md): Ställ in aviseringar för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.
