---
title: I prognostiserad kundomsättning med hjälp av Azure Machine Learning | Microsoft Docs
description: Så här utför omsättning analytics med hjälp av Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7c7b50098cfd1bcac534156dd905b37affab80bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648220"
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Prognostiserad kundomsättning med hjälp av Azure Machine Learning

Att behålla befintliga kunder är i genomsnitt fem gånger billigare än kostnaden för rekrytering nya. Därför marknadsföringschefer ofta själva försöker bedöma sannolikheten till kundomsättning och hitta rätt åtgärder för att minska omsättningsfrekvensen.

Syftet med den här lösningen är att demonstrera förutsägande omsättning analytics med hjälp av Azure Machine Learning Workbench. Den här lösningen innehåller en lättanvänd mall för att utveckla omsättning datapipelines för återförsäljare. Mallen kan användas med olika datauppsättningar och olika definitioner av omsättning. Syftet med det praktiska exemplet är att:

1. Förstå Azure Machine Learning Workbench förberedelse av Data-verktyg för att rensa och mata in relation kundinformation för analys av omsättning.

2. Genomför Transformations för funktionen för att hantera bort störande heterogena data.

3. Integrera bibliotek från tredje part (till exempel `scikit-learn` och `azureml`) att utveckla Bayesian och klassificerare som baseras på trädet för att förutsäga omsättning.

4. Distribuera.

## <a name="link-of-the-gallery-github-repository"></a>Länken till galleriet GitHub-lagringsplats
Följande är en länk till den offentliga GitHub-lagringsplatsen där all kod finns:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Översikt över användningsfall
Företag behöver en effektiv strategi för att hantera kundomsättningen. Kundomsättning inkluderar kunder slutar använda en tjänst, byter till en konkurrents tjänst, byter till en upplevelse på lägre nivå i tjänsten eller minskar sitt engagemang för tjänsten.

I det här användningsfallet tittar vi på data från franskt telekomföretag Orange att identifiera kunder som sannolikt omsättningen under den närmaste tiden för att förbättra tjänsten och skapa anpassade bredd kampanjer som hjälper att behålla kunder.

Telekommunikationsföretag inför en konkurrensutsatt marknad. Många leverantörer förlorar intäkter från kunder som väljer efterbetalning på grund av för att omsättning. Möjligheten att korrekt identifiera kundomsättning kan därför vara en stor Konkurrensmässig fördel.

Några av de faktorer som bidrar till kundomsättning för Kostnadsuppföljning av är:

* Upplevda frekventa avbrott
* Dålig service kundupplevelser i online/butiker
* Erbjudanden från rör (bättre familjeabonnemang, dataplan osv.).

I den här lösningen använder vi ett konkreta exempel för att skapa en förebyggande kund omsättning modellen för telekommunikationsföretag.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga)

* En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](../service/quickstart-installation.md) att installera programmet och skapa en arbetsyta

* Det är bäst för driftsättning, om du har Docker-motorn installerad och köras lokalt. Om inte, du kan använda klusteralternativet men tänk på att köra ett Azure Container Service (ACS) kan vara dyrt.

* Den här lösningen förutsätter att du använder Azure Machine Learning Workbench på Windows 10 med Docker-motorn installerad lokalt. Om du använder macOS är instruktionen i stort sett samma.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök efter projektmallar** sökrutan skriver du ”prognostiserad kundomsättning” och väljer mallen
5.  Klicka på **Skapa**

## <a name="data-description"></a>Databeskrivning

Datamängden som används i lösningen är SIDKDD 2009 konkurrensen. Den kallas `CATelcoCustomerChurnTrainingSample.csv` och finns i den [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) mapp. Datauppsättningen består av heterogena bort störande data (numeriska/kategoriska variabler) från Franska Kostnadsuppföljning av företag Orange och är anonyma.

Variablerna hitta demografiska kundinformation, anropsstatistik (till exempel Genomsnittlig samtalstid, fel hastighet, osv.), kontrakt klagomål statistik. Omsättning variabeln är binär (0 - inte omsättningen 1 – omsättning).

## <a name="scenario-structure"></a>Scenario-struktur

Mappstrukturen är ordnade enligt följande:

__data__: innehåller den datauppsättning som används i lösningen  

__docs__: innehåller alla praktiska labbövningar

Ordningen praktiska övningar för att utföra lösningen är följande:
1. Förberedelse av data: Huvudfilen som rör förberedelse av Data i datamappen är `CATelcoCustomerChurnTrainingSample.csv`
2. Modellering och utvärdering: huvudfilen som rör modellering och utvärdering i rotmappen är `CATelcoCustomerChurnModeling.py`
3. Modellering och utvärdering utan .dprep: huvudfilen för den här uppgiften i rotmappen är `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Driftsättning: Huvudsakliga filerna för distribution är modellen (`model.pkl`) och `churn_schema_gen.py`

| Beställa| Filnamn | Användarrelaterad filer |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | 'CATelcoCustomerChurnModelingWithoutDprep.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl'<br>'churn_schema_gen.py' |

Efter Labs på sekventiella sätt som beskrivs ovan.

## <a name="conclusion"></a>Sammanfattning
Detta händer på scenariot visas hur du utför kundomsättning med hjälp av Azure Machine Learning Workbench. Vi först utföra datarensning för att hantera bort störande och heterogena data, följt av funktionsframställning med hjälp av verktyg för förberedelse av Data. Vi sedan används maskininlärningsverktyg för öppen källkod för att skapa och utvärdera en modell för klassificering och sedan används lokala docker-behållare för att distribuera modellen som gör att det är dags för produktion.
