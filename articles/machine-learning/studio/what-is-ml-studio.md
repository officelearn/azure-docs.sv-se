---
title: Vad är ML Studio (klassisk)
titleSuffix: Azure
description: Azure Machine Learning Studio (klassisk) är ett dra-och-släpp-verktyg för att snabbt skapa modeller från ett färdigt bibliotek med algoritmer och moduler.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: c1772ceb514e46542129759711f2d45db39abf82
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371977"
---
# <a name="what-is-machine-learning-studio-classic"></a>Vad är Machine Learning Studio (klassisk)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klassisk) är ett samarbete, dra och släpp-verktyg som du kan använda för att bygga, testa och distribuera förutsägelse analys lösningar på dina data. Azure Machine Learning Studio (klassisk) publicerar modeller som webb tjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel.

Machine Learning Studio (klassisk) är den plats där data vetenskap, förutsägelse analys, moln resurser och dina data är uppfyllda.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Den interaktiva arbets ytan Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

För att utveckla en förutsägelse analys modell använder du vanligt vis data från en eller flera källor, transformerar och analyserar dessa data via olika data manipulations-och statistik funktioner och genererar en uppsättning resultat. Att utveckla en modell som den här är en process som är baserad på upprepningar (iteration). När du ändrar olika funktionerna och deras parametrar kommer resultaten att konvergera tills du är nöjd och säker på att du har en tränad, effektiv modell.

Azure Machine Learning Studio (klassisk) ger dig en interaktiv, visuell arbets yta som enkelt kan bygga, testa och iterera på en förutsägelse analys modell. Du kan dra och släppa ***data uppsättningar*** och analys ***moduler*** till en interaktiv arbets yta och ansluta dem till ett ***experiment***, som du kör i Machine Learning Studio (klassisk). Om du vill köra en iteration med modelldesignen, redigerar du experimentet, sparar en kopia om du så önskar och sedan kör du försöket igen. När du är klar kan du konvertera ditt ***träningsexperiment*** till ett ***prediktivt experiment***, och sedan publicera det som en ***webbtjänst*** så att din modell kan användas av andra.

Ingen programmering krävs, visuellt ansluta data uppsättningar och moduler för att skapa en förutsägelse analys modell.

![Azure Machine Learning Studio-diagram (klassiskt): Skapa experiment, läsa data för många källor, skriva Poäng data, skriva modeller.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Ladda ned översikts diagrammet för Machine Learning Studio (klassisk)
Ladda ned **översikts diagrammet över Microsoft Azure Machine Learning Studio (klassisk)** och få en överblick på hög nivå av funktionerna i Machine Learning Studio (klassisk). Om du vill ha det nära tillhands kan du skriva ut det i tabloidformat (280 x 385 mm).

**Hämta diagrammet här: [Översikt](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![över funktioner i Microsoft Azure Machine Learning Studio (klassisk) Microsoft Azure Machine Learning Studio (klassisk)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Komponenter i ett Studio-experiment (klassisk)
Ett experiment består av datauppsättningar som förser de analytiska modulerna med data. Du kopplar sedan samman dessa moduler för att skapa en prediktiv analysmodell. Mer konkret uttryckt så har ett giltigt experiment följande egenskaper:

* Experimentet har minst en datauppsättning och en modul
* Datauppsättningar kan bara anslutas till moduler
* Moduler kan anslutas antingen till datauppsättningar eller till andra moduler
* Alla indataportar för moduler måste ha någon typ av koppling till dataflödet
* Alla nödvändiga parametrar måste anges för varje modul

Du kan skapa ett experiment från grunden eller så kan du använda ett befintligt exempelexperiment som mall. Mer information finns i avsnittet [Kopiera exempelexperiment för att skapa nya maskininlärningsexperiment](sample-experiments.md).

Ett exempel på hur du skapar ett experiment finns i [skapa ett enkelt experiment i Azure Machine Learning Studio (klassisk)](create-experiment.md).

En mer fullständig genom gång av hur du skapar en förutsägelse analys lösning finns i [utveckla en förutsägelse lösning med Azure Machine Learning Studio (klassisk)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datauppsättningar
En data uppsättning är data som har överförts till Machine Learning Studio (klassisk) så att de kan användas i modellerings processen. Ett antal exempel data uppsättningar ingår i Machine Learning Studio (klassisk) som du kan experimentera med och du kan ladda upp fler data uppsättningar när du behöver dem. Här följer några exempel på datauppsättningar som ingår:

* **MPG-data för olika bilar** – MPG-värden (Miles Per Gallon, USA:s motsvarighet till liter per 100 km) för bilar beräknat utifrån antalet cylindrar, hästkrafter o.s.v.
* **Bröstcancerdata** – Data för bröstcancerdiagnoser.
* **Data om skogsbränder** – Storleken på skogsbränder i nordöstra Portugal.

När du skapar ett experiment kan du välja från listan över data uppsättningar som är tillgängliga till vänster på arbets ytan.

En lista med exempel data uppsättningar som ingår i Machine Learning Studio (klassisk) finns i [använda exempel data uppsättningar i Azure Machine Learning Studio (klassisk)](use-sample-datasets.md).

### <a name="modules"></a>Moduler
En modul är en algoritm som du kan tillämpa på dina data. Azure Machine Learning Studio (klassisk) har ett antal moduler som sträcker sig från data ingångs funktioner till inlärnings-, poängsättnings-och validerings processer. Här följer några exempel på moduler som ingår:

* [Konvertera till ARFF][convert-to-arff] – Konverterar en .NET-serialiserad datauppsättning till formatet ARFF (Attribute-Relation File Format).
* [Beräkna elementär statistik][elementary-statistics] – Beräknar elementär statistik, som medelvärde, standardavvikelse osv.
* [Linjär regression][linear-regression] – Skapar en linjär regressionsmodell baserad på brantaste lutningsmetoden online.
* [Bedömningsmodell][score-model] – Bedömer en tränad klassificerings- eller regressionsmodell.

När du skapar ett experiment kan du välja från listan över moduler som är tillgängliga till vänster på arbets ytan.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbetsytan modulens visas modulens parametrar i fönstret **Egenskaper** på arbetsytans högra sida. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

Information om hur du navigerar genom det stora biblioteket med Machine Learning-algoritmer finns i [så här väljer du algoritmer för Microsoft Azure Machine Learning Studio (klassisk)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Distribuera en prediktiv analysmodell som webbtjänst
När din förutsägelse analys modell är klar kan du distribuera den som en webb tjänst direkt från Machine Learning Studio (klassisk). Mer information om den här processen finns i [distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Nästa steg
Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att gå en [snabbstart med stegvisa anvisningar](create-experiment.md) och genom att [bygga vidare på prover](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
