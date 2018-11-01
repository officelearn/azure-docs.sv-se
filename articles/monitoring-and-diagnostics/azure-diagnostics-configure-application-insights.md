---
title: Konfigurera Azure Diagnostics för att skicka data till Application Insights
description: Uppdatera Azure Diagnostics offentliga konfigurationen för att skicka data till Application Insights.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: f2a48756252f538adc7d813aafe6dbfbf3a270b5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416701"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Skicka molntjänst, virtuell dator eller Service Fabric diagnostiska data till Application Insights
Molntjänster, virtuella datorer, Virtual Machine Scale Sets och Service Fabric alla Använd Azure Diagnostics-tillägget för att samla in data.  Azure-diagnostik skickar data till Azure Storage-tabeller.  Du kan dock också pipe alla eller en delmängd av data till andra platser med hjälp av Azure-diagnostiktillägget 1.5 eller senare.

Den här artikeln beskriver hur du skickar data från Azure Diagnostics-tillägget till Application Insights.

## <a name="diagnostics-configuration-explained"></a>Diagnostiky förklaras
Azure-diagnostiktillägget 1.5 introduceras egenskaperna som är ytterligare platser där du kan skicka diagnostikdata.

Exempel konfigurationen av en mottagare för Application Insights:

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
- Den **mottagare** *namn* attributet är ett strängvärde som unikt identifierar mottagaren.

- Den **ApplicationInsights** elementet anger instrumenteringsnyckeln för Application insights-resurs där Azure diagnostics-data skickas.
    - Om du inte har en befintlig Application Insights-resurs kan du läsa [skapa en ny Application Insights-resurs](../application-insights/app-insights-create-new-resource.md) mer information om att skapa en resurs och komma instrumenteringsnyckeln.
    - Om du utvecklar en molntjänst med Azure SDK 2.8 och senare, fylls instrumentationsnyckeln i automatiskt. Värdet är baserad på den **APPINSIGHTS_INSTRUMENTATIONKEY** konfigurationsinställning för tjänst när paketering Cloud Service-projekt. Se [använda Application Insights med molntjänster](../application-insights/app-insights-cloudservices.md).

- Den **kanaler** elementet innehåller ett eller flera **kanal** element.
    - Den *namn* attributet unikt refererar till kanalen.
    - Den *loglevel* attribut kan du ange den Loggnivå som gör att kanalen. De tillgängliga loggningsnivåerna i ordningen för de flesta till minst information är:
        - Utförlig
        - Information
        - Varning
        - Fel
        - Kritisk

En kanal fungerar som ett filter och kan du välja specifika loggningsnivåerna att skicka till mål-mottagare. Du kan till exempel samla in detaljerade loggarna och skicka dem till lagring, men skicka endast fel till mottagaren.

Följande bild visar den här relationen.

![Offentliga Diagnostiky](media/azure-diagnostics-configure-application-insights/AzDiag_Channels_App_Insights.png)

Följande bild sammanfattas konfigurationsvärdena och hur de fungerar. Du kan inkludera flera mottagare i konfigurationen på olika nivåer i hierarkin. Mottagare på den översta nivån som fungerar som en global inställning och den som angetts vid enskilda element fungerar som en åsidosättning av den globala inställningen.

![Diagnostik egenskaperna med Application Insights](media/azure-diagnostics-configure-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Konfigurationsexempel för fullständig mottagare
Här är exempel på hur offentliga filen som
1. skickar alla fel till Application Insights (angivna på den **DiagnosticMonitorConfiguration** nod)
2. skickar också utförlig nivå loggar för programloggarna (angivna på den **loggar** nod).

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
I den tidigare konfigurationen med följande rader betydelser:

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

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Skicka endast felloggarna till Application Insights-mottagare

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Skicka utförliga programloggar till Application Insights

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

- **Kanaler bara logga typ och inte prestandaräknare.** Om du anger en kanal med ett prestandaräknaren element ignoreras.
- **Loggningsnivå för en kanal får inte överskrida loggningsnivån för vad som samlas in av Azure-diagnostik.** Exempelvis kan du kan inte samla in programmet logga fel i loggarna elementet och försöker skicka utförliga loggar till Application Insights-mottagare. Den *scheduledTransferLogLevelFilter* attributet måste alltid samla in lika eller mer loggar än loggarna du vill skicka till en mottagare.
- **Du kan inte skicka blob-data som samlas in av Azure diagnostics-tillägg till Application Insights.** Till exempel något som anges under den *kataloger* noden. För kraschdumpar faktiska kraschdumpen skickas till blob storage och bara ett meddelande om att kraschdumpen har genererats skickas till Application Insights.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [se information om din Azure-diagnostik](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) i Application Insights.
* Använd [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) att aktivera Azure diagnostics-tillägg för ditt program.
* Använd [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) att aktivera Azure diagnostics-tillägg för ditt program
