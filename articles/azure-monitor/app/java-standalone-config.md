---
title: Övervaka Java-program var som helst – Azure Monitor Application Insights
description: Kodlös programprestandaövervakning för Java-program som körs i alla miljöer utan att instrumentera appen. Hitta orsaken till problemen d med distribuerad spårning och programmappning.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641893"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurationsalternativ - Java-fristående agent för Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Anslutningssträng och rollnamn

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

Anslutningssträngen krävs och rollnamnet är viktigt när du skickar data från olika program till samma Application Insights-resurs.

Du hittar mer information och ytterligare konfigurationsalternativ nedan för mer information.

## <a name="configuration-file-path"></a>Sökväg till konfigurationsfil

Som standard förväntar sig Application Insights Java 3.0 `ApplicationInsights.json`Preview att konfigurationsfilen ska `applicationinsights-agent-3.0.0-PREVIEW.jar`namnges och finnas i samma katalog som .

Du kan ange en egen sökväg till konfigurationsfilen med antingen

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`miljövariabeln, eller
* `applicationinsights.configurationFile`Egenskapen Java-system

Om du anger en relativ sökväg matchas `applicationinsights-agent-3.0.0-PREVIEW.jar` den i förhållande till katalogen där den finns.

## <a name="connection-string"></a>Anslutningssträng

Detta krävs. Du hittar din anslutningssträng i din Application Insights-resurs:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Anslutningssträng för programinsikter":::

Du kan också ställa in anslutningssträngen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_CONNECTION_STRING`.

## <a name="cloud-role-name"></a>Namn på molnroll

Cloud-rollnamn används för att märka komponenten på programmappningen.

Om du vill ange molnet rollnamn:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Om molnrollnamnet inte har angetts används application insights-resursens namn för att märka komponenten på programmappningen.

Du kan också ange molnet rollnamnet `APPLICATIONINSIGHTS_ROLE_NAME`med hjälp av miljövariabeln .

## <a name="cloud-role-instance"></a>Molnrollinstans

Molnrollinstansen är som standard datornamnet.

Om du vill ställa in molnrollförekomsten till något annat än datornamnet:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Du kan också ange molnrollinstansen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_ROLE_INSTANCE`.

## <a name="application-log-capture"></a>Insamling av programlogg

Application Insights Java 3.0 Preview fångar automatiskt programloggning via Log4j, Logback och java.util.logging.

Som standard kommer det att fånga `WARN` all loggning som utförs på nivå eller högre.

Om du vill ändra det här tröskelvärdet:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Det här `threshold` är de giltiga värden `ApplicationInsights.json` som du kan ange i filen och hur de motsvarar loggningsnivåer över olika loggningsramverk:

| `threshold`  | Log4j (på andra sätt)  | Logga tillbaka | Jul     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| Dödlig        | Dödlig  | FEL   | SEVERE  |
| FEL/ALLVARLIGT | FEL  | FEL   | SEVERE  |
| VARNA/VARNING | Varna   | Varna    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | FELSÖK  | FELSÖK   | CONFIG  |
| FELSÖKA/FIN   | FELSÖK  | FELSÖK   | FINE    |
| FINER        | FELSÖK  | FELSÖK   | FINER   |
| SPÅR/FINASTE | Spåra  | Spåra   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX-mått

Om du har några JMX-mått som du är intresserad av att fånga:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Micrometer

Som standard, om ditt program använder [Micrometer](https://micrometer.io), Application Insights 3.0 (börjar med Preview.2) lägger nu till Micrometer globala registret och fångar Micrometer mått.

Om du vill inaktivera den här funktionen:

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

Som standard skickar Application Insights Java 3.0 Preview ett pulsslagsmått en gång var 15:e minut. Om du använder pulsslagsmåttet för att utlösa aviseringar kan du öka frekvensen för det här pulsslaget:

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
> Du kan inte minska frekvensen för dessa pulsslag, eftersom pulsslagsdata också används för att spåra användning av Application Insights.

## <a name="sampling"></a>Samling

Sampling är användbart om du behöver minska kostnaderna.
Sampling utförs som en funktion på operations-ID (kallas även spår-ID), så att samma operations-ID alltid resulterar i samma provtagningsbeslut. Detta säkerställer att du inte får delar av en distribuerad transaktion som samplas i medan andra delar av den samplas ut.

Om du till exempel anger sampling till 10 % ser du bara 10 % av dina transaktioner, men var och en av dessa 10 % har fullständig transaktionsinformation från slutna till slutna dagar.

Här är ett exempel på hur du ställer in samplingen till **10% av alla transaktioner** - se till att du ställer in samplingsfrekvensen som är korrekt för ditt användningsfall:

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

## <a name="http-proxy"></a>HTTP-proxy

Om ditt program ligger bakom en brandvägg och inte kan ansluta direkt till Application Insights (se [IP-adresser som används av Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)kan du konfigurera Application Insights Java 3.0 Preview för att använda en HTTP-proxy:

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

## <a name="self-diagnostics"></a>Självdiagnostik

"Självdiagnostik" avser intern loggning från Application Insights Java 3.0 Preview.

Detta kan vara till hjälp för att upptäcka och diagnostisera problem med Application Insights själv.

Som standard loggas den till `warn`konsol med nivå , vilket motsvarar den här konfigurationen:

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

Giltiga nivåer `OFF` `ERROR`är `WARN` `INFO`, `DEBUG`, `TRACE`, , och .

Om du vill logga till en fil i stället för att logga till konsolen:

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

När du använder filloggning, `maxSizeMB`när filen träffar , kommer det att rulla över, hålla bara den senast avslutade loggfilen utöver den aktuella loggfilen.
