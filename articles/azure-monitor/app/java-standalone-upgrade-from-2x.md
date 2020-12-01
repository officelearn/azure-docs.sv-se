---
title: Uppgradera från 2. x-Azure Monitor Application Insights Java
description: Uppgradera från Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355136"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Uppgradera från Application Insights Java SDK 2. x

Om du redan använder Application Insights Java SDK 2. x i ditt program behöver du inte ta bort den.
Java 3,0-agenten identifierar den och samlar in och korrelerar en anpassad telemetri som du skickar via Java SDK 2. x, samtidigt som du undertrycker automatisk insamling som utförs av Java SDK 2. x för att förhindra duplicerad telemetri.

Om du använde Application Insights 2. x-agenten måste du ta bort det `-javaagent:` JVM-arg som pekade på 2. x-agenten.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers och TelemetryProcessors

Java SDK 2. x TelemetryInitializers och TelemetryProcessors kommer inte att köras när du använder 3,0-agenten.
Många av de användnings fall som tidigare krävdes kan lösas i 3,0 genom att konfigurera [anpassade dimensioner](./java-standalone-config.md#custom-dimensions) eller konfigurera [telemetri-processorer](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Flera program i en enda JVM

För närvarande stöder 3,0 bara en enda [anslutnings sträng och roll namn](./java-standalone-config.md#connection-string-and-role-name) per process som körs. I synnerhet kan du inte ha flera Tomcat-webbappar i samma Tomcat-distribution som använder olika anslutnings strängar eller olika roll namn ännu.

## <a name="http-request-telemetry-names"></a>Namn på telemetri för HTTP-begäran

Namn på telemetri för HTTP-begäran i 3,0 har ändrats till allmänt sett en bättre sammanställd vy i Application Insights portalen U/X.

För vissa program kan du dock fortfarande föredra den sammanställda vyn i U/X som tillhandahölls av de tidigare telemetridata, och i så fall kan du använda funktionen för förhands granskning av telemetri i 3,0 för att återgå till föregående namn.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>För att prefix namnet på telemetri med HTTP-metoden ( `GET` , `POST` osv.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Ange telemetri-namnet till den fullständiga URL-sökvägen

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
