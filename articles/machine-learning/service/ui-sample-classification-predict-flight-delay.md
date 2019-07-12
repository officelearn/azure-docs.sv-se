---
title: 'Klassificering: Förutse flygförseningar'
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du skapar en maskininlärningsmodell för att förutsäga flygförseningar med visuella dra och släpp-gränssnittet och anpassade R-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607642"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Exempel 6 - klassificering: Förutsäga flygförseningar med R

Det här experimentet använder historiska flygning och weather-data för att förutsäga om en schemalagd passagerartrafik flygning kommer att fördröjas av mer än 15 minuter.

Det här problemet kan vara närmat sig som en klassificeringsproblem att förutsäga två klasser--fördröjd, eller i tid. Skapa en klassificerare, den här modellen med hjälp av ett stort antal exempel från historiska flygning data.

Här är den sista experimentdiagram:

[![Diagram över experimentet](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj den **öppna** knappen för experimentet exempel 6:

    ![Öppna experimentet](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Hämta data

Detta experimentera använder den **flygning fördröjningar Data** datauppsättning. Det är en del av TranStats datainsamling från USA Department of Transportation. Datauppsättningen innehåller flygning fördröjning information från April till oktober 2013. Innan du skickar data till det visuella gränssnittet, har det redan bearbetats på följande sätt:

* Filtrerade med 70 mest använda flygplatser i kontinentala USA.
* Avbröt flyg, relabeled som försenade på grund av mer än 15 minuter.
* Filtreras bort och distribueras flyg.
* Valda 14 kolumner.

Du behöver data rör sig den **väder datauppsättning** används. Väderdata innehåller per timme mark-baserade väder observationer från amerikanska NOAA och representerar observationer från flygplats väder stationer, som omfattar samma tid då April – oktober 2013. Innan du skickar till Azure ML visuella gränssnittet, har det redan bearbetats på följande sätt:

* -Väderstation, ID: N har mappats till motsvarande flygplats ID: N.
* Väder stationer som inte är associerade med 70 mest använda flygplatser togs bort.
* Datumkolumnen är uppdelad i separata kolumner: År, månad och dag.
* Valda 26 kolumner.

## <a name="pre-process-the-data"></a>Förbearbeta data

En datauppsättning kräver vanligtvis viss förbearbetning innan de kan analyseras.

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Flight data

Kolumnerna **operatör**, **OriginAirportID**, och **DestAirportID** sparas som heltal. De kommer dock kategoriska attribut, Använd den **redigera Metadata** modulen man konverterar dem till kategoriska.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Använd sedan den **Välj kolumner** i datauppsättningen modulen ska undantas från kolumner för datauppsättningar som är möjligt mål leakers: **DepDelay**, **DepDel15**, **ArrDelay**, **har avbrutits**, **år**. 

För att ansluta till flygning poster med timvis väder-poster, använder du schemalagd avgångstid som en av nycklarna join. Om du vill göra kopplingen kolumnen CSRDepTime måste avrundas nedåt till närmaste timme, vilket gör du genom att i den **kör R-skript** modulen. 

### <a name="weather-data"></a>Weather-data

Kolumner som har en stor del av värden som saknas är undantagna med hjälp av den **Projektkolumner** modulen. Dessa kolumner är alla strängvärden kolumner: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, och **StationPressure**.

Den **Rensa Data som saknas** modulen sedan appliceras till övriga kolumner för att ta bort rader med data som saknas.

Väder Fjärrvisning gånger avrundas uppåt till närmaste hela timmen. Schemalagd flygning gånger och väder Fjärrvisning tider avrundas i motsatt riktning så använder modellen bara väder före den rör sig tiden. 

Eftersom weather-data rapporteras i lokal tid, redovisas tidszonskillnader i genom att subtrahera tidszon kolumner från schemalagda avgång och väder Fjärrvisning av tiden. De här åtgärderna är klar med den **kör R-skript** modulen.

### <a name="joining-datasets"></a>Ansluta till datauppsättningar

Flight poster är anslutna med weather-data på ursprung flygningens längd (**OriginAirportID**) med hjälp av den **ansluta Data** modulen.

 ![gör som rör sig och väder efter ursprung](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Flight poster är anslutna med weather-data med målet flygningens längd (**DestAirportID**).

 ![Gör som rör sig och väder efter mål](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Förbereda utbildning och Test-exempel

Den **dela Data** modulen delar upp data till April igenom September poster för träning och oktober poster för testning.

 ![Dela upp utbildning och testa data](media/ui-sample-classification-predict-flight-delay/split.png)

År, månad och tidszon kolumner tas bort från datauppsättning för träning med hjälp av modulen Välj kolumner.

## <a name="define-features"></a>Definiera funktioner

Machine learning, är funktioner enskilda mätbara egenskaper av något du är intresserad av. Hitta en rik uppsättning funktioner kräver kunskaper om experimentering och domän. Vissa funktioner är bättre för att förutsäga målet än andra. Dessutom vissa funktioner kan ha en stark korrelation med andra funktioner och lägga till inte ny information i modellen. Dessa funktioner kan tas bort.

Du kan använda alla tillgängliga funktioner eller välja en delmängd av funktionerna för att skapa en modell.

## <a name="choose-and-apply-a-learning-algorithm"></a>Välja och tillämpa en inlärningsalgoritm

Skapa en modell med hjälp av den **Tvåklassförhöjt Logistic Regression** modulen och träna på datauppsättning för träning. 

Resultatet av den **Träningsmodell** modul är en tränad klassificerings-modell som kan användas för att poängsätta nya prov för att göra förutsägelser. Använd testet att generera resultat från de tränade modellerna. Använd sedan den **utvärdera modell** modulen att analysera och jämföra kvaliteten på modellerna.

När du har kört experimentet, du kan visa utdata från den **Poängmodell** modulen genom att klicka på utdataporten och välja **visualisera**. Utdata innehåller poängsatta etiketter och sannolikheter för etiketter.

Slutligen testa resultatets kvalitet, lägger du till den **utvärdera modell** modulen till arbetsytan för experimentet och koppla den vänstra indataporten till utdataporten för modulen poängsätta modell. Kör experimentet och visa resultatet av den **utvärdera modell** modulen genom att klicka på utdataporten och välja **visualisera**.

## <a name="evaluate"></a>Utvärdera
Logistic regression-modellen har AUC av 0.631 på testet ange.

 ![utvärdera](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – Regression: Förutsäga priset på en bil](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – Regression: Jämför algoritmer för bil pris förutsägelse](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsäga kreditrisk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 - klassificering: Förutsäga kundomsättning](ui-sample-classification-predict-churn.md)
