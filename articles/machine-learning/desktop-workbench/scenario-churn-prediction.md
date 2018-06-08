---
title: Kunden Omsättningsuppdateringar förutsägelse med hjälp av Azure Machine Learning | Microsoft Docs
description: Så här utför omsättning analytics med hjälp av Azure ML-arbetsstationen.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: a4e3441e4b7512d60be8ce5433822a95732cd802
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832398"
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Kunden omsättning förutsägelse med hjälp av Azure Machine Learning

I genomsnitt är befintliga kunder fem gånger billigare än kostnaden för rekrytering nya. Därför hitta marknadsföring chefer ofta själva försöker beräkna sannolikheten för kunden omsättning och hitta nödvändiga åtgärder för att minimera omsättningen.

Syftet med den här lösningen är att demonstrera förutsägande omsättning analytics med hjälp av Azure Machine Learning-arbetsstationen. Den här lösningen ger ett enkelt att använda mallen för att utveckla omsättning förutsägande data pipelines för återförsäljare. Mallen kan användas med olika DataSet och olika definitioner av omsättning. Syftet med det praktiska exemplet är att:

1. Förstå Azure Machine Learning-arbetsstationens förberedelse av Data-verktyg för att rensa och mata in customer relationship data för analys av omsättning.

2. Utför omvandling av funktionen för att hantera mycket brus heterogena data.

3. Integrera tredjeparts-bibliotek (t.ex `scikit-learn` och `azureml`) att utveckla Bayesian och trädet-baserade klassificerare för att förutsäga omsättning.

4. Distribuera.

## <a name="link-of-the-gallery-github-repository"></a>Länken till GitHub-lagringsplatsen galleri
Följande är en länk till den offentliga GitHub-lagringsplatsen där all kod finns:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Använd case-översikt
Företag måste en effektiv strategi för hantering av kunden omsättning. Kunden omsättning innehåller kunder stoppar användning av en tjänst, växla till en tjänst som konkurrenter, växla till en lägre nivå upplevelse i tjänsten eller minska interaktion med tjänsten.

I det här fallet används titta vi på data från Franska telecom företag Orange att identifiera kunder som sannolikt att trycka på kort sikt för att förbättra tjänsten och skapa anpassade bredd kampanjer som Behåll kunderna.

Telecom företag står inför en konkurrerande marknad. Många leverantörer gå förlorade intäkter från postpaid kunder på grund av för att omsättningsuppdateringar. Möjlighet att identifiera kunden omsättning kan därför vara en stor konkurrensfördel.

Några av de faktorer som bidrar till telecom kunden omsättning är:

* Upplevd ofta driftstörningar
* Dålig service kundupplevelser i online/butiker
* Erbjudanden från andra konkurrerande leverantörer (bättre family plan, dataabonnemang osv.).

I den här lösningen använder vi en konkret exempel för att skapa en prediktiv kund omsättning modellen för telecom företag.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns)

* En installerad kopia av [Azure Machine Learning arbetsstationen](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](../service/quickstart-installation.md) att installera programmet och skapa en arbetsyta

* Det är bäst för operationalization, om du har installerat och körs lokalt Docker-motorn. Om inte, du kan använda alternativet klustret men tänk på att köra Azure Container Service (ACS) kan vara dyra.

* Denna lösning förutsätter att du använder Azure Machine Learning-arbetsstationen på Windows 10 med Docker-motorn har installerats lokalt. Om du använder macOS är instruktionen i stort sett densamma.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”kund Omsättningsuppdateringar förutsägelse” och välj mallen
5.  Klicka på **Skapa**

## <a name="data-description"></a>Beskrivning av data

Datamängden som används i lösningen är från SIDKDD 2009 konkurrens. Den kallas `CATelcoCustomerChurnTrainingSample.csv` och finns i den [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) mapp. Dataset består av heterogena mycket data (numeriska/kategoriska variabler) från Franska Telecom företag Orange och är anonym.

Variablerna avbilda kunden demografisk information anropsstatistik (till exempel genomsnitt, anropet felintervall osv.), kontrakt klagomål statistik. Omsättning variabeln är binär (0 - inte omsättningsuppdateringar 1 – omsättningsuppdateringar).

## <a name="scenario-structure"></a>Scenario-struktur

Mappstrukturen är ordnade enligt följande:

__data__: innehåller den datamängd som används i lösningen  

__dokumenten__: innehåller de praktiska övningarna

Ordningen praktiska övningar för att utföra lösningen är följande:
1. Förberedelse av data: Huvudfil rör förberedelse av Data i datamappen är `CATelcoCustomerChurnTrainingSample.csv`
2. Modellering och utvärdering: huvudfil som rör utformning och utvärdering i rotmappen är `CATelcoCustomerChurnModeling.py`
3. Modellering och utvärdering utan .dprep: huvudfil för den här uppgiften i rotmappen är `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operationalization: Huvudsakliga filerna för deloyment är modellen (`model.pkl`) och `churn_schema_gen.py`

| Ordning| Filnamn | Realted filer |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | 'CATelcoCustomerChurnModelingWithoutDprep.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl'<br>'churn_schema_gen.py' |

Efter Labs på sekventiella sätt som beskrivs ovan.

## <a name="conclusion"></a>Sammanfattning
Detta händer på scenariot visas hur du utför omsättning förutsägelse med hjälp av Azure Machine Learning-arbetsstationen. Vi kan först utföras Datarensning för att hantera mycket brus och heterogena data, följt av funktionen tekniker med hjälp av verktyg för förberedelse av Data. Vi sedan används maskininlärning för öppen källkod verktyg för att skapa och utvärdera en modell för klassificering och sedan används lokala dockerbehållare för att distribuera modellen gör den redo för produktion.
