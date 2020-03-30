---
title: Övervaka Python-program med Azure Monitor (förhandsversion) | Microsoft-dokument
description: Innehåller instruktioner för att koppla in OpenCensus Python med Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537116"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurera Azure Monitor för ditt Python-program

Azure Monitor stöder distribuerad spårning, måttinsamling och loggning av Python-program via integrering med [OpenCensus](https://opencensus.io). Den här artikeln kommer att gå igenom processen att konfigurera OpenCensus för Python och skicka dina övervakningsdata till Azure Monitor.

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Python-installation. Den här artikeln använder [Python 3.7.0,](https://www.python.org/downloads/)även om tidigare versioner sannolikt fungerar med mindre ändringar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Skapa en application insights-resurs i Azure Monitor

Först måste du skapa en Application Insights-resurs i Azure Monitor, som genererar en instrumenteringsnyckel (ikey). Ikey används sedan för att konfigurera OpenCensus SDK för att skicka telemetridata till Azure Monitor.

1. Välj **Skapa en resurs** > **Developer tools** > **Application Insights**.

   ![Lägga till en application insights-resurs](./media/opencensus-python/0001-create-resource.png)

1. En konfigurationsruta visas. Använd följande tabell för att fylla i inmatningsfälten.

   | Inställning        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namn på den nya resursgruppen som ska vara värd för Application Insights-data |
   | **Location** | USA, östra | En plats nära dig eller i närheten av den plats där appen finns |

1. Välj **Skapa**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument med OpenCensus Python SDK för Azure Monitor

Installera exportörerna av OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

En fullständig lista över paket och integrationer finns i [OpenCensus-paket](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Kommandot `python -m pip install opencensus-ext-azure` förutsätter att du `PATH` har en miljövariabel inställd för din Python-installation. Om du inte har konfigurerat den här variabeln måste du ange den fullständiga katalogsökvägen dit din Körbara Python-körbar finns. Resultatet är ett kommando `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`som detta: .

SDK använder tre Azure Monitor-exportörer för att skicka olika typer av telemetri till Azure Monitor: spårning, mått och loggar. Mer information om dessa telemetrityper finns [i översikten över dataplattformen](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Använd följande instruktioner för att skicka dessa telemetrityper via de tre exportörerna.

## <a name="telemetry-type-mappings"></a>Mappningar av telemetrityp

Här är exportörerna som OpenCensus tillhandahåller mappade till de typer av telemetri som du ser i Azure Monitor.

![Skärmbild av mappning av telemetrityper från OpenCensus till Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Spårning

> [!NOTE]
> `Trace`i OpenCensus avser [distribuerad spårning](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). Skickar `AzureExporter` `requests` och `dependency` telemetri till Azure Monitor.

1. Låt oss först generera vissa spårningsdata lokalt. Ange följande kod i Python IDLE eller din redigerare.

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

2. Om du kör koden uppmanas du upprepade gånger att ange ett värde. Med varje post skrivs värdet ut till skalet och OpenCensus Python-modulen genererar en motsvarande bit . `SpanData` OpenCensus-projektet definierar ett [spår som ett spännviddsträd](https://opencensus.io/core-concepts/tracing/).
    
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

3. Även om det är bra att ange värden i `SpanData` demonstrationssyfte, vill vi i slutändan släppa ut till Azure Monitor. Skicka anslutningssträngen direkt till exportören, eller så `APPLICATIONINSIGHTS_CONNECTION_STRING`kan du ange den i en miljövariabel . Ändra koden från föregående steg baserat på följande kodexempel:

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

4. Nu när du kör Python-skriptet bör du ändå uppmanas att ange värden, men bara värdet skrivs ut i skalet. Den `SpanData` skapade skickas till Azure Monitor. Du hittar de utsända intervalldata under `dependencies`. Mer information om utgående begäranden finns i OpenCensus [Python-beroenden](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency).
Mer information om inkommande begäranden finns i OpenCensus [Python-begäranden](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>Samling

För information om provtagning i OpenCensus, ta en titt på [provtagning i OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Spåra korrelation

Mer information om telemetrikorrelation i spårningsdata finns i OpenCensus Python [telemetrikorrelation](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan den skickas till Azure Monitor finns i OpenCensus Python [telemetriprocessorer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

### <a name="metrics"></a>Mått

1. Låt oss först generera några lokala måttdata. Vi skapar ett enkelt mått för att spåra hur många gånger användaren trycker på Retur.

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
2. Om du kör koden uppmanas du upprepade gånger att trycka på Retur. Ett mått skapas för att spåra antalet gånger som Enter trycks ned. För varje post ökas värdet och måttinformationen visas i konsolen. Informationen innehåller det aktuella värdet och den aktuella tidsstämpeln när måttet uppdaterades.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Även om det är användbart att ange värden i demonstrationssyfte, vill vi i slutändan avge måttdata till Azure Monitor. Skicka anslutningssträngen direkt till exportören, eller så `APPLICATIONINSIGHTS_CONNECTION_STRING`kan du ange den i en miljövariabel . Ändra koden från föregående steg baserat på följande kodexempel:

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

4. Exportören skickar måttdata till Azure Monitor med ett fast intervall. Standard är var 15:e sekund. Vi spårar ett enda mått, så de här måttdata, med det värde och tidsstämpel som det innehåller, kommer att skickas varje intervall. Du hittar data `customMetrics`under .

#### <a name="standard-metrics"></a>Standardmått

Som standard skickar exportören av mått en uppsättning standardmått till Azure Monitor. Du kan inaktivera detta `enable_standard_metrics` genom `False` att ställa in flaggan till i konstruktorn för måttexportören.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Nedan finns en lista över standardmått som för närvarande skickas:

- Tillgängligt minne (byte)
- CPU-processortid (procent)
- Inkommande begäran (per sekund)
- Genomsnittlig körningstid för inkommande begäran (millisekunder)
- Utgående begäran (per sekund)
- Process CPU-användning (procent)
- Bearbeta privata byte (byte)

Du bör kunna se dessa `performanceCounters`mått i . Priset för inkommande `customMetrics`begäran skulle vara under . Mer information finns i [prestandaräknare](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan den skickas till Azure Monitor finns i OpenCensus Python [telemetriprocessorer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

### <a name="logs"></a>Loggar

1. Låt oss först generera några lokala loggdata.

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

2.  Koden kommer kontinuerligt att be om ett värde som ska anges. En loggpost skickas ut för varje angivet värde.

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

3. Även om det är bra att ange värden i demonstrationssyfte, vill vi i slutändan avge loggdata till Azure Monitor. Skicka anslutningssträngen direkt till exportören, eller så `APPLICATIONINSIGHTS_CONNECTION_STRING`kan du ange den i en miljövariabel . Ändra koden från föregående steg baserat på följande kodexempel:

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

4. Exportören skickar loggdata till Azure Monitor. Du hittar data `traces`under . 

> [!NOTE]
> `traces`i detta sammanhang är `Tracing`inte samma sak som . `traces`refererar till den typ av telemetri som visas i `AzureLogHandler`Azure Monitor när du använder . `Tracing`avser ett koncept i OpenCensus och avser [distribuerad spårning](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Om du vill formatera dina `formatters` loggmeddelanden kan du använda det inbyggda Python-loggnings-API:et . [logging API](https://docs.python.org/3/library/logging.html#formatter-objects)

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

6. Du kan också lägga till anpassade egenskaper i dina loggmeddelanden i det *extra* nyckelordsargumentet med hjälp av fältet custom_dimensions. Dessa visas som nyckel-värde-par i `customDimensions` i Azure Monitor.
> [!NOTE]
> För att den här funktionen ska fungera måste du skicka en ordlista till fältet custom_dimensions. Om du skickar argument av någon annan typ ignorerar loggern dem.

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
    ```

#### <a name="sending-exceptions"></a>Skicka undantag

OpenCensus Python spårar inte automatiskt `exception` och skickar telemetri. De skickas via `AzureLogHandler` undantagen via Python-loggningsbiblioteket. Du kan lägga till anpassade egenskaper precis som med normal loggning.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Eftersom du måste logga undantag uttryckligen är det upp till användaren i hur de vill logga ohanterade undantag. OpenCensus begränsar inte hur en användare vill göra detta, så länge de uttryckligen loggar en undantagstelemetri.

#### <a name="sampling"></a>Samling

För information om provtagning i OpenCensus, ta en titt på [provtagning i OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Loggkorrelation

Mer information om hur du berikar dina loggar med spårningskontextdata finns i Integreringen av OpenCensus [Python-loggar](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan den skickas till Azure Monitor finns i OpenCensus Python [telemetriprocessorer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

## <a name="view-your-data-with-queries"></a>Visa dina data med frågor

Du kan visa telemetridata som skickades från ditt program via fliken **Loggar (Analytics).**

![Skärmbild av översiktsfönstret med "Loggar (Analytics)" markerat i en röd ruta](./media/opencensus-python/0010-logs-query.png)

I listan under **Aktiv:**

- För telemetri som skickas med Azure Monitor-spårningsexportören visas inkommande begäranden under `requests`. Utgående eller pågående `dependencies`begäranden visas under .
- För telemetri som skickas med exportören av Azure `customMetrics`Monitor-mått visas skickade mått under .
- För telemetri som skickas med exportören av Azure `traces`Monitor-loggar visas loggar under . Undantag visas `exceptions`under .

Mer detaljerad information om hur du använder frågor och loggar finns [i Loggar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Läs mer om OpenCensus för Python

* [OpenCensus Python på GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Anpassning](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor Exportörer på GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus Integrationer](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Exempelprogram för Azure-övervakare](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Nästa steg

* [Spåra inkommande begäranden](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Spåra utgående förfrågningar](./../../azure-monitor/app/opencensus-python-request.md)
* [Programkarta](./../../azure-monitor/app/app-map.md)
* [Övervakning av prestanda från slutna till slutna dagar](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](../../azure-monitor/app/proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Måttaviseringar](../../azure-monitor/app/alerts.md): Ställ in aviseringar för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.
