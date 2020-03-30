---
title: Övervaka Linux-klusterhändelser i Azure Service Fabric
description: Lär dig hur du övervakar en Service Fabric Linux kluster händelser genom att skriva Service Fabric plattform händelser till Syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645760"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Linux-klusterhändelser för Service Fabric i Syslog

Service Fabric exponerar en uppsättning plattformshändelser för att informera dig om viktig aktivitet i klustret. Den fullständiga listan över händelser som exponeras finns [här](service-fabric-diagnostics-event-generation-operational.md). Det finns olika sätt genom vilka dessa händelser kan konsumeras. I den här artikeln kommer vi att diskutera hur du konfigurerar Service Fabric för att skriva dessa händelser till Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introduktion

I 6.4-versionen har SyslogConsumer introducerats för att skicka Plattformshändelserna för Service Fabric till Syslog för Linux-kluster. När händelserna har aktiverats flödar de automatiskt till Syslog som kan samlas in och skickas av Log Analytics Agent.

Varje Syslog-händelse har 4 komponenter
* Anläggning
* Identitet
* Meddelande
* Severity

SyslogConsumer skriver alla plattformshändelser `Local0`med Facility . Du kan uppdatera till alla giltiga anläggningar genom att ändra config config. Identiteten som `ServiceFabric`används är . Fältet Meddelande innehåller hela händelsen serialiserad i JSON så att den kan efterfrågas och förbrukas av en mängd olika verktyg. 

## <a name="enable-syslogconsumer"></a>Aktivera SyslogConsumer

Om du vill aktivera SyslogConsumer måste du uppgradera klustret. Avsnittet `fabricSettings` måste uppdateras med följande kod. Observera att den här koden bara innehåller avsnitt relaterade till SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Här är ändringarna för att ropa
1. I avsnittet Gemensamt finns en ny `LinuxStructuredTracesEnabled`parameter som kallas . **Detta krävs för att ha Linux-händelser strukturerade och serialiserade när de skickas till Syslog.**
2. I avsnittet Diagnostik har en ny ConsumerInstance: SyslogConsumer lagts till. Detta talar om för plattformen att det finns en annan konsument av händelserna. 
3. Det nya avsnittet SyslogConsumer `IsEnabled` `true`måste ha som . Den är konfigurerad för att använda local0-funktionen automatiskt. Du kan åsidosätta detta genom att lägga till en annan parameter.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integrering av Azure Monitor-loggar
Du kan läsa dessa Syslog-händelser i ett övervakningsverktyg som Azure Monitor-loggar. Du kan skapa en Log Analytics-arbetsyta med hjälp av Azure Marketplace med hjälp av dessa [instruktioner]. (.. /azure-monitor/learn/quick-create-workspace.md) Du måste också lägga till Log Analytics-agenten i klustret för att samla in och skicka dessa data till arbetsytan. Detta är samma agent som används för att samla in prestandaräknare. 

1. Navigera till `Advanced Settings` bladet

    ![Inställningar för arbetsyta](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klicka på `Data`
3. Klicka på `Syslog`
4. Konfigurera Local0 som anläggning för att spåra. Du kan lägga till en annan anläggning om du har ändrat den i fabricSettings

    ![Konfigurera Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Gå över till frågeutforskaren genom att `Logs` klicka på arbetsytans resurs meny för att börja fråga

    ![Arbetsyteloggar](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Du kan fråga `Syslog` mot `ServiceFabric` tabellen som letar efter som ProcessName. Frågan nedan är ett exempel på hur man tolkar JSON i händelsen och visa dess innehåll

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Fråga om Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Exemplet ovan är en NodeDown-händelse. Du kan se hela listan över händelser [här](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) på dina noder för att samla in prestandaräknare och samla in dockerstatistik och loggar för dina behållare
* Bekanta dig med [loggsöknings- och frågefunktionerna](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor-loggar
* [Använda Vydesignern för att skapa anpassade vyer i Azure Monitor-loggar](../log-analytics/log-analytics-view-designer.md)
* Referens för integrering [av Azure Monitor loggar med Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
