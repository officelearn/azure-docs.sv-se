---
title: Övervaka Java-program var som helst Azure Monitor Application Insights
description: Kod för program prestanda övervakning för Java-program som körs i en miljö utan att du instrumenterar appen. Hitta rotor saken till problemen d med hjälp av Distributed tracing och program karta.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9b90f8b9336111438b4b832d557d448470959255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537665"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurations alternativ – Java fristående agent för Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Anslutnings sträng och rollnamn

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

Anslutnings strängen krävs och roll namnet är viktigt när du skickar data från olika program till samma Application Insights-resurs.

Du hittar mer information och ytterligare konfigurations alternativ nedan för mer information.

## <a name="configuration-file-path"></a>Sökväg till konfigurations fil

Application Insights Java 3,0 Preview förväntar sig som standard konfigurations filen som ska namnges `ApplicationInsights.json` och placeras i samma katalog som `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Du kan ange en egen sökväg för konfigurations filen med antingen

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` miljö variabel eller
* `applicationinsights.configurationFile` Java system egenskap

Om du anger en relativ sökväg kommer den att matchas i förhållande till den katalog där `applicationinsights-agent-3.0.0-PREVIEW.5.jar` finns.

## <a name="connection-string"></a>Anslutningssträng

Detta är obligatoriskt. Du kan hitta din anslutnings sträng i Application Insights-resursen:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights anslutnings sträng":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Du kan också ange anslutnings strängen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Om du inte anger anslutnings strängen inaktive ras Java-agenten.

## <a name="cloud-role-name"></a>Namn på moln roll

Namnet på moln rollen används för att märka komponenten på program kartan.

Om du vill ange namnet på moln rollen:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Om du inte anger namnet på moln rollen används Application Insights resursens namn för att märka komponenten på program kartan.

Du kan också ange namnet på moln rollen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Moln roll instans

Moln roll instansen har som standard dator namnet.

Om du vill ställa in en annan moln roll instans i stället för namnet på datorn:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Du kan också ställa in moln Rolls instansen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Samla in program logg

Application Insights Java 3,0 Preview samlar automatiskt in program loggning via log4j, logback och Java. util. logging.

Som standard kommer den att fånga all loggning som utförs på `INFO` nivån eller över.

Om du vill ändra detta tröskelvärde:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

Du kan också ange loggnings tröskeln med hjälp av miljövariabeln `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` .

Dessa är giltiga `threshold` värden som du kan ange i `ApplicationInsights.json` filen och hur de motsvarar loggnings nivåer över olika loggnings ramverk:

| tröskel värde   | Log4j  | Logback | Jul     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| DÖDS             | DÖDS  | ERROR   | SEVERE  |
| FEL (eller allvarligt) | ERROR  | ERROR   | SEVERE  |
| Varna (eller varning) | Varna   | Varna    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | FELSÖK  | FELSÖK   | CONFIG  |
| FELSÖKA (eller fint)   | FELSÖK  | FELSÖK   | FINE    |
| FINER             | FELSÖK  | FELSÖK   | FINER   |
| TRACE (eller FINEST) | Rita  | Rita   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX mått

Om du har några JMX-mått som du är intresse rad av att fånga:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Du kan också ange JMX mått med hjälp av miljövariabeln `APPLICATIONINSIGHTS_JMX_METRICS` .

Detta miljö variabel innehåll måste vara JSON-data som matchar ovanstående struktur, t. ex. `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Metaspace", "attribute": "Usage.used", "display": "MetaSpace Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (inklusive mått från våren Boot-motstånd)

Om ditt program använder [micrometer](https://micrometer.io), Application Insights 3,0 (från och med för hands version. 2) samlar nu in mått som skickas till det globala registret för micrometer.

Om programmet använder [våren Boot-motstånd](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)Application Insights 3,0 (från och med för hands version. 4) samlar nu in mått som kon figurer ATS av våren Boot Boot-motstånd (som använder micrometer, men använder inte det globala micrometer-registret).

Om du vill inaktivera dessa funktioner:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Pulsslag

Som standard skickar Application Insights Java 3,0 för hands version ett pulsslags mått var 15: e minut. Om du använder pulsslags måttet för att utlösa aviseringar kan du öka frekvensen för detta pulsslag:

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> Du kan inte minska frekvensen för det här pulsslaget eftersom pulsslags data också används för att spåra Application Insights användning.

## <a name="sampling"></a>Samling

Sampling är användbart om du behöver minska kostnaderna.
Sampling utförs som en funktion i åtgärds-ID (även kallat spårnings-ID), så att samma åtgärds-ID alltid kommer att resultera i samma samplings beslut. På så sätt kan du inte hämta delar av en distribuerad transaktion, medan andra delar av det samplas ut.

Om du till exempel ställer in sampling till 10% visas bara 10% av dina transaktioner, men var och en av de 10% har fullständig transaktions information från slut punkt till slut punkt.

Här är ett exempel på hur du ställer in samplingen på **10% av alla transaktioner** . kontrol lera att du ställer in den samplings frekvens som är korrekt för ditt användnings fall:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

Du kan också ange samplings procenten med hjälp av miljövariabeln `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

## <a name="http-proxy"></a>HTTP-proxy

Om ditt program ligger bakom en brand vägg och inte kan ansluta direkt till Application Insights (se [IP-adresser som används av Application Insights](./ip-addresses.md)) kan du konfigurera Application Insights Java 3,0 Preview för att använda en http-proxy:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Själv diagnostik

"Självdiagnostik" syftar på intern loggning från Application Insights Java 3,0 Preview.

Detta kan vara användbart för att upptäcka och diagnostisera problem med Application Insights sig själv.

Som standard loggar den till-konsolen med nivån `warn` , som motsvarar den här konfigurationen:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

Giltiga nivåer är,,,, `OFF` `ERROR` `WARN` `INFO` `DEBUG` och `TRACE` .

Om du vill logga till en fil i stället för att logga in på konsolen:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

När fil träffar används när fil loggen används, `maxSizeMB` kommer den att förnyas så att bara den senast slutförda logg filen visas, utöver den aktuella logg filen.
