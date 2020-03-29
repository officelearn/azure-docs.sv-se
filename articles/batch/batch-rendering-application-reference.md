---
title: Använda renderingsprogram - Azure Batch
description: Så här använder du renderingsprogram med Azure Batch. Den här artikeln innehåller en kort beskrivning av hur du kör varje renderingsprogram.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390486"
---
# <a name="rendering-applications"></a>Återge program

Renderingsprogram används genom att skapa batchjobb och uppgifter. Egenskapen aktivitetskommandorad anger lämplig kommandorad och parametrar.  Det enklaste sättet att skapa jobbuppgifterna är att använda mallarna i Batch Explorer enligt den [här artikeln](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Mallarna kan visas och ändrade versioner skapas om det behövs.

Den här artikeln innehåller en kort beskrivning av hur du kör varje renderingsprogram.

## <a name="rendering-with-autodesk-3ds-max"></a>Rendering med Autodesk 3ds Max

### <a name="renderer-support"></a>Stöd för renderare

Förutom de renderare inbyggda i 3ds Max, är följande renderare tillgängliga på rendering vm-avbildningar och kan refereras av 3ds Max scenfilen:

* Autodesk Arnold
* Kaos Grupp V-Ray

### <a name="task-command-line"></a>Kommandoraden Aktivitet

Anropa programmet `3dsmaxcmdio.exe` för att utföra kommandoradsrendering på en poolnod.  Det här programmet finns på sökvägen när aktiviteten körs. Ansökan `3dsmaxcmdio.exe` har samma tillgängliga parametrar `3dsmaxcmd.exe` som programmet, som [dokumenteras i 3ds Max hjälpdokumentation](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Rendering | Kommandoradsrenderingsavsnitt).

Ett exempel:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Obs!

* Stor försiktighet måste iakttas för att se till att tillgångsfilerna hittas.  Kontrollera att sökvägarna är korrekta och relativa med `-bitmapPath` hjälp av fönstret **Tillgångsspårning** eller använd parametern på kommandoraden.
* Se om det finns problem med renderingen, till exempel `stdout.txt` oförmåga att hitta tillgångar, genom att kontrollera filen skriven av 3ds Max när aktiviteten körs.

### <a name="batch-explorer-templates"></a>Mallar för grupputforskaren

Pool- och jobbmallar kan nås från **galleriet** i Batch Explorer.  Mallkällfilerna är tillgängliga i [datadatabasen Batch Explorer på GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Rendering med Autodesk Maya

### <a name="renderer-support"></a>Stöd för renderare

Förutom de renderare som är inbyggda i Maya är följande renderare tillgängliga på återgivningen av vm-avbildningar och kan refereras av 3ds Max-scenfilen:

* Autodesk Arnold
* Kaos Grupp V-Ray

### <a name="task-command-line"></a>Kommandoraden Aktivitet

Kommandoradsrenderaren `renderer.exe` används på aktivitetskommandoraden. Kommandoradsrenderaren dokumenteras i [Maya-hjälpen](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

I följande exempel används en jobbförberedelseuppgift för att kopiera scenfiler och resurser till arbetskatalogen för förberedelse av jobb, en utdatamapp används för att lagra återgivningsavbildningen och bildruta 10 återges.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

För V-Ray rendering, maya scenfilen normalt skulle ange V-Ray som renderare.  Det kan också anges på kommandoraden:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

För Arnold rendering, maya scenfilen normalt skulle ange Arnold som renderare.  Det kan också anges på kommandoraden:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Mallar för grupputforskaren

Pool- och jobbmallar kan nås från **galleriet** i Batch Explorer.  Mallkällfilerna är tillgängliga i [datadatabasen Batch Explorer på GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Nästa steg

Använd pool- och jobbmallarna från [datadatabasen i GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) med Hjälp av Batch Explorer.  Skapa nya mallar eller ändra en av de medföljande mallarna när det behövs.