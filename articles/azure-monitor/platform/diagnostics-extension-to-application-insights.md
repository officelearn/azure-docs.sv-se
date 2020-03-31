---
title: Skicka Azure Diagnostics-data till Application Insights
description: Uppdatera den offentliga konfigurationen för Azure Diagnostics för att skicka data till Application Insights.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 80d971abd248ca8253a374b488c693ea9aa2ea3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672335"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Skicka diagnostikdata för molntjänst, virtuell dator eller tjänstinfrastruktur till Application Insights
Molntjänster, virtuella datorer, skalningsuppsättningar för virtuella datorer och serviceinfrastruktur använder alla Azure Diagnostics-tillägget för att samla in data.  Azure-diagnostik skickar data till Azure Storage-tabeller.  Du kan dock också leda alla eller en delmängd av data till andra platser med Azure Diagnostics-tillägget 1.5 eller senare.

I den här artikeln beskrivs hur du skickar data från Azure Diagnostics-tillägget till Application Insights.

## <a name="diagnostics-configuration-explained"></a>Diagnostikkonfiguration förklaras
Azure diagnostics-tillägget 1.5 introducerade sänkor, som är ytterligare platser där du kan skicka diagnostikdata.

Exempel på konfiguration av en diskho för Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- Attributet **Sink** *Sink-namn* är ett strängvärde som unikt identifierar diskhon.

- **ApplicationInsights-elementet** anger instrumenteringsnyckel för application insights-resursen där Azure-diagnostikdata skickas.
    - Om du inte har en befintlig Application Insights-resurs läser du [Skapa en ny Application Insights-resurs](../../azure-monitor/app/create-new-resource.md ) för mer information om hur du skapar en resurs och hämtar instrumenteringsnyckeln.
    - Om du utvecklar en molntjänst med Azure SDK 2.8 och senare fylls den här instrumenteringsnyckeln automatiskt i. Värdet baseras på konfigurationsinställningen **för APPINSIGHTS_INSTRUMENTATIONKEY** tjänst när cloud service-projektet paketerars. Se [Använda programstatistik med Molntjänster](../../azure-monitor/app/cloudservices.md).

- Elementet **Kanaler** innehåller ett eller flera **kanalelement.**
    - *Namnattributet* refererar unikt till den kanalen.
    - Med *attributet loglevel* kan du ange den loggnivå som kanalen tillåter. De tillgängliga loggnivåerna i ordning efter de flesta till minst information är:
        - Verbose
        - Information
        - Varning
        - Fel
        - Kritisk

En kanal fungerar som ett filter och låter dig välja specifika loggnivåer som ska skickas till målmottagaren. Du kan till exempel samla in utförliga loggar och skicka dem till lagring, men skicka bara fel till diskhon.

Följande bild visar den här relationen.

![Offentlig konfiguration för diagnostik](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Följande bild sammanfattar konfigurationsvärdena och hur de fungerar. Du kan inkludera flera diskhoar i konfigurationen på olika nivåer i hierarkin. Diskbänken på den översta nivån fungerar som en global inställning och den som anges vid det enskilda elementet fungerar som en åsidosättning till den globala inställningen.

![Diagnostikmottagare konfiguration med application insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Fullständigt exempel på sink-konfiguration
Här är ett komplett exempel på den offentliga konfigurationsfilen som
1. skickar alla fel till Application Insights (anges på **noden DiagnosticMonitorConfiguration)**
2. skickar också detaljerade nivåloggar för programloggarna (som anges vid noden **Loggar).**

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
I föregående konfiguration har följande rader följande betydelser:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Skicka alla data som samlas in av Azure-diagnostik

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Skicka endast felloggar till programstatistikmottagaren

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Skicka detaljerade programloggar till Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Begränsningar

- **Endast kanaler loggar typ och inte prestandaräknare.** Om du anger en kanal med ett prestandaräknareelement ignoreras den.
- **Loggnivån för en kanal får inte överskrida loggnivån för det som samlas in av Azure-diagnostik.** Du kan till exempel inte samla in programloggfel i elementet Loggar och försöka skicka utförliga loggar till programinsiktsmottagaren. Attributet *scheduledTransferLogLevelFilter* måste alltid samla in lika med eller fler loggar än de loggar som du försöker skicka till en mottagare.
- **Du kan inte skicka blob-data som samlas in av Azure Diagnostics-tillägget till Application Insights.** Till exempel allt som anges under noden *Kataloger.* För kraschdumpar skickas den faktiska kraschdumpen till blob-lagring och endast ett meddelande om att kraschdumpen genererades skickas till Application Insights.

## <a name="next-steps"></a>Efterföljande moment
* Lär dig hur du [visar din Azure-diagnostikinformation](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) i Application Insights.
* Använd [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) för att aktivera Azure Diagnostics-tillägget för ditt program.
* Använda [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) för att aktivera Azure Diagnostics-tillägget för ditt program

