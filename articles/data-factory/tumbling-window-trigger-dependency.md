---
title: Skapa utlösare för rullande fönster utlösarberoenden i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar beroende på en utlösare för rullande fönster i Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574397"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Skapa ett beroende för utlösare för rullande fönster

Den här artikeln innehåller steg för att skapa ett beroende på en utlösare för rullande fönster. Allmän information om utlösare för rullande fönster finns i [hur du skapar utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md).

För att skapa en beroendekedja och se till att en utlösare körs bara när du har utförts av en annan utlösaren i datafabriken kan använda den här avancerade funktionen för att skapa ett beroende för rullande fönster.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Skapa ett beroende i Användargränssnittet för Data Factory

Om du vill skapa beroende på en utlösare, Välj **utlösare > Avancerat > Ny**, och välj sedan utlösaren förlita sig på med lämpliga förskjutning och storlek. Välj **Slutför** och publicera data factory-ändringar för beroenden ska börja gälla.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Egenskaper för rullande fönster beroende

Ett beroende för rullande fönster utlösaren har följande egenskaper:

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

I följande tabell innehåller en lista över attribut som krävs för att definiera ett beroende för rullande fönster.

| **Egenskapsnamn** | **Beskrivning**  | **Typ** | **Krävs** |
|---|---|---|---|
| Utlösare  | Alla de befintliga utlösarna för rullande fönster visas i den här sänkningen ned. Välj utlösaren ska utföras beroende på.  | TumblingWindowTrigger | Ja |
| Offset | Förskjutningen av beroende utlösaren. Ange ett värde i span tidsformat och både negativa och positiva förskjutningar tillåts. Den här parametern är obligatorisk om utlösaren är beroende på själva och i alla andra fall är det valfritt. Self beroende ska alltid vara ett negativt förskjutning. | Tidsintervall | Self-beroende: Ja andra: Nej |
| Fönsterstorlek | Storleken på beroende rullande fönster. Ange ett värde i span tidsformat. Den här parametern är valfri. | Tidsintervall | Nej  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Egenskaper för rullande fönster self-beroende

Skapa ett självsignerat-beroende i scenarier där utlösaren inte ska gå vidare till nästa fönster tills fönstret tidigare har slutförts. Self beroende utlösare kommer har följande egenskaper:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

Nedan visas bilder av scenarier och användningen av rullande fönster egenskaper för beroende.

## <a name="dependency-offset"></a>Beroende förskjutning

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Beroende storlek

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Self-beroende

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Användningsscenarier

### <a name="dependency-on-another-tumbling-window-trigger"></a>Beroende på en annan utlösare för rullande fönster

Till exempel ett jobb för bearbetning av dagliga telemetri beroende på ett annat dagliga jobb sammanställning av de senaste sju dagarna utdata och genererar strömmar för rullande fönster i sju dagar:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Beroende på sig självt

Dagliga jobb utan några mellanrum i utdataströmmar för jobbet:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Övervaka beroenden

Du kan övervaka beroendekedjan och motsvarande windows från utlösaren kör övervakning. Gå till **övervakning > utlösa körningar**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Klicka på sökning glas att visa alla beroende utlösaren körs i fönstret valda.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Nästa steg

Granska [så här skapar du en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md).