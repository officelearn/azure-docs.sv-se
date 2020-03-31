---
title: Beroendespårning i Azure Application Insights med OpenCensus Python | Microsoft-dokument
description: Övervaka beroendeanrop för dina Python-appar via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669938"
---
# <a name="track-dependencies-with-opencensus-python"></a>Spåra beroenden med OpenCensus Python

Ett beroende är en extern komponent som anropas av ditt program. Beroendedata samlas in med OpenCensus Python och dess olika integrationer. Data skickas sedan till Application Insights `dependencies` under Azure Monitor som telemetri.

Först instrumentera ditt Python-program med senaste [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Beroenden i processen

OpenCensus Python SDK för Azure Monitor kan du skicka "i-process" beroende telemetri (information och logik som uppstår i ditt program). Beroenden i processen har `type` fältet `INPROC` som i analyser.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Beroenden med "begäranden" integration

Spåra dina utgående begäranden med `requests` OpenCensus-integreringen.

Hämta och `opencensus-ext-requests` installera från [PyPI](https://pypi.org/project/opencensus-ext-requests/) och lägg till det i spårningsintegrationerna. Begäranden som skickas med biblioteket [python-begäranden](https://pypi.org/project/requests/) spåras.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Beroenden med "httplib"-integrering

Spåra dina utgående begäranden `httplib` med OpenCensus-integrering.

Hämta och `opencensus-ext-httplib` installera från [PyPI](https://pypi.org/project/opencensus-ext-httplib/) och lägg till det i spårningsintegrationerna. Begäranden som skickas med [http.client](https://docs.python.org/3.7/library/http.client.html) för Python3 eller [httplib](https://docs.python.org/2/library/httplib.html) för Python2 spåras.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Beroenden med "django" integration

Spåra dina utgående Django-förfrågningar med `django` OpenCensus-integreringen.

Ladda ner `opencensus-ext-django` och installera från [PyPI](https://pypi.org/project/opencensus-ext-django/) `MIDDLEWARE` och lägg till `settings.py` följande rad i avsnittet i Django-filen.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Ytterligare konfiguration kan tillhandahållas, läsa [anpassningar](https://github.com/census-instrumentation/opencensus-python#customization) för en fullständig referens.

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

## <a name="dependencies-with-mysql-integration"></a>Beroenden med "mysql"-integrering

Spåra dina MYSQL-beroenden med OpenCensus-integreringen. `mysql` Den här integreringen stöder [mysql-connector-biblioteket.](https://pypi.org/project/mysql-connector-python/)

Hämta och `opencensus-ext-mysql` installera från [PyPI](https://pypi.org/project/opencensus-ext-mysql/) och lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Beroenden med "pymysql" integration

Spåra dina PyMySQL-beroenden med OpenCensus-integreringen. `pymysql`

Hämta och `opencensus-ext-pymysql` installera från [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) och lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Beroenden med "postgresql"-integrering

Spåra dina PostgreSQL-beroenden med OpenCensus-integreringen. `postgresql` Den här integreringen stöder [psycopg2-biblioteket.](https://pypi.org/project/psycopg2/)

Hämta och `opencensus-ext-postgresql` installera från [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) och lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Beroenden med "pymongo"-integration

Spåra dina MongoDB-beroenden med OpenCensus-integreringen. `pymongo` Denna integration stöder [pymongo](https://pypi.org/project/pymongo/) biblioteket.

Hämta och `opencensus-ext-pymongo` installera från [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) och lägg till följande rader i koden.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Beroenden med "sqlalchemy"-integrering

Spåra dina beroenden med SQLAlchemy med `sqlalchemy` OpenCensus-integrering. Den här integreringen spårar användningen av [sqlalchemy-paketet,](https://pypi.org/project/SQLAlchemy/) oavsett den underliggande databasen.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Nästa steg

* [Programkarta](../../azure-monitor/app/app-map.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
* [Sök](../../azure-monitor/app/diagnostic-search.md)
* [Loggfråga (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Transaktionsdiagnostik](../../azure-monitor/app/transaction-diagnostics.md)
