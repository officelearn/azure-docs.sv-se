---
title: OpenCensus Python spårning med Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att koppla upp OpenCensus Python spårning med lokala vidarebefordrare och Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7507cdd7f3967dc132eecf81de347f2da791487f
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414989"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Samla in distribuerade spårningar från Python (förhandsversion)

Application Insights nu stöder distribuerad spårning av Python-program via integrering med [OpenCensus](https://opencensus.io) och vår nya [lokala vidarebefordrare](./opencensus-local-forwarder.md). Den här artikeln beskriver steg för steg hur du ställer in OpenCensus för Python och få din spårningsdata till Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en Azure-prenumeration.
- Python ska installeras, den här artikeln använder [Python 3.7.0](https://www.python.org/downloads/), även om tidigare versioner kommer troligen att fungera med mindre justering.
- Följ anvisningarna för att installera den [lokala vidarebefordrare som en Windows-tjänst](./opencensus-local-forwarder.md#windows-service)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Skapa Application Insights-källa

Du måste först skapa en Application Insights-resurs som genererar en instrumentation key(ikey). Nyckeln används sedan för att konfigurera den lokala vidarebefordraren för att skicka distribuerade spårningar från programmets OpenCensus som instrumenteras till Application Insights.   

1. Välj **skapa en resurs** > **utvecklarverktyg** > **Application Insights**.

   ![lägg till en Application Insights-resurs](./media/opencensus-python/0001-create-resource.png)

   En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Programtyp** | Allmänt | Typen av app du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Plats** | Östra USA | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="configure-local-forwarder"></a>Konfigurera lokala vidarebefordrare

1. Välj **Översikt** > **Essentials** > kopiera appens **instrumenteringsnyckel**.

   ![Skärmbild av instrumentationsnyckeln](./media/opencensus-python/0003-instrumentation-key.png)

2. Redigera din `LocalForwarder.config` filen och Lägg till din instrumentationsnyckel. Om du har följt anvisningarna i den [installationsprogrammets](./opencensus-local-forwarder.md#windows-service) filen finns på `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Starta om programmet **lokala vidarebefordrare** service.

## <a name="opencensus-python-package"></a>OpenCensus Python-paketet

1. Installera paketet öppna inventering för Python med pip eller pipenv från kommandoraden:

    ```python
    python -m pip install opencensus
    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` förutsätter att du har en PATH-miljövariabeln för Python-installationen. Om du inte har konfigurerat detta skulle du behöva ge den fullständiga sökvägen till platsen där ditt Python körbara filen finns som resulterar i ett kommando som: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. Första nu ska vi skapa vissa spårningsdata lokalt. I Python INAKTIV eller din redigerare föredrar, anger du följande kod:

    ```python
    from opencensus.trace.tracer import Tracer
    
    def main():
        while True:
            valuePrompt()
    
    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    
    ```

3. Kör kod uppmanas upprepade gånger du att ange ett värde. Med varje post, värdet ska skrivas till gränssnittet och en motsvarande typ av **SpanData** ska skapas av OpenCensus Python-modulen. Projektet OpenCensus definierar en [ _spårning som ett träd med spänner över_](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Även om det här är användbart i demonstrationssyfte slutligen vill vi sända SpanData på ett sätt som den kan hämtas av våra **lokala vidarebefordrartjänst** och skickade in Application Insights. Ändra din kod från föregående steg så här:

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.trace.exporters.ocagent import trace_exporter
    from opencensus.trace import tracer as tracer_module
    
    import os
    
    def main():        
        while True:
            valuePrompt()
    
    def valuePrompt():
        export_LocalForwarder = trace_exporter.TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))
        
        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    ```

5. Om du sparar och försök att köra modulen ovan, kan du få en `ModuleNotFoundError` för `grpc`. Om detta inträffar kör du följande för att installera den [grpcio paketet](https://pypi.org/project/grpcio/) med:

    ```
    python -m pip install grpcio
    ```

6. Nu när du kör Python-skriptet ovan du bör fortfarande uppmanas att ange värden, men nu endast värdet skrivs ut i gränssnittet.

7. Att bekräfta att den **lokala vidarebefordrare** plocka upp kontrollen spårningar den `LocalForwarder.config` filen. Om du har följt stegen i den [nödvändiga](https://docs.microsoft.com/azure/application-insights/local-forwarder#windows-service), det ska finnas i `C:\LF-WindowsServiceHost`.

    I nedanstående bild av loggfilen, kan du se att innan du kör skriptet andra där vi har lagt till en exportör `OpenCensus input BatchesReceived` : 0. När vi började köras det uppdaterade skriptet `BatchesReceived` ökar lika med antalet värden som vi angav:
    
    ![Nytt App Insights-resursformulär](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna Application Insights **översikt** sida i Azure portal, för att visa information om programmet som körs för närvarande. Välj **Live Metric Stream**.

   ![Skärmbild av översiktsfönstret med live metric stream för valt med röd ram](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Om du kör den igen andra Python-skriptet och starta om du anger värden, så ser du live spårningsdata när de anländer i Application Insights från lokala vidarebefordrartjänsten.

   ![Skärmbild av live metric stream för med prestandadata som visas](./media/opencensus-python/0006-stream.png)

3. Gå tillbaka till den **översikt** och välj **Programkartan** för en visuell layout av beroenden och anrop tidsinställning mellan programkomponenter.

    ![Skärmbild av program med grundläggande karta](./media/opencensus-python/0007-application-map.png)

    Eftersom vi bara spårning ett metodanrop, inte våra programkartan som intressant. Men programkartan kan skalas för att visualisera mycket mer distribuerade program:

   ![Programkarta](media/opencensus-python/application-map.png)

4. Välj **Undersök prestanda** att utföra detaljerade prestandaanalys och fastställa orsaken till långsamma prestanda.

    ![Skärmbild av fönstret för prestanda](./media/opencensus-python/0008-performance.png)

5. Att välja **exempel** och sedan klicka på någon av de exempel som visas i rutan till höger startar slutpunkt till slutpunkt transaktion information upplevelse. Även om vår exempelapp kommer bara att visa en enda händelse, skulle ett mer komplext program kan du utforska slutpunkt till slutpunkt-transaktion till nivå i en enskild händelse anropsstacken.

     ![Skärmbild av transaktionsgränssnitt som slutpunkt till slutpunkt](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>Spårning av OpenCensus för Python

Vi bara beskrivs grunderna för att koppla upp OpenCensus för Python med lokala vidarebefordrare och Application Insights. Den officiella användningsvägledning beskriver mer avancerade ämnen som:

* [Färgprov](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Flask-integrering](https://opencensus.io/api/python/trace/usage.html#flask)
* [Django-integrering](https://opencensus.io/api/python/trace/usage.html#django)
* [MySQL-integrering](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>Nästa steg

* [OpenCensus Python-användningsguiden](https://opencensus.io/api/python/trace/usage.html)
* [Programkarta](./app-insights-app-map.md)
* [Prestandaövervakning för slutpunkt till slutpunkt](./app-insights-tutorial-performance.md)
