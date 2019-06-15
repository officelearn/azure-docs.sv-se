---
title: Kör flödesaktivitet för data i Azure Data Factory | Microsoft Docs
description: Hur du kör data flödar från inuti en data factory-pipeline.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: e75c6290474d876ca22b5888d06b1fc0e4c8cd05
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077329"
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

### <a name="debugging-pipelines-with-data-flows"></a>Felsöker pipelines med dataflöden

![Felsöka knappen](media/data-flow/debugbutton.png "Debug-knappen")

Använd den Data flöda felsöka ska kunna använda ett uppvärmning kluster för att testa din dataflöden interaktivt i en pipeline-debug kör. Använd alternativet Felsök Pipeline för att testa din dataflöden i en pipeline.

### <a name="run-on"></a>Kör på

Det här är ett obligatoriskt fält som definierar vilka Integration Runtime för dina Data flöda körningsmiljön för aktiviteten. Som standard använder Data Factory Azure Integration runtime i standardversionen löses. Du kan dock skapa dina egna Azure-Integreringskörningar som definierar specifika regioner, compute typ, kärnor och TTL för dina data flow-körningsmiljön för aktiviteten.

Standardinställningen för dataflöde körningar är 8 kärnor för allmän beräkning med en TTL 60 minuter.

Välj compute-miljö för den här körningen av ditt dataflöde. Standardvärdet är Azure löses standard Integration Runtime. Det här alternativet körs dataflödet på Spark-miljö i samma region som din datafabrik. Compute-typ kommer att ett jobbkluster, vilket innebär att beräkningsmiljön tar flera minuter att start.

Du har kontroll över Spark-körningsmiljö för dina Data flöda aktiviteter. I den [med Azure integration runtime](concepts-integration-runtime.md) finns inställningar du anger beräkningstyp (generell användning, minnesoptimerade och optimerad databehandling), antal kärnor för arbetare och time-to-live så att de matchar motorn för körning med dina Data flöda beräkning krav. Dessutom kan ställa in TTL du underhålla en varm kluster som är omedelbart tillgängligt för jobbkörningar.

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> Integration Runtime-valet i aktiviteten dataflöde gäller endast för *utlösta körningar* i pipelinen. Felsöka en pipeline med Data flödar med Debug körs mot 8 kärnor standard Spark-klustret.

### <a name="staging-area"></a>Mellanlagringsområde

Om du sinka dina data i Azure Data Warehouse, måste du välja en mellanlagringsplats för Polybase batch inläsningen. Mellanlangringsinställningarna gäller endast för Azure Data Warehouse-arbetsbelastningar.

## <a name="parameterized-datasets"></a>Parametriserade datauppsättningar

Om du använder parametriserade datauppsättningar, måste du ange parametervärden.

![Kör flödet Dataarametrar](media/data-flow/params.png "parametrar")

### <a name="debugging-parameterized-data-flows"></a>Felsökning parametriserade dataflöden

Du kan bara felsöka dataflöden med parametriserade datauppsättningar från pipelinen felsöka körs med aktiviteten kör data flöde. Interaktiva debug sessioner i ADF dataflöde fungerar för närvarande inte med parametriserade datauppsättningar. Pipeline-körningar och debug körs fungerar med parametrar.

Ett bra tips är att skapa ditt dataflöde med en statisk datauppsättning så att du har fullständig metadata kolumn spridning är tillgängliga i designläge. Ersätt statiska datauppsättningen med en dynamisk parametriserade datauppsättning när du utföra åtgärder för din datapipeline för flödet.

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
