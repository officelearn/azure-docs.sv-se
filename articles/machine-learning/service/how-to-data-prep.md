---
title: Förbereda data med Machine Learning Data Prep SDK för Python – Azure
description: Lär dig hur du använder Azure Machine Learning Data Prep SDK för Python för att läsa in data av olika format, omvandla den till att vara mer användbara och skriva dessa data till en plats för dina modeller att få åtkomst till.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/20/2018
ms.openlocfilehash: 72a6e7fdc8bd5887782ab23d29812bed792fd53f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164599"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Förbereda data för modellering med Azure Machine Learning
 
I den här artikeln får du lära dig om användningsfall och unika funktioner i Azure Machine Learning Data Prep SDK. Förberedelse av data är den viktigaste delen av machine learning-arbetsflöde. Verkliga data delas ofta inkonsekvent eller kan inte användas som träningsdata utan betydande rengöring och omvandling. Korrigering och avvikelser i rådata och skapa nya funktioner som är relevanta för problemet du försöker lösa, ökar modellens precision.

Du kan förbereda dina data i Python med hjälp av den [Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py).

## <a name="azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Dataförberedelser SDK

Den [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) är ett Python-bibliotek som innehåller:
+ Många vanliga verktyg som används för att förbearbetning data
+ Automatiserad funktionsframställning och transformeringar som härletts från exempel

SDK: N är liknande grundläggande funktioner som populära bibliotek som **Pandas** och **PySpark**, erbjuder ännu mer flexibilitet. Pandas är vanligtvis mest användbara på mindre datamängder (< 2 – 5 GB) innan minne kapacitetsbegränsningar påverkar prestanda. Däremot PySpark är vanligtvis för stordata program, men innebär en belastning som gör att arbeta med små datauppsättningar som är mycket långsammare.

Azure Machine Learning Data Prep SDK erbjuder:
- Praktiska och bekvämlighet när du arbetar med små datauppsättningar

- Skalbarhet för moderna program för stordata

- Möjligheten att använda och skalar upp samma kod för båda användningsfall

### <a name="install-the-sdk"></a>Installera SDK:n

Installera SDK i Python-miljön med följande kommando.

```shell
pip install azureml-dataprep
```

Använd följande kod för att importera paketet.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Referens och exempel

Läs mer om moduler och funktioner i den här SDK, i den [Data Prep SDK referensdokument](https://aka.ms/data-prep-sdk).

I följande exempel beskrivs några av de unika funktionerna i SDK, inklusive:
+ Automatisk identifiering
+ Automatiska funktioner
+ Sammanfattande statistik

#### <a name="automatic-file-type-detection"></a>Automatisk identifiering

Använd den `smart_read_file()` funktionen för att läsa in data utan att behöva ange filtypen. Den här funktionen automatiskt identifierar och tolkar filtypen.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="automated-feature-engineering"></a>Automatiska funktioner

Använda SDK för att dela och härleda kolumner efter både exempel och inferens att automatisera funktionsframställning. Anta att du har ett fält i ditt dataflöde-objekt som kallas `datetime` med värdet `2018-09-15 14:30:00`.

Att automatiskt dela den `datetime` fältet, anropa följande funktion.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

Genom att inte definiera parametern exempel kan funktionen automatiskt dela upp den `datetime` fält till två nya fält `datetime_1` och `datetime_2`. Resultatvärdena finns `2018-09-15` och `14:30:00`respektive. Det är också möjligt att ange en exempel-mönster och SDK: N kommer förutsäga och kör den avsedda omvandlingen. Med samma `datetime` objekt kan följande kod skapar en ny kolumn `datetime_weekday` för veckodagen baserat på angivna exemplet.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>Sammanfattande statistik

Du kan generera quick sammanfattande statistik för ett dataflöde med en rad med kod. Den här metoden erbjuder ett enkelt sätt att förstå dina data och hur den ska distribueras.

```python
dataflow.get_profile()
```

Den här funktionen anropas för ett objekt i dataflöde resulterar i utdata som liknar följande tabell.

![Sammanfattande statistik utdata](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Flera enhetskompatibilitet i hela miljön

SDK: N kan även användas för dataflöde objekt som ska serialiseras och öppnas i *alla* Python-miljön. Miljön där den är öppen kan skilja sig från miljön där den har sparats. Den här funktionen möjliggör filöverföring mellan Python-miljöer och snabb integrering med Azure Machine Learning-modeller.

Använd följande kod för att spara dataflöde-objekt.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Använd följande kod för att öppna ditt paket i alla miljöer och hämta en lista över dataflöde objekt.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Pipeline för förberedelse av data

Om du vill se detaljerade exempel-kod för varje steg för förberedelse, Använd följande guider:

1. [Läsa in data](how-to-load-data.md), vilket kan vara i olika format
2. [Omvandla](how-to-transform-data.md) den i en mer användbara struktur
3. [Skriva](how-to-write-data.md) dessa data till en plats som är tillgänglig för dina modeller

![Processen att förbereda data](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Nästa steg
Granska en [exempel notebook](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) för förberedelse av data med hjälp av Azure Machine Learning Data Prep SDK.

Azure Machine Learning Data Prep SDK [referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).
