---
title: Vad är
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio är ett dra-och-släpp-verktyg för att snabbt skapa modeller från bibliotek med algoritmer och moduler som är klara att använda.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 4ec9cff652bf1badf526d490547ad78de31ac5da
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678002"
---
# <a name="what-is-azure-machine-learning-studio"></a>Vad är Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio är ett drag-och-släpp-verktyg där flera användare kan samarbeta för att bygga, testa och distribuera prediktiva analyslösningar utifrån dina data. Tjänsten Machine Learning Studio publicerar modeller som webbtjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel.

Machine Learning Studio är platsen där datavetenskap, prediktiva analyser, molnresurser och dina data sammanstrålar.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Den interaktiva arbetsytan i tjänsten Machine Learning Studio
För att utveckla en förutsägelse analys modell använder du vanligt vis data från en eller flera källor, transformerar och analyserar dessa data via olika data manipulations-och statistik funktioner och genererar en uppsättning resultat. Att utveckla en modell som den här är en process som är baserad på upprepningar (iteration). När du ändrar olika funktionerna och deras parametrar kommer resultaten att konvergera tills du är nöjd och säker på att du har en tränad, effektiv modell.

**Azure Machine Learning Studio** ger dig en interaktiv, visuell arbetsyta där du kan bygga, testa och utföra iterationer på en prediktiv analysmodell. Du drar och släpper ***datauppsättningar*** och ***analysmoduler*** till en interaktiv ***arbetsyta***, kopplar samman dem för att skapa ett experiment och sedan kör du dessa i Machine Learning Studio. Om du vill köra en iteration med modelldesignen, redigerar du experimentet, sparar en kopia om du så önskar och sedan kör du försöket igen. När du är klar kan du konvertera ditt ***träningsexperiment*** till ett ***prediktivt experiment***, och sedan ***publicera*** det som en webbtjänst så att din modell kan användas av andra.

Ingen programmering krävs. Det enda du behöver göra är att koppla samman datauppsättningar och moduler visuellt för att skapa en prediktiv analysmodell.

