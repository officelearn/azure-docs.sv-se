---
title: Optimera algoritmer
titleSuffix: ML Studio (classic) - Azure
description: Förklarar hur du väljer den optimala parameter uppsättningen för en algoritm i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: ac3aa0dc619ec05dcd79a4f8740026b1eabc19aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427706"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Välj parametrar för att optimera algoritmerna i Azure Machine Learning Studio (klassisk)

I det här avsnittet beskrivs hur du väljer rätt aktiv parameter för en algoritm i Azure Machine Learning Studio (klassisk). De flesta maskininlärningsalgoritmer har parametrar för att konfigurera. När du tränar en modell, måste du ange värden för dessa parametrar. Effekt av den tränade modellen beror på Modellparametrar som du väljer. Processen att hitta den optimala uppsättningen parametrar som kallas *modellera val av*.



Det finns olika sätt att modellera val. I Machine Learning är kors validering en av de mest använda metoderna för modell val och det är standard mekanismen för modell val i Azure Machine Learning Studio (klassisk). Eftersom Azure Machine Learning Studio (klassisk) stöder både R och python kan du alltid implementera egna metoder för modell val genom att använda R eller python.

Det finns fyra steg håller på att hitta den bästa parameteruppsättningen:

1. **Definiera parametern utrymme**: Algorithm, börja med att bestämma de exakta parametervärden som du vill överväga.
2. **Ange inställningar för korsvalidering**: Bestäm hur du väljer korsvalidering vikningar för datauppsättningen.
3. **Definiera måttet**: Bestäm vilka mått som ska använda för att avgöra den bästa uppsättningen parametrar, till exempel noggrannhet, rot medelvärdet cirkels fel, precision, återkallande eller f-poäng.
4. **Träna, utvärdera och jämföra**: för varje unik kombination av parametervärdena som utförs av korsvalidering och baserat på fel-mått som du definierar. Du kan välja modellen med bäst prestanda efter utvärderingen och jämförelse.

Följande bild illustrerar hur detta kan uppnås i Azure Machine Learning Studio (klassisk).

![Hitta den bästa parameteruppsättningen](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definiera parametern-utrymme
Du kan definiera parameteruppsättning modellen initieringen steget. Fönstret parametrar av algoritmexempel som alla har två lägen för trainer: *enda Parameter* och *intervallet för parametern*. Välj parametern intervallet läge. Du kan ange flera värden för varje parameter i intervallet för parametern-läge. Du kan ange kommaseparerade värden i textrutan.

![Tvåklassförhöjt beslutsträd, enskild parameter](./media/algorithm-parameters-optimize/fig2.png)

 Alternativt kan du definiera de högsta och lägsta punkterna i rutnätet och det totala antalet punkter som ska genereras med **Använd intervallet Builder**. Som standard skapas parametrarnas värden på en linjär skala. Men om **logaritmisk skala** kontrolleras kan värdena som har genererats i logaritmisk skala (det vill säga förhållandet mellan de är konstant i stället för sin skillnad). Du kan definiera ett intervall för heltalsparametrar, med hjälp av ett bindestreck. Till exempel ”1-10” innebär att alla heltal mellan 1 och 10 (båda inkluderande) utgör parameteruppsättningen. Blandat läge stöds också. Till exempel parametern värdet ”1-10, 20, 50” omfattar 1-10, 20, heltal och 50.

![Tvåklassförhöjt beslutsträd, intervallet för parametern](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiera korsvalidering vikningar
Modulen [partition och exempel][partition-and-sample] kan användas för att slumpmässigt tilldela vik data. I följande exempelkonfiguration för modulen, vi definiera fem gånger och tilldela slumpmässigt flera vikning till exempel-instanser.

![Partitionera och ta prover](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definiera måttet
Modulen för att [finjustera modellens standardparametrar][tune-model-hyperparameters] ger stöd för att välja den bästa uppsättningen parametrar för en specifik algoritm och data uppsättning. Samt annan information angående träna modellen, de **egenskaper** av den här modulen innehåller mått för att avgöra den bästa parameteruppsättningen. Den har två olika nedrullningsbara listrutor för klassificering och regression algoritmer, respektive. Om algoritmen berörda är en klassificeringsalgoritm för, regression måttet ignoreras och vice versa. I det här exemplet, måttet är **Precision**.   

![Parametrar för svepvinkeln](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Träna, utvärdera och jämföra
Samma parameter för att [finjustera modellets][tune-model-hyperparameters] standardvärden är tågen alla modeller som motsvarar parameter uppsättningen, utvärderar olika mått och skapar sedan den bästa tränade modellen baserat på det mått som du väljer. Den här modulen har två obligatorisk indata:

* Omdöme eleven
* Datauppsättningen

Modulen har även en valfri datamängd som indata. Träffa vikning information till den obligatoriska datauppsättning indataporten datauppsättningen. Om datauppsättningen inte är tilldelad någon vikning information, körs automatiskt 10-fold korsvalidering som standard. Om vikning tilldelningen inte är klar och en datauppsättning för verifiering ges valfritt datauppsättning-port, ett valt träna och testning och den första datauppsättningen som används för att träna modellen för varje kombination av parametern.

![Klassificerare för beslutsträd trädet](./media/algorithm-parameters-optimize/fig6a.png)

Modellen utvärderas sedan för datauppsättningen för verifiering. Den vänstra utdataporten för modulen visar olika mått som funktioner för parametervärden. Rätt utdataporten får den tränade modellen som motsvarar till modellen bäst resultat enligt den valda måtten (**Precision** i det här fallet).  

![Verifiering datauppsättning](./media/algorithm-parameters-optimize/fig6b.png)

Du kan se exakt parametrarna valt genom att visualisera på rätt utdataporten. Den här modellen kan användas i bedömning ingen testmängd eller i en driftsatt webbtjänst när du har sparat som en tränad modell.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
