---
title: Vad är ML Studio (klassisk)
titleSuffix: Azure
description: Azure Machine Learning Studio (klassisk) är ett dra-och-släpp-verktyg för att snabbt skapa modeller från ett färdigt bibliotek med algoritmer och moduler.
services: machine-learning
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: 07629a17492d0c898bbd76aeeab6c84cf39b4b55
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152711"
---
# <a name="what-is-machine-learning-studio-classic"></a>Vad är Machine Learning Studio (klassisk)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klassisk) är ett samarbete, dra och släpp-verktyg som du kan använda för att bygga, testa och distribuera förutsägelse analys lösningar på dina data. Azure Machine Learning Studio (klassisk) publicerar modeller som webb tjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel.

Machine Learning Studio (klassisk) är den plats där data vetenskap, förutsägelse analys, moln resurser och dina data är uppfyllda.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Den interaktiva arbets ytan Machine Learning Studio (klassisk)
För att utveckla en förutsägelse analys modell använder du vanligt vis data från en eller flera källor, transformerar och analyserar dessa data via olika data manipulations-och statistik funktioner och genererar en uppsättning resultat. Att utveckla en modell som den här är en process som är baserad på upprepningar (iteration). När du ändrar olika funktionerna och deras parametrar kommer resultaten att konvergera tills du är nöjd och säker på att du har en tränad, effektiv modell.

Azure Machine Learning Studio (klassisk) ger dig en interaktiv, visuell arbets yta som enkelt kan bygga, testa och iterera på en förutsägelse analys modell. Du kan dra och släppa ***data uppsättningar*** och analys ***moduler*** till en interaktiv arbets yta och ansluta dem till ett ***experiment***, som du kör i Machine Learning Studio (klassisk). Om du vill köra en iteration med modelldesignen, redigerar du experimentet, sparar en kopia om du så önskar och sedan kör du försöket igen. När du är klar kan du konvertera ditt ***träningsexperiment*** till ett ***prediktivt experiment***, och sedan ***publicera*** det som en webbtjänst så att din modell kan användas av andra.

Ingen programmering krävs, visuellt ansluta data uppsättningar och moduler för att skapa en förutsägelse analys modell.

![Azure Machine Learning Studio-diagram (klassiskt): Skapa experiment, läsa data för många källor, skriva Poäng data, skriva modeller.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Hur skiljer sig Machine Learning Studio (klassisk) från Azure Machine Learning?

[Azure Machine Learning](../overview-what-is-azure-ml.md) tillhandahåller både SDK **: er och** Azure Machine Learning designer (för hands version) för att snabbt kunna prepa data, träna och distribuera maskin inlärnings modeller. Designern har en liknande dra-och-släpp-upplevelse till Studio (klassisk). Men till skillnad från den tillverkarspecifika beräknings plattformen i Studio (klassisk) använder designern dina egna beräknings resurser och är helt integrerade i Azure Machine Learning.

Här är en snabb jämförelse:

|| Machine Learning Studio (klassisk) | Azure Machine Learning |
|---| --- | --- |
| Dra och släpp gränssnitt | Ja | Ja – [Azure Machine Learning designer (för hands version)](../concept-designer.md) |
| Experiment | Skalbar (10 GB inlärnings data gräns) | Skala med beräknings mål |
| Moduler för dra och släpp-gränssnitt | Mycket | Inledande uppsättning populära [moduler](../algorithm-module-reference/module-reference.md)|
|Inlärnings mål| Patentskyddat beräknings mål, endast CPU-stöd| Stöder Azure Machine Learning Compute (GPU eller CPU) och virtuella notebook-datorer.<br/>([Andra beräkningar som stöds i SDK](../concept-compute-target.md#train))|
|Inferencing Compute-mål| Eget webb tjänst format, inte anpassningsbart |  Azure Kubernetes service och AML Compute <br/>([Andra beräkningar som stöds i SDK](../how-to-deploy-and-where.md)) |
| ML pipeline | Stöds inte | [Pipelines](../concept-ml-pipelines.md) som stöds |
| MLOps | Grundläggande modell hantering och distribution | Konfigurerbar distributions modell och versions hantering och spårning av pipeline |
| Modell format | Eget format, endast Studio (klassiskt) | Standardformat beroende på typ av utbildnings jobb |
|Automatisk modell inlärning och inställning för att justera dem | Nej | Ännu inte i designern <br/> ([Stöds på sidan med SDK och arbets ytans landning](../concept-automated-ml.md)) | 

Prova design verktyget med [Självstudier: förutsäga det mobila priset med designern](../tutorial-designer-automobile-price-train-score.md)

> [!NOTE]
> Modeller som skapats i Studio (klassisk) kan inte distribueras eller hanteras av Azure Machine Learning. Modeller som skapas och distribueras i designern kan dock hanteras via Azure Machine Learning-arbetsytan.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Ladda ned översikts diagrammet för Machine Learning Studio (klassisk)
Ladda ned **översikts diagrammet över Microsoft Azure Machine Learning Studio (klassisk)** och få en överblick på hög nivå av funktionerna i Machine Learning Studio (klassisk). Om du vill ha det nära tillhands kan du skriva ut det i tabloidformat (280 x 385 mm).

**Ladda ned diagrammet här: [Översikt över funktioner i Microsoft Azure Machine Learning Studio (klassisk)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) **
![Microsoft Azure Machine Learning Studio (klassisk)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


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

* [Konvertera till arff][convert-to-arff] – konverterar en .net-serialiserad data uppsättning till arff (Attribute-relation File Format).
* [Beräkna elementär statistik][elementary-statistics] – beräknar elementär statistik som medelvärde, standard avvikelsen osv.
* [Linjär regression][linear-regression] – skapar en brantaste linjär Regressions modell online.
* [Poäng modell][score-model] – poängen är en tränad klassificerings-eller Regressions modell.

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