![Azure Machine Learning-studiodiagram: Skapa experiment, läs in data från flera källor, skriv in bedömda data, skriv modeller.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Ladda ned översiktsdiagrammet över Machine Learning Studio
Ladda ned diagrammet **Översikt över funktioner i Microsoft Azure Machine Learning Studio**, så får du en övergripande bild av funktionerna i Machine Learning Studio. Om du vill ha det nära tillhands kan du skriva ut det i tabloidformat (280 x 385 mm).

**Ladda ned diagrammet här: [Översikt av funktioner i Microsoft Azure Machine Learning Studio](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Översikt av funktioner i Microsoft Azure Machine Learning Studio](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Kom igång med Machine Learning Studio
Första gången du anger [Machine Learning Studio](https://studio.azureml.net)visas **Start** sidan. Härifrån kan du Visa dokumentation, videor och webb seminarier och hitta andra värdefulla resurser.

Klicka på menyn uppe till vänster ![Meny](./media/what-is-ml-studio/menu.png) och du ser flera alternativ.
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Det finns två alternativ här: **Start**, vilket är den sida där du startade, och **Studio**.

Klicka på **Studio**, så kommer du till **Azure Machine Learning Studio**. Först uppmanas du att logga in med ditt Microsoft-konto eller ditt arbets- eller skolkonto. När du har loggat in visas följande flikar till vänster:

* **PROJEKT** – Samlingar med experiment, datauppsättningar, anteckningsböcker och andra resurser som representerar ett enskilt projekt
* **EXPERIMENT** – Experiment som du har skapat och kört eller sparat som utkast
* **WEBBTJÄNSTER** – Webbtjänster som du har distribuerat från dina experiment
* **NOTEBOOKS** – Jupyter-anteckningsböcker som du har skapat
* **DATAUPPSÄTTNINGAR** – Datauppsättningar som du har överfört till Studio
* **TRÄNADE MODELLER** – Modeller som du har tränat i experiment och sparat i Studio
* **INSTÄLLNINGAR** – En uppsättning av inställningar som du kan använda för att konfigurera ditt konto och resurserna.

### <a name="gallery"></a>Galleri
Klicka på fliken **Galleri**, så kommer du till **[Azure AI-galleriet](https://gallery.azure.ai/)** . Galleriet är en plats där grupper med dataanalytiker och utvecklare kan dela lösningar som skapats med hjälp av komponenter i Cortana Intelligence Suite.

Mer information om galleriet finns i informationen om att [dela och upptäcka lösningar i Azure AI-galleriet](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Komponenter i ett experiment
Ett experiment består av datauppsättningar som förser de analytiska modulerna med data. Du kopplar sedan samman dessa moduler för att skapa en prediktiv analysmodell. Mer konkret uttryckt så har ett giltigt experiment följande egenskaper:

* Experimentet har minst en datauppsättning och en modul
* Datauppsättningar kan bara anslutas till moduler
* Moduler kan anslutas antingen till datauppsättningar eller till andra moduler
* Alla indataportar för moduler måste ha någon typ av koppling till dataflödet
* Alla nödvändiga parametrar måste anges för varje modul

Du kan skapa ett experiment från grunden eller så kan du använda ett befintligt exempelexperiment som mall. Mer information finns i avsnittet [Kopiera exempelexperiment för att skapa nya maskininlärningsexperiment](sample-experiments.md).

Om du vill se ett exempel på hur du kan skapa ett enkelt experiment kan du gå till [Skapa ett enkelt experiment i Azure Machine Learning Studio](create-experiment.md).

Om du vill få en fullständig genomgång av att skapa en prediktiv analyslösning kan du gå till [Utveckla en prediktiv lösning med Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datauppsättningar
En datauppsättning är data som har överförts till Machine Learning Studio så att de kan användas i modelleringsprocessen. Ett antal provdatauppsättningar ingår i Machine Learning Studio för att du ska kunna experimentera med dem. Och du kan föra över fler datauppsättningar efter behov. Här följer några exempel på datauppsättningar som ingår:

* **MPG-data för olika bilar** – MPG-värden (Miles Per Gallon, USA:s motsvarighet till liter per 100 km) för bilar beräknat utifrån antalet cylindrar, hästkrafter o.s.v.
* **Bröstcancerdata** – Data för bröstcancerdiagnoser.
* **Data om skogsbränder** – Storleken på skogsbränder i nordöstra Portugal.

När du skapar ett experiment kan du välja från listan över data uppsättningar som är tillgängliga till vänster på arbets ytan.

Om du vill se en lista över provdatauppsättningar som ingår i Machine Learning Studio, kan du gå till [Använda provdatauppsättningar i Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Moduler
En modul är en algoritm som du kan tillämpa på dina data. Machine Learning Studio har ett antal moduler, med allt från dataåtkomstfunktioner och träning till bedömning och processer för verifiering. Här följer några exempel på moduler som ingår:

* [Konvertera till arff][convert-to-arff] – konverterar en .net-serialiserad data uppsättning till arff (Attribute-relation File Format).
* [Beräkna elementär statistik][elementary-statistics] – beräknar elementär statistik som medelvärde, standard avvikelsen osv.
* [Linjär regression][linear-regression] – skapar en brantaste linjär Regressions modell online.
* [Poäng modell][score-model] – poängen är en tränad klassificerings-eller Regressions modell.

Medan du skapar ett experiment kan välja bland alternativ från listan över tillgängliga moduler på arbetsytans vänstra sida.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbetsytan modulens visas modulens parametrar i fönstret **Egenskaper** på arbetsytans högra sida. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

Om du vill få hjälp med att navigera genom det stora biblioteket med tillgängliga maskininlärningsalgoritmer kan du gå till [Så här väljer du algoritmer för Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Distribuera en prediktiv analysmodell som webbtjänst
När din prediktiva analysmodell är färdig kan du distribuera den som en webbtjänst direkt från Machine Learning Studio. Mer information om den här processen finns i [Distribuera en webbtjänst via Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Hur skiljer sig tjänsten Machine Learning Studio från Azure Machine Learning Service?

[Azure Machine Learning-tjänsten](../service/overview-what-is-azure-ml.md) tillhandahåller både SDK **-och-** ett visuellt gränssnitt (för hands version) för att snabbt kunna utveckla data, träna och distribuera maskin inlärnings modeller. Det här visuella gränssnittet (för hands version) innehåller en liknande dra-och-släpp-upplevelse till Studio. Till skillnad från den patentskyddade beräknings plattformen i Studio använder Visual-gränssnittet dock dina egna beräknings resurser och är helt integrerade i Azure Machine Learning-tjänsten.

Här är en snabb jämförelse.

|| Machine Learning Studio | Azure Machine Learning tjänst:<br/>Visuellt gränssnitt|
|---| --- | --- |
|| Allmänt tillgänglig (GA) | Förhandsversion|
|Moduler för gränssnitt| Mycket | Inledande uppsättning populära moduler|
|Inlärnings mål| Patentskyddat beräknings mål, endast CPU-stöd| Stöder Azure Machine Learning Compute, GPU eller CPU.<br/>(Andra beräkningar som stöds i SDK)|
|Beräknings mål för distribution| Eget webb tjänst format, inte anpassningsbart | Företags säkerhets alternativ & Azure Kubernetes-tjänsten. <br/>([Andra beräkningar](../service/how-to-deploy-and-where.md) som stöds i SDK) |
|Automatisk modell inlärning och inställning för att justera dem | Nej | Ännu inte i Visual Interface. <br/> (Stöds i SDK och Azure Portal.) | 

Prova Visual Interface (för hands version) med [Självstudier: Förutsäg bil pris med Visual Interface](../service/ui-tutorial-automobile-price-train-score.md)

> [!NOTE]
> Modeller som skapats i Studio kan inte distribueras eller hanteras av Azure Machine Learning tjänsten. Modeller som skapas och distribueras i tjänstens visuella gränssnitt kan dock hanteras via Azure Machine Learning-tjänstens arbets yta.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Nästa steg
Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att gå en [snabbstart med stegvisa anvisningar](create-experiment.md) och genom att [bygga vidare på prover](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
