---
title: Övervaka händelser för Linux-kluster i Azure Service Fabric | Microsoft Docs
description: Lär dig att övervaka Linux-kluster av händelser från Syslog
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670378"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Service Fabric Linux klusterhändelser i Syslog

Service Fabric visar en uppsättning händelser på plattformsnivå om viktig aktivitet i klustret. En fullständig lista över händelser som visas är tillgänglig [här](service-fabric-diagnostics-event-generation-operational.md). Det finns olika sätt som de här händelserna kan användas. I den här artikeln ska vi att diskutera hur du konfigurerar Service Fabric för att skriva dessa händelser till Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introduktion

I utgåvan av 6.4 har SyslogConsumer introducerats för att skicka Service Fabric-plattformshändelser till Syslog för Linux-kluster. När aktiverad, flödar automatiskt händelser till Syslog som kan samlas in och skickas av Log Analytics-agenten.

Varje Syslog-händelse har 4 komponenter
* Lokal
* Identitet
* Meddelande
* Severity

SyslogConsumer skriver alla plattformshändelser med hjälp av anläggning `Local0`. Du kan uppdatera till någon giltig resurs genom att ändra config-config. Den identitet som används är `ServiceFabric`. Meddelandet innehåller hela händelsen serialiseras i JSON så att den kan efterfrågas och används av en mängd olika verktyg. 

## <a name="enable-syslogconsumer"></a>Aktivera SyslogConsumer

Om du vill aktivera SyslogConsumer som du behöver utföra en uppgradering av klustret. Den `fabricSettings` avsnittet behöver uppdateras med följande kod. Observera att den här koden innehåller endast avsnitt relaterade till SyslogConsumer

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

Här är ändringarna att anropa
1. Det finns en ny parameter med namnet i avsnittet vanliga `LinuxStructuredTracesEnabled`. **Det är nödvändigt att ha Linux händelser strukturerad och serialiseras om skickas till Syslog.**
2. I avsnittet diagnostik, en ny ConsumerInstance: SyslogConsumer har lagts till. Det här visar plattformen finns på en annan konsumenter av händelserna. 
3. Det nya avsnittet SyslogConsumer måste ha `IsEnabled` som `true`. Den är konfigurerad för att använda funktionen Local0 automatiskt. Du kan åsidosätta detta genom att lägga till en annan parameter.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integrering med Azure Monitor-loggar
Du kan läsa de här Syslog-händelser i ett övervakningsverktyg, till exempel Azure Monitor-loggar. Du kan skapa en Log Analytics-arbetsyta med hjälp av Azure Marketplace med hjälp av [anvisningarna]. (.. / azure-monitor/learn/quick-create-workspace.md) måste du också lägga till Log Analytics-agenten till klustret för att samla in och skicka dessa data till arbetsytan. Det här är samma agent används för att samla in prestandaräknare. 

1. Navigera till den `Advanced Settings` bladet

    ![Inställningar för arbetsyta](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klicka på `Data`
3. Klicka på `Syslog`
4. Konfigurera Local0 som funktionen för att spåra. Du kan lägga till en annan resurs om du har ändrat i fabricSettings

    ![Konfigurera Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Gå till frågeutforskaren genom att klicka på `Logs` i arbetsytan resursens menyn för att börja ställa frågor till

    ![Arbetsytan loggar](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Du kan fråga mot den `Syslog` tabell söker `ServiceFabric` som processnamn. Frågan nedan är ett exempel på hur du parsa JSON i händelsen och visa innehållet

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog-fråga](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

I exemplet ovan är av en NodeDown-händelse. Du kan visa en fullständig lista över händelser [här](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) till noderna kan samla in prestandaräknare och samla in loggar för dina behållare och docker-stats
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Azure Monitor-loggar
* [Använd View Designer för att skapa anpassade vyer i Azure Monitor-loggar](../log-analytics/log-analytics-view-designer.md)
* Referens för hur du [Azure Monitor-loggar integrering med Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
