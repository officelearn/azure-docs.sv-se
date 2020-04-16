---
title: Skapa utlösande beroenden för tumlande fönster
description: Lär dig hur du skapar beroende av en utlösare för tumlande fönster i Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 39ea8dda0fd823d3061b2cb29e1c548f99281c82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418804"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Skapa ett beroende för utlösare för rullande fönster
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln innehåller steg för att skapa ett beroende av en utlösare för tumlande fönster. Allmän information om utlösare av Tumbling Window finns i [Så här skapar du utlösare av tumlande fönster](how-to-create-tumbling-window-trigger.md).

För att skapa en beroendekedja och se till att en utlösare körs först efter att en annan utlösare har körts i datafabriken, använd den här avancerade funktionen för att skapa ett tumlande fönsterberoende.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Skapa ett beroende i datafabrikens användargränssnitt

Om du vill skapa beroende på en utlösare väljer du **Utlösare > Avancerat > Nytt**och väljer sedan den utlösare som ska vara beroende av med lämplig förskjutning och storlek. Välj **Slutför** och publicera ändringar i datafabriken för de beroenden som ska börja gälla.

![Skapa beroende](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Skapa beroende")

## <a name="tumbling-window-dependency-properties"></a>Egenskaper för Tumbling window dependency

En utlösare för ett tumlande fönster med ett beroende har följande egenskaper:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

I följande tabell finns en lista över attribut som behövs för att definiera ett Tumbling Window-beroende.

| **Egenskapsnamn** | **Beskrivning**  | **Typ** | **Obligatoriskt** |
|---|---|---|---|
| typ  | Alla befintliga tumlande fönsterutlösare visas i den här listrutan. Välj den utlösare som du vill ta beroende av.  | TumlandeWindowTriggerBeroendeRena eller självberoende tumlandewindowTriggerreference | Ja |
| offset | Förskjutning av beroendeutlösaren. Ange ett värde i tidsintervallformat och både negativa och positiva förskjutningar tillåts. Den här egenskapen är obligatorisk om utlösaren är beroende av sig själv och i alla andra fall är den valfri. Självberoende bör alltid vara en negativ förskjutning. Om inget värde anges är fönstret samma som själva utlösaren. | Tidsintervall<br/>(hh:mm:ss) | Självberoende: Ja<br/>Övrigt: Nej |
| size | Storleken på beroendet tumlande fönster. Ange ett positivt tidsintervallvärde. Den här egenskapen är valfri. | Tidsintervall<br/>(hh:mm:ss) | Inga  |

> [!NOTE]
> En tumlande fönsterutlösare kan bero på högst två andra utlösare.

## <a name="tumbling-window-self-dependency-properties"></a>Självberoendeegenskaper för tumlande fönster

I scenarier där utlösaren inte ska fortsätta till nästa fönster förrän det tidigare fönstret har slutförts skapar du ett självberoende. En självberoendeutlösare som är beroende av framgång för tidigare körningar av sig själv inom den föregående timmen kommer att ha nedanstående egenskaper:

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
## <a name="usage-scenarios-and-examples"></a>Användningsscenarier och exempel

Nedan finns illustrationer av scenarier och användning av tumbling fönster beroende egenskaper.

### <a name="dependency-offset"></a>Förskjutning av beroende

![Exempel på förskjutning](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Exempel på förskjutning")

### <a name="dependency-size"></a>Beroendestorlek

![Exempel på storlek](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Exempel på storlek")

### <a name="self-dependency"></a>Självberoende

![Självberoende](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Självberoende")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Beroende av en annan utlösande faktor för ett tumlande fönster

Ett dagligt jobb för telemetribearbetning beroende på ett annat dagligt jobb som samlar de senaste sju dagarnas utdata och genererar sju dagars rullande fönsterströmmar:

![Exempel på beroende](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Exempel på beroende")

### <a name="dependency-on-itself"></a>Beroende av sig själv

Ett dagligt jobb utan luckor i utdataströmmarna för jobbet:

![Exempel på självberoende](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exempel på självberoende")

En demonstration om hur du skapar beroende pipelines i Din Azure Data Factory med utlösande fönster kan du titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Övervaka beroenden

Du kan övervaka beroendekedjan och motsvarande fönster från övervakningssidan för utlösarkörning. Navigera till **Övervakning > utlösarkörningar**. Under åtgärdskolumnen kan du köra om utlösaren eller visa dess beroenden.

![Övervaka utlösarkörningar](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Övervaka utlösarkörningar")

Om du klickar på Visa triggerberoenden kan du se status för beroenden. Om en av beroendeutlösare misslyckas måste du köra den igen för att den beroende utlösaren ska kunna köras. En tumlande fönsterutlösare väntar på beroenden i sju dagar innan du går ut.

![Övervaka beroenden](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Övervaka beroenden")

Om du vill visa schemat för utlösarberoende för att visa schemat för utlösarens beroende, välj Gantt-vyn.

![Övervaka beroenden](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Övervaka beroenden")

## <a name="next-steps"></a>Nästa steg

* Granska [Hur du skapar en utlösare för ett tumlande fönster](how-to-create-tumbling-window-trigger.md)
