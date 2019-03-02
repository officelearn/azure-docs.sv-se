---
title: Kör flödesaktivitet för data i Azure Data Factory | Microsoft Docs
description: Aktiviteten kör data flödet körs dataflöden.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 17bcb9efbf28901f241d44231bc5c71ae20380b5
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241620"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Kör flödesaktivitet för data i Azure Data Factory
Använda aktiviteten kör data flöde för att köra ditt ADF dataflöde i pipelinekörningar för felsökning (sandbox) och utlösta pipelinekörningar.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

* ```dataflow``` är namnet på entiteten data flödet som du vill köra
* ```compute``` Beskriver Spark-körningsmiljö
* ```coreCount``` är antalet kärnor som ska tilldelas den här aktivitetskörning av ditt dataflöde

![Köra dataflödet](media/data-flow/activity-data-flow.png "köra dataflödet")

### <a name="run-on"></a>Kör på

Välj compute-miljö för den här körningen av ditt dataflöde. Standardvärdet är Azure löses standard Integration Runtime. Det här alternativet körs dataflödet på Spark-miljö i samma region som din datafabrik. Compute-typ kommer att ett jobbkluster, vilket innebär att beräkningsmiljön tar flera minuter att start.

Om du väljer en dedikerad IR kan du skapa en ny Azure IR med en Fäst region och compute-storlekar som uppfyller dina krav på flödet. Det här alternativet kommer snurra upp interaktiva kluster, som kommer start omedelbart efter det första jobbet har skickats iväg. Det här klustret kommer förblir aktiva tills TTL-Perioden upphör att gälla efter det senaste jobbet har körts.

### <a name="compute-type"></a>Typ av databehandling

Du kan välja General Purpose Compute Optimized eller Minnesoptimerade, beroende på kraven för ditt dataflöde.

### <a name="core-count"></a>Antal kärnor

Välj hur många kärnor som du vill tilldela till jobbet. För mindre jobb fungerar färre kärnor bättre.

### <a name="staging-area"></a>Mellanlagringsområde

Om du sinka dina data i Azure Data Warehouse, måste du välja en mellanlagringsplats för Polybase batch inläsningen.

## <a name="parameterized-datasets"></a>Parametriserade datauppsättningar

Om du använder parametriserade datauppsättningar, måste du ange parametervärden.

![Kör flödet Dataarametrar](media/data-flow/params.png "parametrar")

### <a name="debugging-parameterized-data-flows"></a>Felsökning parametriserade dataflöden

Du kan bara felsöka dataflöden med parametriserade datauppsättningar från pipelinen felsöka körs med aktiviteten kör data flöde. Interaktiva debug sessioner i ADF dataflöde fungerar för närvarande inte med parametriserade datauppsättningar. Pipeline-körningar och debug körs fungerar med parametrar.

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
