---
title: Vad är
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio är ett dra-och-släpp-verktyg för att snabbt skapa modeller från bibliotek med algoritmer och moduler som är klara att använda.
keywords: azure machine learning,azure ml, ml studio
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
ms.openlocfilehash: 77c69e7e655770ab912e6ee25183e760f972504d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243696"
---
# <a name="what-is-azure-machine-learning-studio"></a>Vad är Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio är ett drag-och-släpp-verktyg där flera användare kan samarbeta för att bygga, testa och distribuera prediktiva analyslösningar utifrån dina data. Tjänsten Machine Learning Studio publicerar modeller som webbtjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel.

Machine Learning Studio är platsen där datavetenskap, prediktiva analyser, molnresurser och dina data sammanstrålar.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Den interaktiva arbetsytan i tjänsten Machine Learning Studio
För att skapa en prediktiv analysmodell använder du normalt data från eller flera källor, omvandlar och analyserar dessa data genom att utföra en serie datamodifieringar och statistiska funktioner och sedan genererar du en uppsättning resultat. Att utveckla en modell som den här är en process som är baserad på upprepningar (iteration). När du ändrar olika funktionerna och deras parametrar kommer resultaten att konvergera tills du är nöjd och säker på att du har en tränad, effektiv modell.

**Azure Machine Learning Studio** ger dig en interaktiv, visuell arbetsyta där du kan bygga, testa och utföra iterationer på en prediktiv analysmodell. Du drar och släpper ***datauppsättningar*** och ***analysmoduler*** till en interaktiv ***arbetsyta***, kopplar samman dem för att skapa ett experiment och sedan kör du dessa i Machine Learning Studio. Om du vill köra en iteration med modelldesignen, redigerar du experimentet, sparar en kopia om du så önskar och sedan kör du försöket igen. När du är klar kan du konvertera ditt ***träningsexperiment*** till ett ***prediktivt experiment***, och sedan ***publicera*** det som en webbtjänst så att din modell kan användas av andra.

Ingen programmering krävs. Det enda du behöver göra är att koppla samman datauppsättningar och moduler visuellt för att skapa en prediktiv analysmodell.

