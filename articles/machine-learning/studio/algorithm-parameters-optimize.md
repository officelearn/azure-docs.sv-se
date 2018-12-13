---
title: Optimera algoritmer – Azure Machine Learning Studio | Microsoft Docs
description: Beskriver hur du väljer den optimala parameteruppsättning för en algoritm i Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 57561c9841297b7ea2991bda1e94065a854597cd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269421"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio"></a>Välj parametrar för att optimera algoritmerna i Azure Machine Learning Studio

Det här avsnittet beskriver hur du väljer rätt finjustering för en algoritm i Azure Machine Learning. De flesta maskininlärningsalgoritmer har parametrar för att konfigurera. När du tränar en modell, måste du ange värden för dessa parametrar. Effekt av den tränade modellen beror på Modellparametrar som du väljer. Processen att hitta den optimala uppsättningen parametrar som kallas *modellera val av*.



Det finns olika sätt att modellera val. Korsvalidering är en av de mest använda metoderna för vald modell i machine learning, och det är standardmekanismen för modellen val i Azure Machine Learning. Eftersom Azure Machine Learning har stöd för både R och Python, kan du alltid implementera sina egna mekanismer för val av modellen genom att använda R eller Python.

Det finns fyra steg håller på att hitta den bästa parameteruppsättningen:

1. **Definiera parametern utrymme**: Algoritmen, först bestämma de exakta parametervärden som du vill överväga.
2. **Ange inställningar för korsvalidering**: Bestäm hur du väljer korsvalidering vikningar för datauppsättningen.
3. **Definiera måttet**: Bestäm vilka mått som ska använda för att avgöra den bästa uppsättningen parametrar, till exempel noggrannhet, rot medelvärdet cirkels fel, precision, återkallande eller f-poäng.
4. **Träna, utvärdera och jämföra**: För varje unik kombination av parametervärdena som utförs av korsvalidering och baserat på fel-mått som du definierar. Du kan välja modellen med bäst prestanda efter utvärderingen och jämförelse.

Följande bild illustrerar visar hur du kan göra detta i Azure Machine Learning.

![Hitta den bästa parameteruppsättningen](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definiera parametern-utrymme
Du kan definiera parameteruppsättning modellen initieringen steget. Fönstret parametrar med alla maskininlärningsalgoritmer har två trainer lägen: *Enkel parametern* och *intervallet för parametern*. Välj parametern intervallet läge. Du kan ange flera värden för varje parameter i intervallet för parametern-läge. Du kan ange kommaseparerade värden i textrutan.

![Tvåklassförhöjt beslutsträd, enskild parameter](./media/algorithm-parameters-optimize/fig2.png)

 Alternativt kan du definiera de högsta och lägsta punkterna i rutnätet och det totala antalet punkter som ska genereras med **Använd intervallet Builder**. Som standard skapas parametrarnas värden på en linjär skala. Men om **logaritmisk skala** kontrolleras kan värdena som har genererats i logaritmisk skala (det vill säga förhållandet mellan de är konstant i stället för sin skillnad). Du kan definiera ett intervall för heltalsparametrar, med hjälp av ett bindestreck. Till exempel ”1-10” innebär att alla heltal mellan 1 och 10 (båda inkluderande) utgör parameteruppsättningen. Blandat läge stöds också. Till exempel parametern värdet ”1-10, 20, 50” omfattar 1-10, 20, heltal och 50.

![Tvåklassförhöjt beslutsträd, intervallet för parametern](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiera korsvalidering vikningar
Den [partitionera och ta prover] [ partition-and-sample] modulen kan användas för att slumpmässigt tilldelas vikningar data. I följande exempelkonfiguration för modulen, vi definiera fem gånger och tilldela slumpmässigt flera vikning till exempel-instanser.

![Partitionera och ta prover](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definiera måttet
Den [Justeringsmodeller] [ tune-model-hyperparameters] modulen har stöd för empiriskt välja den bästa uppsättningen parametrar för en viss algoritm och datauppsättning. Samt annan information angående träna modellen, de **egenskaper** av den här modulen innehåller mått för att avgöra den bästa parameteruppsättningen. Den har två olika nedrullningsbara listrutor för klassificering och regression algoritmer, respektive. Om algoritmen berörda är en klassificeringsalgoritm för, regression måttet ignoreras och vice versa. I det här exemplet, måttet är **Precision**.   

![Parametrar för svepvinkeln](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Träna, utvärdera och jämföra
Samma [Justeringsmodeller] [ tune-model-hyperparameters] modulen tåg alla modeller som motsvarar parametern anger, utvärderar olika mått och skapar sedan den bäst tränade modellen baserat på måtten du Välj. Den här modulen har två obligatorisk indata:

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
