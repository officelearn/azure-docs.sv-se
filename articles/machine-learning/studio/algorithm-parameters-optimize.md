---
title: Optimera algoritmerna i Azure Machine Learning | Microsoft Docs
description: "Beskriver hur du väljer den optimala parameter har angetts för en algoritm i Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 8f1d9b48e505bef0ebd622267705c3e7e26a3a47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Välj parametrar för att optimera algoritmerna i Azure Machine Learning
Det här avsnittet beskriver hur du väljer rätt hyperparameter för en algoritm i Azure Machine Learning. De flesta maskininlärningsalgoritmer har parametrar för att konfigurera. Du måste ange värden för dessa parametrar när du tränar en modell. Effekt av den tränade modellen beror på Modellparametrar som du väljer. Processen för att hitta den optimala uppsättningen av parametrar kallas *modell markeringen*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Det finns olika sätt att modellera val. Korsvalidering är en av de vanligaste metoderna för vald modell i machine learning och det är standardmekanism för modellen val i Azure Machine Learning. Eftersom Azure Machine Learning stöder både R och Python kan implementera du alltid egna mekanismer för val av modellen genom att använda R eller Python.

Det finns fyra steg håller på att söka efter den bästa parameteruppsättningen:

1. **Definiera parametern utrymme**: först bestämma de exakta parametervärden som du vill överväga för algoritmen.
2. **Ange inställningar för korsvalidering**: Bestäm hur du väljer korsvalidering vikningar för datauppsättningen.
3. **Definiera måttet**: Bestäm vilka mått som ska användas för att avgöra den bästa uppsättningen parametrar, till exempel noggrannhet, roten medelvärdet kvadrat fel, precision, återkalla eller f poäng.
4. **Träna, utvärdera och jämföra**: för varje unik kombination av parametervärdena som utförs av korsvalidering och baserat på fel måttet som du definierar. Du kan välja bäst prestanda modellen efter utvärderingen och jämförelse.

Följande bild illustrerar visar hur du kan göra detta i Azure Machine Learning.

![Hitta den bästa parameteruppsättningen](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definiera parametern utrymme
Du kan definiera parameteruppsättningen modellen initieringen steget. Parametern-rutan i alla maskininlärningsalgoritmer har två trainer lägen: *enskild Parameter* och *parametern intervallet*. Välj parametern intervallet läge. Du kan ange flera värden för varje parameter i intervallet för parametern-läge. Du kan ange kommaseparerade värden i textrutan.

![Två-klass förstärkta beslutsträd, enskild parameter](./media/algorithm-parameters-optimize/fig2.png)

 Alternativt kan du ange högsta och lägsta punkterna i rutnätet och det totala antalet punkter som ska genereras med **Använd intervallet Builder**. Som standard genereras parametervärden på en linjär skala. Men om **logaritmisk skala** kontrolleras kan värdena som ska genereras i logaritmisk skala (förhållandet mellan de är konstant i stället för deras skillnaden). Du kan definiera ett intervall för heltal parametrar, med bindestreck. Till exempel ”1-10” innebär att alla heltal mellan 1 och 10 (båda inkluderande) formuläret parameteruppsättningen. Blandat läge stöds också. Till exempel parametern värdet ”1-10, 20, 50” innefattar heltal 1-10, 20, och 50.

![Två-klass förstärkta beslutsträd, intervallet för parametern](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiera korsvalidering vikningar
Den [partitionera och ta prover] [ partition-and-sample] modulen kan användas för att slumpmässigt tilldelas vikningar data. I följande exempelkonfiguration för modulen, vi definiera fem gånger och tilldela slumpmässigt vikning nummer till exempel instanser.

![Partitionera och ta prover](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definiera måttet
Den [Justeringsmodeller] [ tune-model-hyperparameters] modulen ger stöd för empiriskt att välja den bästa uppsättningen parametrar för en given algoritmen och dataset. Förutom andra information angående träna modellen, den **egenskaper** rutan i den här modulen innehåller mått för att avgöra den bästa parameteruppsättningen. Den har två olika nedrullningsbara listrutor för klassificering och regression algoritmer respektive. Om algoritmen berörda är en klassificeringsalgoritm för, regression måttet ignoreras och vice versa. I det här exemplet, måttet är **noggrannhet**.   

![Svep parametrar](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Träna, utvärdera och jämföra
Samma [Justeringsmodeller] [ tune-model-hyperparameters] modulen tränar alla modeller som motsvarar parameteruppsättningen utvärderar olika mått och skapar sedan den bäst tränade modellen baserat på det mått som du väljer. Modulen har två obligatoriska indata:

* Omdöme deltagaren
* Datauppsättningen

Modulen har även en valfri datauppsättning som indata. Ansluta datauppsättningen med vikning information till obligatoriska dataset-indata. Om datamängden inte är tilldelad någon vikning information, utförs automatiskt en 10-fold korsvalidering som standard. Om vikning tilldelningen sker inte och en verifiering dataset tillhandahålls på valfri dataset-port, train testläge är valt och den första datauppsättningen som används för att träna modellen för varje parameter-kombination.

![Förstärkta trädet klassificerare](./media/algorithm-parameters-optimize/fig6a.png)

Modellen utvärderas sedan för verifiering datauppsättningen. Den vänstra utdataporten för modulen visas olika mått som funktioner i parametervärden. Den högra utdataporten ger den tränade modellen som motsvarar till bäst prestanda modellen enligt det valda måttet (**noggrannhet** i detta fall).  

![Verifieringen dataset](./media/algorithm-parameters-optimize/fig6b.png)

Du kan se exakt parametrarna som visualisera rätt utdataporten valt. Den här modellen kan användas i poängsättning av en uppsättning test eller en operationalized webbtjänst när du har sparat som en tränad modell.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
