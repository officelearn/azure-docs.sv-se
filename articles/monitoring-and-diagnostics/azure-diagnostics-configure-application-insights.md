---
title: "Konfigurera Azure-diagnostik för att skicka data till Application Insights | Microsoft Docs"
description: "Uppdatera Azure-diagnostik offentliga konfiguration för att skicka data till Application Insights."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2016
ms.author: robb
ms.openlocfilehash: 67dc2d5bbfa2012e4e098616edda593d023c4c1e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Skicka Cloud Service, virtuell dator eller Service Fabric diagnostikdata till Application Insights
Molntjänster, virtuella datorer, virtuella datorer och Service Fabric alla filnamnstillägget Azure Diagnostics att samla in data.  Azure diagnostics skickar data till Azure Storage-tabeller.  Du kan dock också pipe alla eller en delmängd av data till andra platser med hjälp av Azure-diagnostik tillägget 1.5 eller senare.

Den här artikeln beskriver hur du skickar data från Azure-diagnostik-tillägg till Application Insights.

## <a name="diagnostics-configuration-explained"></a>Diagnostik-konfiguration beskrivs
Azure diagnostics tillägget 1.5 introduceras egenskaperna som är ytterligare platser där du kan skicka diagnostikdata.

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
- Den **Sink** *namn* attributet är ett strängvärde som unikt identifierar sink.

- Den **ApplicationInsights** element anger instrumentation nyckeln för Application insights-resurs där Azure diagnostikdata skickas.
    - Om du inte har en befintlig Application Insights-resurs, se [skapar en ny Application Insights-resurs](../application-insights/app-insights-create-new-resource.md) mer information om att skapa en resurs och hämtar nyckeln instrumentation.
    - Om du utvecklar en tjänst i molnet med Azure SDK 2.8 och senare fylls instrumentation nyckeln i automatiskt. Värdet är baserad på den **APPINSIGHTS_INSTRUMENTATIONKEY** konfigurationsinställning för tjänst när paketera Cloud Service-projekt. Se [Programinsikter för användning med Azure-diagnostik för att felsöka problem med Cloud Service](../cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

- Den **kanaler** elementet innehåller ett eller flera **kanal** element.
    - Den *namn* attributet unikt refererar till kanalen.
    - Den *loglevel* attribut kan du ange den Loggnivå som kanalen tillåter. Tillgängliga nivåer i den ordning de mest till minst information är:
        - Utförlig
        - Information
        - Varning
        - Fel
        - kritiska

En kanal som fungerar som ett filter och kan du välja specifika loggningsnivåer att skicka till mål-mottagare. Du kan till exempel samla in detaljerade loggarna och skicka dem till lagring, men skicka endast fel till sink.

Följande bild visar den här relationen.

![Diagnostik offentliga konfiguration](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

Bilden nedan sammanfattas konfigurationsvärdena och hur de fungerar. Du kan inkludera flera handfat i konfigurationen på olika nivåer i hierarkin. Sink på den översta nivån som fungerar som en global inställning och den som anges i enskilda element fungerar som en åsidosättning av den globala inställningen.

![Diagnostik egenskaperna med Application Insights](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Fullständig sink Konfigurationsexempel
Här är en komplett exempel på offentliga konfiguration fil som
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
I den tidigare konfigurationen har följande rader följande:

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

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Skicka bara felloggar till Application Insights sink

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

- **Kanaler bara logga typ och inte prestanda räknare.** Om du anger en kanal med elementet prestandaräknaren ignoreras.
- **Loggningsnivån för en kanal får inte överskrida loggningsnivån för vad som samlas in av Azure-diagnostik.** T.ex, du kan inte samla in program logga fel i loggarna elementet och försök att skicka utförlig loggar till Application Insights sink. Den *scheduledTransferLogLevelFilter* attributet måste alltid samla in lika eller fler loggar än loggarna som du försöker skicka till en mottagare.
- **Du kan inte skicka blob-data som samlas in av Azure-diagnostik tillägget till Application Insights.** Till exempel allt som anges under den *kataloger* nod. För kraschdumpar faktiska krasch-dump skickas till blob storage och endast ett meddelande om att krasch-dump genererades skickas till Application Insights.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [se information om din Azure-diagnostik](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) i Application Insights.
* Använd [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) aktivera tillägget för Azure-diagnostik för ditt program.
* Använd [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) aktivera tillägget för Azure-diagnostik för ditt program
