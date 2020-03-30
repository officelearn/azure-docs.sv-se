---
title: Spårning av inkommande begäran i Azure Application Insights med OpenCensus Python | Microsoft-dokument
description: Övervaka begärandenrop för dina Python-appar via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669955"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Spåra inkommande begäranden med OpenCensus Python

Inkommande begäran data samlas in med OpenCensus Python och dess olika integrationer. Spåra inkommande begäran data som skickas till dina webbprogram `django` `flask` bygger `pyramid`på de populära webbramverk , och . Data skickas sedan till Application Insights `requests` under Azure Monitor som telemetri.

Först instrumentera ditt Python-program med senaste [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Spåra Django applikationer

1. Ladda ner `opencensus-ext-django` och installera från [PyPI](https://pypi.org/project/opencensus-ext-django/) och instrument ditt program med `django` middleware. Inkommande förfrågningar som `django` skickas till din ansökan kommer att spåras.

2. Inkludera `opencensus.ext.django.middleware.OpencensusMiddleware` i `settings.py` filen `MIDDLEWARE`under .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Kontrollera att AzureExporter är korrekt `settings.py` `OPENCENSUS`konfigurerat i under .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. Du kan också lägga `settings.py` `BLACKLIST_PATHS` till webbadresser till under för förfrågningar som du inte vill spåra.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Spåra flaskapplikationer

1. Ladda ner `opencensus-ext-flask` och installera från [PyPI](https://pypi.org/project/opencensus-ext-flask/) och instrument ditt program med `flask` middleware. Inkommande förfrågningar som `flask` skickas till din ansökan kommer att spåras.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Du kan `flask` konfigurera din middleware direkt i koden. För förfrågningar från webbadresser som du inte vill `BLACKLIST_PATHS`spåra, lägg till dem i .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Spåra pyramidprogram

1. Ladda ner `opencensus-ext-django` och installera från [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) och instrument ditt program med `pyramid` interpolering. Inkommande förfrågningar som `pyramid` skickas till din ansökan kommer att spåras.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Du kan `pyramid` konfigurera interpolering direkt i koden. För förfrågningar från webbadresser som du inte vill `BLACKLIST_PATHS`spåra, lägg till dem i .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Nästa steg

* [Programkarta](../../azure-monitor/app/app-map.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
* [Sök](../../azure-monitor/app/diagnostic-search.md)
* [Loggfråga (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Transaktionsdiagnostik](../../azure-monitor/app/transaction-diagnostics.md)
