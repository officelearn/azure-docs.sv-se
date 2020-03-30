---
title: Skicka data från Windows Azure-diagnostiktillägg till Azure Event Hubs
description: Konfigurera diagnostiktillägg i Azure Monitor för att skicka data till Azure Event Hub så att du kan vidarebefordra det till platser utanför Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672539"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Skicka data från Windows Azure-diagnostiktillägg till Azure Event Hubs
Azure Diagnostics-tillägget är en agent i Azure Monitor som samlar in övervakningsdata från gästoperativsystemet och arbetsbelastningar för virtuella Azure-datorer och andra beräkningsresurser. I den här artikeln beskrivs hur du skickar data från Windows Azure Diagnostic-tillägget (WAD) till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) så att du kan vidarebefordra till platser utanför Azure.

## <a name="supported-data"></a>Data som stöds

De data som samlas in från gästoperativsystemet som kan skickas till Event Hubs innehåller följande. Andra datakällor som samlas in av WAD, inklusive IIS-loggar och kraschdumpar, kan inte skickas till Event Hubs.

* ETW-händelser (Event Tracing for Windows)
* Prestandaräknare
* Windows-händelseloggar, inklusive programloggar i Windows-händelseloggen
* Azure Diagnostics-infrastrukturloggar

## <a name="prerequisites"></a>Krav

* Windows diagnostiktillägg 1.6 eller senare. Se [Azure Diagnostics-schemaversioner för tilläggskonfiguration och historik](diagnostics-extension-versions.md) för en versionshistorik och [Azure Diagnostics-tilläggsöversikt](diagnostics-extension-overview.md) för resurser som stöds.
* Namnområdet Event Hubs måste alltid etableras. Mer information [finns i Komma igång med eventhubbar.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>Konfigurationsschema
Se [Installera och konfigurera Windows Azure diagnostics extension (WAD)](diagnostics-extension-windows-install.md) för olika alternativ för att aktivera och konfigurera [konfigurationsschemat](diagnostics-extension-schema-windows.md) för diagnostiktillägg och Azure Diagnostics för en referens till konfigurationsschemat. I resten av den här artikeln beskrivs hur du använder den här konfigurationen för att skicka data till en händelsehubb. 

Azure Diagnostics skickar alltid loggar och mått till ett Azure Storage-konto. Du kan konfigurera en eller flera *datamottagare* som skickar data till ytterligare platser. Varje mottagare definieras i [sinksConfig-elementet](diagnostics-extension-schema-windows.md#sinksconfig-element) i den offentliga konfigurationen med känslig information i den privata konfigurationen. Den här konfigurationen för händelsehubbar använder värdena i följande tabell.

| Egenskap | Beskrivning |
|:---|:---|
| Namn | Beskrivande namn för diskbänken. Används i konfigurationen för att ange vilka datakällor som ska skickas till diskhon. |
| URL  | Url till händelsehubben \<i formulärhändelse-hubbar-namnområdet\>\<.servicebus.windows.net/ händelse-hubb-namn\>.          |
| SharedAccessKeyName | Namn på en princip för delad åtkomst **Send** för händelsehubben som har minst Skicka-behörighet. |
| SharedAccessKey     | Primär eller sekundär nyckel från principen för delad åtkomst för händelsehubben. |

Exempel på offentliga och privata konfigurationer visas nedan. Detta är en minimal konfiguration med en enda prestandaräknare och händelselogg för att illustrera hur du konfigurerar och använder händelsehubbdatamottagaren. Se [Azure Diagnostics konfigurationsschema](diagnostics-extension-schema-windows.md) för ett mer komplext exempel.

### <a name="public-configuration"></a>Offentlig konfiguration

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Privat konfiguration

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Konfigurationsalternativ
Om du vill skicka data till en datamottagare anger du **attributet sinks** på datakällans nod. Var du **placerar attributet sinks** bestämmer tilldelningens omfattning. I följande exempel definieras **attributet sinks** till noden **PerformanceCounters,** vilket gör att alla underordnade prestandaräknare skickas till händelsehubben.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


I följande exempel tillämpas **attributet sinks** direkt på tre räknare som gör att endast dessa prestandaräknare skickas till händelsehubben. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Validera konfiguration
Du kan använda en mängd olika metoder för att verifiera att data skickas till händelsehubben. ne enkel metod är att använda Händelsehubbar fånga som beskrivs i [Capture händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Felsöka sänkor för händelsehubbar

- Titta på Azure Storage-tabellen **WADDiagnosticInfrastructureLogsTable** som innehåller loggar och fel för Azure Diagnostics själv. Ett alternativ är att använda ett verktyg som [Azure Storage Explorer](https://www.storageexplorer.com) för att ansluta till det här lagringskontot, visa den här tabellen och lägga till en fråga för Tidsstämpel under de senaste 24 timmarna. Du kan använda verktyget för att exportera en CSV-fil och öppna den i ett program som Microsoft Excel. Excel gör det enkelt att söka efter telefonkortssträngar, till exempel **EventHubs,** för att se vilket fel som rapporteras.  

- Kontrollera att händelsehubben har etablerats. All anslutningsinformation i avsnittet **PrivateConfig** i konfigurationen måste matcha värdena för din resurs som visas i portalen. Kontrollera att du har definierat en SAS-princip *(SendRule* i exemplet) i portalen och att *Send-behörighet* beviljas.  

## <a name="next-steps"></a>Nästa steg

* [Översikt över Event Hubs](../../event-hubs/event-hubs-about.md)
* [Skapa en händelsehubb](../../event-hubs/event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