![Azure Machine Learning-studiodiagram: Skapa experiment, läs in data från flera källor, skriv in bedömda data, skriv modeller.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Kom igång med Machine Learning Studio
När du startar [Machine Learning Studio](https://studio.azureml.net) kommer du att se **startsidan**. Härifrån kan du titta på dokumentation, videor, webbseminarier och hitta andra användbara resurser.

Klicka på menyn uppe till vänster ![Meny](./media/what-is-ml-studio/menu.png) och du ser flera alternativ.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Om du klickar på **Cortana Intelligence** öppnas startsidan för [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite). Cortana Intelligence är en fullständigt hanterad programsvit för stordata och avancerad analys som hjälper dig att omvandla data till intelligenta åtgärder. På programsvitens startsida finns fullständig dokumentation, inklusive kundberättelser.

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

Om du vill få en fullständig genomgång av att skapa en prediktiv analyslösning kan du gå till [Utveckla en prediktiv lösning med Azure Machine Learning](tutorial-part1-credit-risk.md).

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

Om du vill få hjälp med att navigera genom det stora biblioteket med tillgängliga maskininlärningsalgoritmer kan du gå till [Så här väljer du algoritmer för Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Distribuera en prediktiv analysmodell som webbtjänst
När din prediktiva analysmodell är färdig kan du distribuera den som en webbtjänst direkt från Machine Learning Studio. Mer information om den här processen finns i [Distribuera en webbtjänst via Azure Machine Learning](publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg



## <a name="key-machine-learning-terms-and-concepts"></a>Viktiga termer och begrepp inom maskininlärning
Maskininlärningstermerna kan vara förvirrande. Här följer definitioner av de viktigaste termerna. Skicka gärna kommentarer om det finns andra termer som du vill få förklarade.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Datagranskning, beskrivande analys och förutsägelseanalys

**Datagranskning** är en process där du samlar in information om en stor och ofta ostrukturerad datauppsättning för att identifiera egenskaper för fokuserad analys.

**Datautvinning** syftar på automatiserad datagranskning.

**Beskrivande analys** är en process där du analyserar en datauppsättning för att sammanfatta vad som hänt. Merparten av alla företagsanalyser – försäljningsrapporter, mätvärden från webben och analys av sociala nätverk – är beskrivande.

**Förutsägelseanalyser** är en process där du skapar modeller från historiska eller aktuella data för att förutsäga framtida resultat.

### <a name="supervised-and-unsupervised-learning"></a>Övervakad och oövervakad inlärning
 Algoritmer för **övervakad inlärning** tränas med märkta data, dvs. data bestående av exempel på önskade svar. En modell som identifierar falska kreditkort skulle exempelvis tränas från en datauppsättning med namngivna datapunkter för kända falska och giltiga debiteringar. Merparten av all maskininlärning är övervakad.

 **Oövervakad inlärning** används med omärkta data och målet är att identifiera relationer i dessa data. Du kanske exempelvis vill identifiera grupperingar av kundområden med liknande köpvanor.

### <a name="model-training-and-evaluation"></a>Träning och utvärdering av modeller
En maskininlärningsmodell är en abstraktion av den fråga som du försöker besvara eller det resultat som du vill förutsäga. Modeller tränas och utvärderas från befintliga data.

#### <a name="training-data"></a>Träningsdata
När du tränar en modell från data använder du en känd datauppsättning och justerar modellen baserat på dataegenskaperna för att få det mest korrekta svaret. I Azure Machine Learning skapas en modell från en algoritmmodul som bearbetar träningsdata och funktionella moduler, t.ex. en poängsättningsmodul.

Om du tränar en bedrägeriidentifieringsmodell med övervakad inlärning använder du en uppsättning transaktioner som är märkta som bedrägliga eller giltiga. Du delar datauppsättningen slumpmässigt och använder en del för att träna modellen och en del för att testa eller utvärdera modellen.

#### <a name="evaluation-data"></a>Utvärdering av data
När du har en tränad modell utvärderar du modellen med hjälp av återstående testdata. Du använder data vars resultat du redan känner till, så att du kan se om din modell gör korrekta förutsägelser.

## <a name="other-common-machine-learning-terms"></a>Andra vanliga maskininlärningstermer
* **algoritmen**: En fristående uppsättning regler som används för att lösa problem genom databehandling, beräkning eller automatiserade resonemang.
* **avvikelseidentifiering**: En modell som flaggar ovanliga händelser eller värden och hjälper dig att identifiera problem. Exempelvis letar en identifiering av falska kreditkort efter ovanliga köp.
* **kategoriska data**: Data som är ordnade efter kategorier och som kan delas in i grupper. En uppsättning kategoriska data för bilar kan exempelvis definiera år, märke, modell och pris.
* **klassificering**: En modell för att organisera datapunkter i kategorier baserat på en datauppsättning vars kategorigrupperingar redan är kända.
* **egenskapsval**: En process för att extrahera eller välja egenskaper relaterade till en datauppsättning för att förbättra datauppsättningen och förbättra resultatet. Information om flygpriser kan till exempel förbättras med dagar i veckan och helgdagar. Mer information finns i avsnittet om [egenskapsval i Azure Machine Learning](../team-data-science-process/create-features.md).
* **modul**: Ett funktionellt element i en Machine Learning Studio-modell, till exempel modulen Enter Data (Ange data), som kan användas för att mata in och redigera små datauppsättningar. En algoritm är också en typ av modul i Machine Learning Studio.
* **modell**: En övervakad inlärningsmodell är produkten av ett maskininlärningsexperiment bestående av träningsdata, en algoritmmodul och funktionella moduler som modulen Score Model (Poängsätta modell).
* **numeriska data**: Data som representerar mått (kontinuerliga data) eller antal (diskreta data). Kallas även *kvantitativa data*.
* **partition**: En metod för att dela in data i prover. Mer information finns i [Partitionera och ta prover](https://msdn.microsoft.com/library/azure/dn905960.aspx).
* **förutsägelse**: En förutsägelse är en prognos av värden från en maskininlärningsmodell. Du kan också se termen ”förutsägelsepoäng”. Detta är emellertid inte en modells slutgiltiga data. Poängsättningen följs av en utvärdering av modellen.
* **regression**: En mall för att förutsäga ett värde baserat på oberoende variabler, till exempel att förutsäga priset på en bil baserat på dess år och märke.
* **poäng**: Ett prognostiserat värde som genereras från en tränad klassificerings- eller regressionsmodell med hjälp av modulen [Poängsätta modell](https://msdn.microsoft.com/library/azure/dn905995.aspx) i Machine Learning Studio. Klassificeringsmodeller bedömer också sannolikheten för förutsägelsevärdet. När du har genererat poäng från en modell kan du utvärdera modellens precision med hjälp av modellen [Utvärdera modell](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **prov**: En del av en datauppsättning som är avsedd att representera helheten. Prover kan väljas slumpmässigt eller baserat på specifika funktioner i datauppsättningen.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att gå en [snabbstart med stegvisa anvisningar](create-experiment.md) och genom att [bygga vidare på prover](sample-experiments.md).


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
