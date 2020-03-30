---
title: Importera data
titleSuffix: Azure Machine Learning
description: Lär dig hur du importerar dina data till Azure Machine Learning designer från olika datakällor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: bd1c2ca182ae8be8425246f691dca805bf38637b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064019"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importera dina data till Azure Machine Learning Designer (förhandsversion)

I den här artikeln får du lära dig hur du importerar dina egna data i designern för att skapa anpassade lösningar. Du kan importera data till designern på två sätt: 

* **Azure Machine Learning-datauppsättningar** – Registrera [datauppsättningar](concept-data.md#datasets) i Azure Machine Learning för att aktivera avancerade funktioner som hjälper dig att hantera dina data.
* **Importera datamodul** - Använd modulen [Importera data](algorithm-module-reference/import-data.md) för att direkt komma åt data från datakällor online.

## <a name="use-azure-machine-learning-datasets"></a>Använda Azure Machine Learning-datauppsättningar

Vi rekommenderar att du använder [datauppsättningar](concept-data.md#datasets) för att importera data till designern. När du registrerar en datauppsättning kan du dra full nytta av avancerade datafunktioner som [versionshantering och spårning](how-to-version-track-datasets.md) och [dataövervakning](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrera en datauppsättning

Du kan registrera befintliga datauppsättningar [programmässigt med SDK](how-to-create-register-datasets.md#use-the-sdk) eller [visuellt i Azure Machine Learning studio](how-to-create-register-datasets.md#use-the-ui).

Du kan också registrera utdata för en designermodul som en datauppsättning.

1. Välj den modul som matar ut de data som du vill registrera.

1. Välj UtdataRegisterdatauppsättning i > **egenskapsfönstret**. **Outputs**

    ![Skärmbild som visar hur du navigerar till alternativet Registrera datauppsättning](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Använda en datauppsättning

Dina registrerade datauppsättningar finns i modulpaletten under **Dataset** > **Mina datauppsättningar**. Om du vill använda en datauppsättning drar och släpper du den på pipeline-arbetsytan. Anslut sedan datauppsättningens utdataport till andra moduler i paletten.

![Skärmbild som visar platsen för sparade datauppsättningar i designerpaletten](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Designern stöder för närvarande endast bearbetning av [tabelldatauppsättningar](how-to-create-register-datasets.md#dataset-types). Om du vill använda [fildatauppsättningar](how-to-create-register-datasets.md#dataset-types)använder du Azure Machine Learning SDK som är tillgängligt för Python och R.

## <a name="import-data-using-the-import-data-module"></a>Importera data med modulen Importera data

Vi rekommenderar att du använder datauppsättningar för att importera data, men du kan också använda modulen [Importera data.](algorithm-module-reference/import-data.md) Modulen Importera data hoppar över att registrera datauppsättningen i Azure Machine Learning och importerar data direkt från en [datalager-](concept-data.md#datastores) eller HTTP-URL.

Detaljerad information om hur du använder modulen Importera data finns på [referenssidan för importdata](algorithm-module-reference/import-data.md).

> [!NOTE]
> Om datauppsättningen har för många kolumner kan följande fel uppstå: "Valideringen misslyckades på grund av storleksbegränsning". Undvik detta genom att [registrera datauppsättningen i datauppsättningsgränssnittet](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Källor som stöds

I det här avsnittet visas de datakällor som stöds av designern. Data kommer in i designern från antingen ett datalager eller från [tabelldatauppsättning](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Datalagerkällor
En lista över datalagerkällor som stöds finns [i Access-data i Azure storage services](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Tabelldatauppsättningskällor

Designern stöder tabelldatauppsättningar som skapats från följande källor:
 * Avgränsade filer
 * JSON-filer
 * Parquet-filer
 * SQL-frågor

## <a name="data-types"></a>Datatyper

Designern känner internt igen följande datatyper:

* String
* Integer
* Decimal
* Boolean
* Datum

Designern använder en intern datatyp för att skicka data mellan moduler. Du kan uttryckligen konvertera dina data till datatabellformat med modulen [Konvertera till datauppsättning.](algorithm-module-reference/convert-to-dataset.md) Alla moduler som accepterar andra format än det interna formatet konverterar data tyst innan de skickar dem till nästa modul.

## <a name="data-constraints"></a>Databegränsningar

Moduler i designern begränsas av storleken på beräkningsmålet. För större datauppsättningar bör du använda en större Azure Machine Learning-beräkningsresurs. Mer information om Azure Machine Learning-beräkning finns [i Vad är beräkningsmål i Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Nästa steg

Lär dig grunderna i designern med [handledning: Förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md).
