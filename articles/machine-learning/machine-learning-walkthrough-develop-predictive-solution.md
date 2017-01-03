---
title: "Förutsägelseanalys för kreditrisk med Machine Learning | Microsoft Docs"
description: "En detaljerad genomgång av hur du skapar en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning Studio."
keywords: "kreditrisk, lösning för förutsägelseanalys, riskbedömning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Genomgång: Utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning

I den här genomgången ska vi ta en mer ingående titt på arbetet med att utveckla en lösning i Machine Learning Studio. Vi utvecklar en modell för förutsägelseanalys i Machine Learning Studio och distribuerar den sedan som en Azure Machine Learning-webbtjänst, där modellen kan göra förutsägelser med nya data. 

> [!TIP]
> Den här genomgången förutsätter att du har använt Machine Learning Studio minst en gång innan och att du har viss förståelse för Machine Learning-begrepp, även om det inte antas att du är expert på ämnet.
> 
>Om du aldrig har använt **Azure Machine Learning Studio** innan kanske du vill börja med självstudierna, [Skapa ditt första dataexperiment i Azure Machine Learning Studio](machine-learning-create-experiment.md). Den här självstudiekursen guidar dig genom Machine Learning Studio första gången och visar dig grunderna för hur du drar och släpper moduler till ditt experiment, kopplar ihop dem, kör experimentet och tittar på resultatet.
>
>Om du inte har använt Machine Learning tidigare kan det vara bra att börja med videoserien [Datavetenskap för nybörjare](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Med den här videoserien får du en bra introduktion till Machine Learning med ord och begrepp som är lätta att förstå.
> 

## <a name="the-problem"></a>Problemet

Tänk dig att du behöver förutsäga kreditrisken för en person baserat på informationen personen fyller i på en kreditansökan.  

Kreditriskbedömning är förstås komplicerat, men vi kommer att förenkla frågeparametrarna en aning. Sedan kommer vi att använda det som ett exempel på hur du kan använda Microsoft Azure Machine Learning med Machine Learning Studio och Machine Learning-webbtjänsten för att skapa en sådan förutsägelseanalys.  

## <a name="the-solution"></a>Lösningen

I den här detaljerade beskrivningen börjar vi med offentligt tillgängliga kreditriskuppgifter, utvecklar och tränar en förutsägelsemodell baserat på dessa data och distribuerar sedan modellen som en webbtjänst som andra kan använda för kreditriskbedömning.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Vi skapar en lösning för kreditriskbedömning enligt följande steg:  

1. [Skapa en Machine Learning-arbetsyta](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](machine-learning-walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](machine-learning-walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](machine-learning-walkthrough-6-access-web-service.md)

Den här genomgången är baserad på en förenklad version av exempelexperimentet [Binär klassificering: kreditriskanalys](http://go.microsoft.com/fwlink/?LinkID=525270) i [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/).


> [!TIP]
> Se [Översiktsdiagram över funktionerna i Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md) om du vill ladda ned och skriva ut ett diagram med en översikt över funktionerna i Machine Learning Studio.
> 
> 



<!--HONumber=Jan17_HO1-->


