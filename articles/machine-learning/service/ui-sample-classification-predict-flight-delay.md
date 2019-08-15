---
title: 'Exempel på visuella gränssnitt #6: Klassificering för att förutsäga flyg fördröjningar'
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du skapar en maskin inlärnings modell för att förutsäga fördröjningar med hjälp av Visual-och-Drop-gränssnittet och anpassad R-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 4c0a990ae3f45fc7b08c157f180d8ecf805c24e6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990028"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Exempel 6 – klassificering: Förutsäg fördröjningar i flygning med R

Det här experimentet använder historiska flyg-och väder data för att förutsäga om en schemalagd passagerar flygning kommer att fördröjas med mer än 15 minuter.

Det här problemet kan uppstå som ett klassificerings problem, som förutsäger två klasser – fördröjd eller i tid. För att bygga en klassificerare använder den här modellen ett stort antal exempel från historiska flyg data.

Här är det sista experiment diagrammet:

[![Diagram över experimentet](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel på 6 experiment:

    ![Öppna experimentet](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Hämta data

I det här experimentet används data uppsättningen för **flyg fördröjning** . Den är en del av data insamlingen TranStats från USA Transport ministerium. Data uppsättningen innehåller information om flyg fördröjning från april till oktober 2013. Innan du överför data till det visuella gränssnittet har den förbehandlats på följande sätt:

* Filtrerat för att inkludera vanligaste-flyg platser på 70 i kontinental USA.
* För avbrutna flygningar får du etiketterat som fördröjt av mer än 15 minuter.
* Filtrerat bort inledade flygningar.
* Markerade 14 kolumner.

För att komplettera flyg data används **väder data uppsättningen** . Väder data innehåller en timmes markbaserade väderleks observationer från NOAA och representerar observationer från flyg plats väderleks stationer som omfattar samma tids period april-oktober 2013. Innan du överför till det visuella Azure ML-gränssnittet har det förbehandlats på följande sätt:

* Väder Stations-ID: n mappades till motsvarande flyg plats-ID.
* Väder stationer som inte är kopplade till 70 vanligaste-flyg platserna togs bort.
* Datum kolumnen delades upp i separata kolumner: År, månad och dag.
* De markerade 26 kolumnerna.

## <a name="pre-process-the-data"></a>För behandling av data

En data uppsättning kräver vanligt vis lite för bearbetning innan den kan analyseras.

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Flyg data

Kolumnerna **Carrier**, **OriginAirportID**och **DestAirportID** sparas som heltal. De är dock kategoriska attribut, Använd modulen **Redigera metadata** för att konvertera dem till kategoriska.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Använd sedan modulen **Välj kolumner** i data uppsättning för att utesluta från data mängds kolumnerna som är möjliga mål läckor: **DepDelay**, **DepDel15**, **ArrDelay**,avbrutet, **år**. 

Använd den schemalagda avgångs tiden som en av kopplings nycklarna för att ansluta till flyg poster med Tim väder poster. För att kunna göra kopplingen måste kolumnen CSRDepTime avrundas nedåt till närmaste timme, vilket görs i modulen **Kör R-skript** . 

### <a name="weather-data"></a>Väder data

Kolumner som har en stor del av saknade värden utesluts med hjälp av modulen **projekt kolumner** . Kolumnerna innehåller alla sträng värdes kolumner: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**och **StationPressure**.

Modulen **Rensa data som saknas** används sedan för de återstående kolumnerna för att ta bort rader med data som saknas.

Observations tiderna för väder avrundas uppåt till närmaste hela timmen. Schemalagda flyg tider och väder observations tiderna avrundas i motsatt riktning för att säkerställa att modellen endast använder väder före flygningen. 

Eftersom väder data rapporteras i lokal tid redovisas tids zons skillnaderna genom att subtrahera tids zons kolumnerna från den schemalagda sändnings tiden och väder observations tiden. Dessa åtgärder görs med hjälp av modulen **Kör R-skript** .

### <a name="joining-datasets"></a>Koppla data uppsättningar

Flight-poster är kopplade till väder data från flygets ursprung (**OriginAirportID**) med hjälp av modulen **koppla data** .

 ![Anslut till flyg och väder efter ursprung](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Flight-poster är kopplade till väder data med hjälp av flygets mål (**DestAirportID**).

 ![Delta i flyg och väder per mål](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Förbereda utbildning och test exempel

Modulen **dela data** delar upp data i april via september-poster för utbildning och oktober-poster för test.

 ![Dela utbildning och testa data](media/ui-sample-classification-predict-flight-delay/split.png)

Kolumnerna Year, Month och timezone tas bort från träning-datauppsättningen med hjälp av modulen Välj kolumner.

## <a name="define-features"></a>Definiera funktioner

I Machine Learning är funktioner enskilda mätbara egenskaper av något du är intresse rad av. Att hitta en stark uppsättning funktioner kräver experimentering och domän kunskap. Vissa funktioner är bättre för att förutsäga målet än andra. Vissa funktioner kan också ha en stark korrelation med andra funktioner och kommer inte att lägga till ny information i modellen. Dessa funktioner kan tas bort.

Om du vill bygga en modell kan du använda alla tillgängliga funktioner eller välja en delmängd av funktionerna.

## <a name="choose-and-apply-a-learning-algorithm"></a>Välja och tillämpa en inlärningsalgoritm

Skapa en modell med hjälp av en **logistik Regressions-modul i två klasser** och träna den på inlärnings data uppsättningen. 

Resultatet av modulen **träna modell** är en tränad klassificerings modell som kan användas för att skapa nya prover för att göra förutsägelser. Använd test uppsättningen för att generera Poäng från de tränade modellerna. Använd sedan modulen **utvärdera modell** för att analysera och jämföra modellens kvalitet.

När du har kört experimentet kan du visa utdata från modulen **Poäng modell** genom att klicka på utdataporten och välja **visualisera**. Utdata innehåller de poäng etiketter och sannolikheter för etiketterna.

Om du vill testa kvaliteten på resultaten lägger du till modulen **utvärdera modell** till arbets ytan för experimentet och ansluter den vänstra Indataporten till utdata från modulen Poäng modell. Kör experimentet och visa utdata från modulen **utvärdera modell** genom att klicka på utdataporten och välja **visualisera**.

## <a name="evaluate"></a>Utvärdera
Logistik Regressions modellen har AUC 0,631 på test uppsättningen.

 ![utvärdera](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: Förutsäga ett bils pris](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – regression: Jämför algoritmer för bilpris förutsägelser för bilar](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsägelse kredit risk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: Förutsäg omsättning](ui-sample-classification-predict-churn.md)
