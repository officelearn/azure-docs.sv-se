---
title: 'Designer: förutse flyg fördröjnings exempel'
titleSuffix: Azure Machine Learning
description: Bygg en klassificerare och Använd anpassad R-kod för att förutsäga flyg fördröjningar med Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 23b763a69fc0ea3191150c6255cf358d69bc4b73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213991"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Bygg en klassificerare & använda R för att förutsäga flyg fördröjningar med Azure Machine Learning designer

**Designer (för hands version) exempel 6**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Den här pipelinen använder historiska flyg-och väder data för att förutsäga om en schemalagd passagerar flygning kommer att fördröjas med mer än 15 minuter. Det här problemet kan uppstå som ett klassificerings problem, som förutsäger två klasser: fördröjt eller på tid.

Här är den slutliga pipeline-grafen för det här exemplet:

[![diagram över pipelinen](media/how-to-designer-sample-classification-predict-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Klicka på exempel 6 för att öppna det.

## <a name="get-the-data"></a>Hämta data

I det här exemplet används data uppsättningen för **flyg fördröjning** . Den är en del av TranStats-datainsamlingen från USA: s departements transport. Data uppsättningen innehåller information om flyg fördröjning från april till oktober 2013. Data uppsättningen har för beretts på följande sätt:

* Filtrerat för att inkludera vanligaste-flyg platser på 70 i kontinental USA.
* Har märkt om annullerade flygningar som fördröjda med mer än 15 minuter.
* Filtrerat bort inledade flygningar.
* Markerade 14 kolumner.

För att komplettera flyg data används **väder data uppsättningen** . Väder data innehåller varje timme, landbaserade väderleks observationer från NOAA och visar observationer från flyg plats väderleks stationer som omfattar samma tids period som flygningarnas data uppsättning. Den har förbehandlats på följande sätt:

* Väder Stations-ID: n mappades till motsvarande flyg plats-ID.
* Väder stationer som inte är kopplade till 70 vanligaste-flyg platserna togs bort.
* Datum kolumnen delades upp i separata kolumner: år, månad och dag.
* De markerade 26 kolumnerna.

## <a name="pre-process-the-data"></a>För behandling av data

En data uppsättning kräver vanligt vis lite för bearbetning innan den kan analyseras.

![data-process](media/how-to-designer-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Flyg data

Kolumnerna **Carrier**, **OriginAirportID**och **DestAirportID** sparas som heltal. De är dock kategoriska attribut, Använd modulen **Redigera metadata** för att konvertera dem till kategoriska.

![edit-metadata](media/how-to-designer-sample-classification-predict-flight-delay/edit-metadata.png)

Använd sedan modulen **Välj kolumner** i data uppsättning för att utesluta från data mängds kolumnerna som är möjliga mål läckor: **DepDelay**, **DepDel15**, **ArrDelay**, **canceled**, **Year**. 

Använd den schemalagda avgångs tiden som en av kopplings nycklarna för att ansluta till flyg poster med Tim väder poster. För att kunna göra kopplingen måste kolumnen CSRDepTime avrundas nedåt till närmaste timme, vilket görs i modulen **Kör R-skript** . 

### <a name="weather-data"></a>Väder data

Kolumner som har en stor del av saknade värden utesluts med hjälp av modulen **projekt kolumner** . Kolumnerna innehåller alla sträng värdes kolumner: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**och **StationPressure** .

Modulen **Rensa data som saknas** används sedan för de återstående kolumnerna för att ta bort rader med data som saknas.

Observations tiderna för väder avrundas uppåt till närmaste hela timmen. Schemalagda flyg tider och väder observations tiderna avrundas i motsatt riktning för att säkerställa att modellen endast använder väder före flygningen. 

Eftersom väder data rapporteras i lokal tid redovisas tids zons skillnaderna genom att subtrahera tids zons kolumnerna från den schemalagda sändnings tiden och väder observations tiden. Dessa åtgärder görs med hjälp av modulen **Kör R-skript** .

### <a name="joining-datasets"></a>Koppla data uppsättningar

Flight-poster är kopplade till väder data från flygets ursprung (**OriginAirportID**) med hjälp av modulen **koppla data** .

 ![Anslut till flyg och väder efter ursprung](media/how-to-designer-sample-classification-predict-flight-delay/join-origin.png)


Flight-poster är kopplade till väder data med hjälp av flygets mål (**DestAirportID**).

 ![Delta i flyg och väder per mål](media/how-to-designer-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Förbereda utbildning och test exempel

Modulen **dela data** delar upp data i april via september-poster för utbildning och oktober-poster för test.

 ![Dela utbildning och testa data](media/how-to-designer-sample-classification-predict-flight-delay/split.png)

Kolumnerna Year, Month och timezone tas bort från träning-datauppsättningen med hjälp av modulen Välj kolumner.

## <a name="define-features"></a>Definiera funktioner

I Machine Learning är funktioner enskilda mätbara egenskaper av något du är intresse rad av. Att hitta en stark uppsättning funktioner kräver experimentering och domän kunskap. Vissa funktioner är bättre för att förutsäga målet än andra. Vissa funktioner kan också ha en stark korrelation med andra funktioner och kommer inte att lägga till ny information i modellen. Dessa funktioner kan tas bort.

Om du vill bygga en modell kan du använda alla tillgängliga funktioner eller välja en delmängd av funktionerna.

## <a name="choose-and-apply-a-learning-algorithm"></a>Välja och tillämpa en inlärningsalgoritm

Skapa en modell med hjälp av en **logistik Regressions-modul i två klasser** och träna den på inlärnings data uppsättningen. 

Resultatet av modulen **träna modell** är en tränad klassificerings modell som kan användas för att skapa nya prover för att göra förutsägelser. Använd test uppsättningen för att generera Poäng från de tränade modellerna. Använd sedan modulen **utvärdera modell** för att analysera och jämföra modellens kvalitet.
pipeline när du har kört pipelinen kan du visa utdata från modulen **Poäng modell** genom att klicka på utdataporten och välja **visualisera**. Utdata innehåller de poäng etiketter och sannolikheter för etiketterna.

Om du vill testa kvaliteten på resultaten lägger du till modulen **utvärdera modell** i pipeline-arbetsytan och ansluter den vänstra Indataporten till utdata från modulen Poäng modell. Kör pipelinen och visa utdata från modulen **utvärdera modell** genom att klicka på utdataporten och välja **visualisera**.

## <a name="evaluate"></a>Utvärdera
Logistik Regressions modellen har AUC 0,631 på test uppsättningen.

 ![utvärdera](media/how-to-designer-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](how-to-designer-sample-classification-churn.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)