---
title: Använd renderingsprogram – Azure Batch
description: Hur du använder renderingsprogram med Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4c93abdfb5c523d48ce115ed7d3251a346937f5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775397"
---
# <a name="rendering-applications"></a>Renderingsprogram

Renderingsprogram används genom att skapa Batch-jobb och uppgifter. Uppgiften kommandoradsegenskapen anger lämplig kommandorad och parametrar.  Det enklaste sättet att skapa jobbuppgifter är att använda Batch Explorer-mallar som anges i [i den här artikeln](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Mallarna kan visas och ändras versioner som skapats vid behov.

Den här artikeln innehåller en kort beskrivning av hur du kör programmen för rendering.

## <a name="rendering-with-autodesk-3ds-max"></a>Rendering med Autodesk 3ds Max

### <a name="renderer-support"></a>Stöd för återgivning

Förutom renderare inbyggt i 3ds Max, följande renderare är tillgängliga på VM-avbildningarna rendering och kan refereras av 3ds Max-scenfilen:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Aktivitetens kommandorad

Anropa den `3dsmaxcmdio.exe` program som kör kommandoraden återgivning på en nod i poolen.  Det här programmet finns på sökvägen när aktiviteten körs. Den `3dsmaxcmdio.exe` programmet har samma tillgängliga parametrar som den `3dsmaxcmd.exe` programmet, vilket beskrivs i den [3ds Max hjälpdokumentation](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Rendering | Kommandoradsverktyget Rendering avsnittet).

Exempel:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Information:

* Försiktighet måste vidtas för att kontrollera resursfiler hittas.  Se till att sökvägarna är korrekt och relativa med hjälp av den **tillgången spåra** fönster eller Använd den `-bitmapPath` parametern på kommandoraden.
* Om det finns problem med rendering, till exempel inte går att hitta tillgångar, genom att markera den `stdout.txt` filen som skrivits av 3ds Max när aktiviteten körs.

### <a name="batch-explorer-templates"></a>Batch Explorer-mallar

Mallar för poolen och jobbet kan nås från den **galleriet** i Batch Explorer.  Källfilerna för mallen är tillgängliga i den [Batch Explorer data arkivet på GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Rendera med Autodesk Maya

### <a name="renderer-support"></a>Stöd för återgivning

Förutom renderare inbyggt i Maya, följande renderare är tillgängliga på VM-avbildningarna rendering och kan refereras av 3ds Max-scenfilen:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Aktivitetens kommandorad

Den `renderer.exe` kommandoradsverktyget återgivning används i aktivitetens kommandorad. Kommandoradsverktyget återgivning dokumenteras i [Maya hjälpa](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

I följande exempel visas en jobbförberedelseaktivitet används för att kopiera scen filer och resurser till arbetskatalogen för jobbförberedelse, en utdatamapp används för att lagra avbildningen rendering och ramens 10 återges.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

För V-Ray rendering anger scenfilen Maya normalt V-Ray som renderare.  Det kan också anges på kommandoraden:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

För Arnold rendering, scenfilen Maya skulle normalt ange Arnold som renderare.  Det kan också anges på kommandoraden:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer-mallar

Mallar för poolen och jobbet kan nås från den **galleriet** i Batch Explorer.  Källfilerna för mallen är tillgängliga i den [Batch Explorer data arkivet på GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Nästa steg

Använda mallar för poolen och jobbet från den [data lagringsplatsen i GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) med Batch Explorer.  Vid behov, skapa nya mallar eller ändra någon av de angivna mallarna.