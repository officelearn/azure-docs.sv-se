---
title: Optimera algoritmer
titleSuffix: ML Studio (classic) - Azure
description: I artikeln beskrivs hur du väljer den optimala parameteruppsättningen för en algoritm i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218275"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Välj parametrar för att optimera dina algoritmer i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

I det här avsnittet beskrivs hur du väljer rätt hyperparameteruppsättning för en algoritm i Azure Machine Learning Studio (klassisk). De flesta maskininlärningsalgoritmer har parametrar att ställa in. När du tränar en modell måste du ange värden för dessa parametrar. Effekten av den tränade modellen beror på de modellparametrar som du väljer. Processen att hitta den optimala uppsättningen parametrar kallas *modellval*.



Det finns olika sätt att göra modellval. I maskininlärning är korsvalning en av de mest använda metoderna för modellval, och det är standardmodellens urvalsmekanism i Azure Machine Learning Studio (klassisk). Eftersom Azure Machine Learning Studio (klassisk) stöder både R och Python kan du alltid implementera deras egna modellvalsmekanismer med hjälp av antingen R eller Python.

Det finns fyra steg i processen för att hitta den bästa parameteruppsättningen:

1. **Definiera parameterutrymmet:** För algoritmen bestämmer du först de exakta parametervärden som du vill ta hänsyn till.
2. **Definiera inställningarna för korsverifiering:** Bestäm hur du väljer korsvalideringsveckor för datauppsättningen.
3. **Definiera måttet:** Bestäm vilket mått som ska användas för att bestämma den bästa uppsättningen parametrar, till exempel noggrannhet, rotmedelsrutade fel, precision, återkallande eller f-poäng.
4. **Träna, utvärdera och jämföra**: För varje unik kombination av parametervärdena utförs korsvalidering av och baseras på det felmått som du definierar. Efter utvärdering och jämförelse kan du välja den modell som har bäst resultat.

Följande bild illustrerar hur detta kan uppnås i Azure Machine Learning Studio (klassisk).

![Hitta den bästa parameteruppsättningen](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definiera parameterutrymmet
Du kan definiera parameteruppsättningen vid modellinitualiseringssteget. Parameterfönstret för alla maskininlärningsalgoritmer har två träningslägen: *Single Parameter* och *Parameter Range*. Välj parameterområdesläge. I parameterområdesläge kan du ange flera värden för varje parameter. Du kan ange kommaavgränsade värden i textrutan.

![Tvåklassigt förstärkt beslutsträd, en parameter](./media/algorithm-parameters-optimize/fig2.png)

 Alternativt kan du definiera det högsta och lägsta antalet punkter i rutnätet och det totala antalet punkter som ska genereras med **Använd Range Builder**. Som standard genereras parametervärdena på en linjär skala. Men om **loggskala** är markerad genereras värdena i loggskalan (det vill säga förhållandet mellan de intilliggande punkterna är konstant i stället för deras skillnad). För heltalsparametrar kan du definiera ett intervall med hjälp av ett bindestreck. Till exempel betyder "1-10" att alla heltal mellan 1 och 10 (båda inklusive) utgör parameteruppsättningen. Ett blandat läge stöds också. Parameteruppsättningen "1-10, 20, 50" skulle till exempel innehålla heltal 1-10, 20 och 50.

![Tvåklassade förstärkta beslutsträd, parameterintervall](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiera korsvalningsveck
[Partitions- och exempelmodulen][partition-and-sample] kan användas för att slumpmässigt tilldela vikter till data. I följande exempelkonfiguration för modulen definierar vi fem veck och tilldelar slumpmässigt ett viknummer till exempelinstanserna.

![Partition och exempel](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definiera måttet
[Modulen Tune Model Hyperparameters][tune-model-hyperparameters] ger stöd för att empiriskt välja den bästa uppsättningen parametrar för en viss algoritm och datauppsättning. Förutom annan information om utbildning av modellen innehåller **egenskapsfönstret** för den här modulen måttet för att bestämma den bästa parameteruppsättningen. Den har två olika listrutor för klassificerings- respektive regressionsalgoritmer. Om algoritmen som övervägs är en klassificeringsalgoritm ignoreras regressionsmåttet och vice versa. I det här specifika exemplet är måttet **Noggrannhet**.   

![Rensa parametrar](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Träna, utvärdera och jämföra
Samma [Tune Model Hyperparameters][tune-model-hyperparameters] modul tränar alla modeller som motsvarar parameteruppsättningen, utvärderar olika mått och skapar sedan den bäst tränade modellen baserat på det mått du väljer. Denna modul har två obligatoriska ingångar:

* Den outbildade eleven
* Datauppsättningen

Modulen har också en valfri datauppsättningsinmatning. Anslut datauppsättningen med vikinformation till den obligatoriska datauppsättningsinmatningen. Om datauppsättningen inte tilldelas någon vikinformation körs en 10-faldig korsvalidering automatiskt som standard. Om viktilldelningen inte görs och en valideringsdatauppsättning tillhandahålls vid den valfria datamängdsporten väljs ett tågtestläge och den första datauppsättningen används för att träna modellen för varje parameterkombination.

![Förstärkt beslutsträd klassificerare](./media/algorithm-parameters-optimize/fig6a.png)

Modellen utvärderas sedan på valideringsdatauppsättningen. Modulens vänstra utdataport visar olika mått som funktioner för parametervärden. Rätt utdataport ger den tränade modellen som motsvarar den bäst presterande modellen enligt det valda måttet (**Noggrannhet** i det här fallet).  

![Valideringsdatauppsättning](./media/algorithm-parameters-optimize/fig6b.png)

Du kan se de exakta parametrar som valts genom att visualisera rätt utdataport. Den här modellen kan användas för att göra en testuppsättning eller i en operationaliserad webbtjänst efter att ha sparat som en utbildad modell.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
