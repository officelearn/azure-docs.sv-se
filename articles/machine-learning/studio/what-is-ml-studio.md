---
title: Vad är
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio är ett dra-och-släpp-verktyg för att snabbt skapa modeller från bibliotek med algoritmer och moduler som är klara att använda.
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: 44f2c24f1fe53b6fadabf23f03a6feda75c87883
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738291"
---
# <a name="what-is-azure-machine-learning-studio"></a>Vad är Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio är ett drag-och-släpp-verktyg där flera användare kan samarbeta för att bygga, testa och distribuera prediktiva analyslösningar utifrån dina data. Tjänsten Machine Learning Studio publicerar modeller som webbtjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel.

Machine Learning Studio är platsen där datavetenskap, prediktiva analyser, molnresurser och dina data sammanstrålar.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Den interaktiva arbetsytan i tjänsten Machine Learning Studio
För att skapa en prediktiv analysmodell använder du normalt data från eller flera källor, omvandlar och analyserar dessa data genom att utföra en serie datamodifieringar och statistiska funktioner och sedan genererar du en uppsättning resultat. Att utveckla en modell som den här är en process som är baserad på upprepningar (iteration). När du ändrar olika funktionerna och deras parametrar kommer resultaten att konvergera tills du är nöjd och säker på att du har en tränad, effektiv modell.

**Azure Machine Learning Studio** ger dig en interaktiv, visuell arbetsyta där du kan bygga, testa och utföra iterationer på en prediktiv analysmodell. Du drar och släpper ***datauppsättningar*** och ***analysmoduler*** till en interaktiv ***arbetsyta***, kopplar samman dem för att skapa ett experiment och sedan kör du dessa i Machine Learning Studio. Om du vill köra en iteration med modelldesignen, redigerar du experimentet, sparar en kopia om du så önskar och sedan kör du försöket igen. När du är klar kan du konvertera ditt ***träningsexperiment*** till ett ***prediktivt experiment***, och sedan ***publicera*** det som en webbtjänst så att din modell kan användas av andra.

Ingen programmering krävs. Det enda du behöver göra är att koppla samman datauppsättningar och moduler visuellt för att skapa en prediktiv analysmodell.

![Azure Machine Learning-studiodiagram: Skapa experiment, läs in data från flera källor, skriv in bedömda data, skriv modeller.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Ladda ned översiktsdiagrammet över Machine Learning Studio
Ladda ned diagrammet **Översikt över funktioner i Microsoft Azure Machine Learning Studio**, så får du en övergripande bild av funktionerna i Machine Learning Studio. Om du vill ha det nära tillhands kan du skriva ut det i tabloidformat (280 x 385 mm).

**Ladda ned diagrammet här: [Översikt av funktioner i Microsoft Azure Machine Learning Studio](http://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Översikt av funktioner i Microsoft Azure Machine Learning Studio](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Kom igång med Machine Learning Studio
När du startar [Machine Learning Studio](https://studio.azureml.net) kommer du att se **startsidan**. Härifrån kan du titta på dokumentation, videor, webbseminarier och hitta andra användbara resurser.

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
Klicka på fliken **Galleri**, så kommer du till **[Azure AI-galleriet](http://gallery.azure.ai/)**. Galleriet är en plats där grupper med dataanalytiker och utvecklare kan dela lösningar som skapats med hjälp av komponenter i Cortana Intelligence Suite.

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

Medan du skapar ett experiment kan välja bland alternativ från listan över tillgängliga datauppsättningar på arbetsytans vänstra sida.

Om du vill se en lista över provdatauppsättningar som ingår i Machine Learning Studio, kan du gå till [Använda provdatauppsättningar i Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Moduler
En modul är en algoritm som du kan tillämpa på dina data. Machine Learning Studio har ett antal moduler, med allt från dataåtkomstfunktioner och träning till bedömning och processer för verifiering. Här följer några exempel på moduler som ingår:

* [Konvertera till ARFF][convert-to-arff] – Konverterar en .NET-serialiserad datauppsättning till formatet ARFF (Attribute-Relation File Format).
* [Beräkna elementär statistik][elementary-statistics] – Beräknar elementär statistik, som medelvärde, standardavvikelse osv.
* [Linjär regression][linear-regression] – Skapar en linjär regressionsmodell baserad på brantaste lutningsmetoden online.
* [Bedömningsmodell][score-model] – Bedömer en tränad klassificerings- eller regressionsmodell.

Medan du skapar ett experiment kan välja bland alternativ från listan över tillgängliga moduler på arbetsytans vänstra sida.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbetsytan modulens visas modulens parametrar i fönstret **Egenskaper** på arbetsytans högra sida. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

Om du vill få hjälp med att navigera genom det stora biblioteket med tillgängliga maskininlärningsalgoritmer kan du gå till [Så här väljer du algoritmer för Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Distribuera en prediktiv analysmodell som webbtjänst
När din prediktiva analysmodell är färdig kan du distribuera den som en webbtjänst direkt från Machine Learning Studio. Mer information om den här processen finns i [Distribuera en webbtjänst via Azure Machine Learning](publish-a-machine-learning-web-service.md).


## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Hur skiljer sig tjänsten Machine Learning Studio från Azure Machine Learning Service?

[Azure Machine Learning Service](../service/overview-what-is-azure-ml.md) tillhandahåller SDK:er och tjänster som du kan använda för att snabbt förbereda data samt träna och distribuera maskininlärningsmodeller. Förbättra produktiviteten och minska kostnaderna med databearbetning med automatisk skalning och pipelines. Använd dessa funktioner med Python-ramverk med öppen källkod, till exempel PyTorch, TensorFlow och scikit-learn. 

Använd Machine Learning Studio om du snabbt och enkelt vill experimentera med maskininlärningsmodeller och om de inbyggda maskininlärningsalgoritmerna räcker för dina lösningar.

Använd Machine Learning-tjänsten om du arbetar i en Python-miljö och vill ha mer kontroll över maskininlärningsalgoritmerna eller om du vill använda maskininlärningsbibliotek med öppen källkod.

> [!NOTE]
> Modeller som skapas i Azure Machine Learning Studio kan inte distribueras eller hanteras av Azure Machine Learning-tjänsten.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Nästa steg
Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att gå en [snabbstart med stegvisa anvisningar](create-experiment.md) och genom att [bygga vidare på prover](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
