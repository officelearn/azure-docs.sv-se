---
title: Övervaka Python-program med Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att koppla upp OpenCensus Python med Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541441"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Samla in distribuerade spårningar från Python (förhandsversion)

Application Insights nu stöder distribuerad spårning av Python-program via integrering med [OpenCensus](https://opencensus.io). Den här artikeln vägleder dig stegvis genom processen med att ställa in OpenCensus för Python och hämtar dina övervakade data till Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en Azure-prenumeration.
- Python ska installeras, den här artikeln använder [Python 3.7.0](https://www.python.org/downloads/), även om tidigare versioner kommer troligen att fungera med mindre justering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Skapa Application Insights-resurs

Du måste först skapa en Application Insights-resurs som genererar en instrumentation key(ikey). Nyckeln används sedan för att konfigurera OpenCensus SDK för att skicka telemetridata till Application Insights.

1. Välj **skapa en resurs** > **utvecklarverktyg** > **Application Insights**.

   ![lägg till en Application Insights-resurs](./media/opencensus-python/0001-create-resource.png)

   En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Name**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Location** | East US | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Installera OpenCensus Azure Monitor exportörer

1. Installera OpenCensus Azure Monitor exportörer:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` förutsätter att du har en PATH-miljövariabeln för Python-installationen. Om du inte har konfigurerat detta skulle du behöva ge den fullständiga sökvägen till platsen där ditt Python körbara filen finns som resulterar i ett kommando som: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Första nu ska vi skapa vissa spårningsdata lokalt. I Python INAKTIV eller din redigerare föredrar, anger du följande kod:

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

3. Kör kod uppmanas upprepade gånger du att ange ett värde. Med varje post, värdet ska skrivas till gränssnittet och en motsvarande typ av **SpanData** ska skapas av OpenCensus Python-modulen. Projektet OpenCensus definierar en [ _spårning som ett träd med spänner över_](https://opencensus.io/core-concepts/tracing/).
    
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

4. När det är användbart i demonstrationssyfte, slutligen vill vi sända SpanData till Application Insights. Ändra din kod från föregående steg baserat på följande kodexempel:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Nu när du kör Python-skriptet ska du fortfarande uppmanas för att ange värden, men nu endast värdet skrivs ut i gränssnittet.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna Application Insights **översikt** sida i Azure portal, för att visa information om programmet som körs för närvarande. Välj **Live Metric Stream**.

   ![Skärmbild av översiktsfönstret med live metric stream för valt med röd ram](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Gå tillbaka till den **översikt** och välj **Programkartan** för en visuell layout av beroenden och anrop tidsinställning mellan programkomponenter.

    ![Skärmbild av program med grundläggande karta](./media/opencensus-python/0007-application-map.png)

    Eftersom vi bara spårning ett metodanrop, inte våra programkartan som intressant. Men programkartan kan skalas för att visualisera mycket mer distribuerade program:

   ![Programkarta](media/opencensus-python/application-map.png)

3. Välj **Undersök prestanda** att utföra detaljerade prestandaanalys och fastställa orsaken till långsamma prestanda.

    ![Skärmbild av fönstret för prestanda](./media/opencensus-python/0008-performance.png)

4. Att välja **exempel** och sedan klicka på någon av de exempel som visas i rutan till höger startar slutpunkt till slutpunkt transaktion information upplevelse. Även om vår exempelapp kommer bara att visa en enda händelse, skulle ett mer komplext program kan du utforska slutpunkt till slutpunkt-transaktion till nivå i en enskild händelse anropsstacken.

     ![Skärmbild av transaktionsgränssnitt som slutpunkt till slutpunkt](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>OpenCensus för Python

* [Anpassning](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask-integrering](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django Integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL Integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Nästa steg

* [OpenCensus Python på GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Programkarta](./../../azure-monitor/app/app-map.md)
* [Prestandaövervakning för slutpunkt till slutpunkt](./../../azure-monitor/learn/tutorial-performance.md)