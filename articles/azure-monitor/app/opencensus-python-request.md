---
title: Spårning av inkommande begär anden i Azure Application insikter med python-räkning python | Microsoft Docs
description: Övervaka begär ande anrop för dina python-appar via python-räkningar.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: c94bc949f13ee19a9d2150c9d3c1b6a2bdb959b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850074"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Spåra inkommande begär Anden med openräkningar python

Inkommande begär ande data samlas in med hjälp av openräkningar python och dess olika integreringar. Spåra inkommande begär ande data som skickas till dina webb program som skapats ovanpå de populära webb ramverken `django` `flask` och `pyramid` . Data skickas sedan till Application Insights under Azure Monitor som `requests` telemetri.

Börja med att Instrumenta din python-app med senaste [Openräkning python SDK](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Spåra django-program

1. Hämta och installera `opencensus-ext-django` från [pypi](https://pypi.org/project/opencensus-ext-django/) och instrumentera ditt program med `django` mellanprogram. Inkommande begär Anden som skickas till ditt `django` program spåras.

2. Inkludera `opencensus.ext.django.middleware.OpencensusMiddleware` i `settings.py` filen under `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Kontrol lera att AzureExporter har kon figurer ATS korrekt i `settings.py` under `OPENCENSUS` . För förfrågningar från URL: er som du inte vill spåra lägger du till dem i `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Spåra program i kolven

1. Hämta och installera `opencensus-ext-flask` från [pypi](https://pypi.org/project/opencensus-ext-flask/) och instrumentera ditt program med `flask` mellanprogram. Inkommande begär Anden som skickas till ditt `flask` program spåras.

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

2. Du kan också konfigurera ditt `flask` program genom `app.config` . För förfrågningar från URL: er som du inte vill spåra lägger du till dem i `BLACKLIST_PATHS` .

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

## <a name="tracking-pyramid-applications"></a>Spåra program i pyramiden

1. Hämta och installera `opencensus-ext-django` från [pypi](https://pypi.org/project/opencensus-ext-pyramid/) och instrumentera ditt program med `pyramid` interpoleringen. Inkommande begär Anden som skickas till ditt `pyramid` program spåras.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Du kan konfigurera `pyramid` interpoleringen direkt i koden. För förfrågningar från URL: er som du inte vill spåra lägger du till dem i `BLACKLIST_PATHS` .

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

* [Programkarta](./app-map.md)
* [Tillgänglighet](./monitor-web-app-availability.md)
* [Sök](./diagnostic-search.md)
* [Logg (analys) fråga](../log-query/log-query-overview.md)
* [Transaktionsdiagnostik](./transaction-diagnostics.md)

