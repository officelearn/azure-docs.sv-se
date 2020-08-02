---
title: Importera data till designern (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du importerar data till Azure Machine Learning designer (för hands version) från olika data källor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d977c8e13ce75eb276c8fdb11e9dd40e40a923ad
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495379"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Importera data till Azure Machine Learning designer (förhands granskning)

I den här artikeln får du lära dig hur du importerar dina egna data i designern för att skapa anpassade lösningar. Det finns två sätt att importera data till designern: 

* **Azure Machine Learning data uppsättningar** – registrera [data uppsättningar](concept-data.md#datasets) i Azure Machine Learning för att aktivera avancerade funktioner som hjälper dig att hantera dina data.
* **Importera datamodul** – Använd modulen [Importera data](algorithm-module-reference/import-data.md) för att direkt komma åt data från online-datakällor.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Använd Azure Machine Learning data uppsättningar

Vi rekommenderar att du använder data [uppsättningar](concept-data.md#datasets) för att importera data till designern. När du registrerar en data uppsättning kan du dra full nytta av avancerade data funktioner som [versions hantering och spårning](how-to-version-track-datasets.md) och [data övervakning](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrera en data uppsättning

Du kan registrera befintliga data uppsättningar [program mässigt med SDK](how-to-create-register-datasets.md#datasets-sdk) eller [visuellt i Azure Machine Learning Studio](how-to-create-register-datasets.md#datasets-ui).

Du kan också registrera utdata för alla designer-moduler som en data uppsättning.

1. Välj den modul som matar ut de data som du vill registrera.

1. I fönstret Egenskaper väljer du **utdata**  >  **register data uppsättning**.

    ![Skärm bild som visar hur du navigerar till alternativet registrera data uppsättning](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Använd en data uppsättning

Du hittar dina registrerade data uppsättningar i modulen modul under **data uppsättningar**  >  **mina data uppsättningar**. Om du vill använda en data uppsättning drar du och släpper den på pipeline-arbetsytan. Anslut sedan utdataporten för data uppsättningen till andra moduler i paletten.

![Skärm bild som visar platsen för sparade data uppsättningar i design verktyget](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Designern stöder för närvarande endast bearbetning av [tabell data uppsättningar](how-to-create-register-datasets.md#dataset-types). Om du vill använda [fil data uppsättningar](how-to-create-register-datasets.md#dataset-types)använder du Azure Machine Learning SDK som finns för python och R.

## <a name="import-data-using-the-import-data-module"></a>Importera data med modulen importera data

Vi rekommenderar att du använder data uppsättningar för att importera data, men du kan också använda modulen [Importera data](algorithm-module-reference/import-data.md) . Modulen importera data hoppar över registrering av data uppsättningen i Azure Machine Learning och importerar data direkt från en [data lager](concept-data.md#datastores) -eller http-URL.

Detaljerad information om hur du använder modulen importera data finns på [sidan Importera data referens](algorithm-module-reference/import-data.md).

> [!NOTE]
> Om din data uppsättning har för många kolumner kan följande fel uppstå: "verifieringen misslyckades på grund av storleks begränsning". Undvik detta genom att [registrera data uppsättningen i gränssnittet för data uppsättningar](how-to-create-register-datasets.md#datasets-ui).

## <a name="supported-sources"></a>Källor som stöds

I det här avsnittet visas de data källor som stöds av designern. Data kommer in i designern från antingen ett data lager eller från [tabell data uppsättning](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Data källor
En lista över data källor som stöds finns i [få åtkomst till data i Azure Storage-tjänster](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Tabell data uppsättnings källor

Designern stöder tabell data uppsättningar som skapats från följande källor:
 * Avgränsade filer
 * JSON-filer
 * Parquet-filer
 * SQL-frågor

## <a name="data-types"></a>Datatyper

Designern identifierar internt följande data typer:

* Sträng
* Heltal
* Decimal
* Boolesk
* Date

Designern använder en intern datatyp för att skicka data mellan moduler. Du kan uttryckligen konvertera dina data till data tabell format med hjälp av modulen [konvertera till data uppsättning](algorithm-module-reference/convert-to-dataset.md) . Alla moduler som accepterar andra format än det interna formatet kommer att konvertera data tyst innan de skickas till nästa modul.

## <a name="data-constraints"></a>Data begränsningar

Modulerna i designern är begränsade till beräknings målets storlek. För större data uppsättningar bör du använda en större Azure Machine Learning beräknings resurs. Mer information om Azure Machine Learning Compute finns [i vad är beräknings mål i Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Få åtkomst till data i ett virtuellt nätverk

Om din arbets yta finns i ett virtuellt nätverk måste du utföra ytterligare konfigurations steg för att visualisera data i designern. Mer information om hur du använder data lager och data uppsättningar i ett virtuellt nätverk finns i [nätverks isolering under träning &s härledning med privata virtuella nätverk](how-to-enable-virtual-network.md#machine-learning-studio).

## <a name="next-steps"></a>Nästa steg

Lär dig grunderna i designern med [Självstudier: förutsäga det mobila priset med designern](tutorial-designer-automobile-price-train-score.md).
