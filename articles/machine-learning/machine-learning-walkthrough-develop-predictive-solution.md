<properties
    pageTitle="Förutsägelseanalys för kreditrisk med Machine Learning | Microsoft Azure"
    description="En detaljerad genomgång av hur du skapar en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning Studio."
    keywords="kreditrisk, lösning för förutsägelseanalys, riskbedömning"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/10/2016"
    ms.author="garye"/>


# Genomgång: Utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning

Tänk dig att du behöver förutsäga kreditrisken för en person baserat på informationen personen fyller i på en kreditansökan.  

Kreditriskbedömning är förstås komplicerat, men vi kan förenkla frågeparametrarna en aning. Vi kan sedan använda det som ett exempel på hur du kan använda Microsoft Azure Machine Learning med Machine Learning Studio och Machine Learning-webbtjänsten för att skapa en sådan förutsägelseanalys.  

I den här detaljerade genomgången följer vi processen när en modell för förutsägelseanalys utvecklas i Machine Learning Studio och sedan distribueras som en Azure Machine Learning-webbtjänst. Vi börjar med offentligt tillgängliga kreditriskuppgifter, utvecklar och tränar en förutsägelsemodell baserat på dessa data och distribuerar sedan modellen som en webbtjänst som andra kan använda för kreditriskbedömning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Se [Översiktsdiagram över funktionerna i Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md) om du vill ladda ned och skriva ut ett diagram med en översikt över funktionerna i Machine Learning Studio.

Vi skapar en lösning för kreditriskbedömning enligt följande steg:  

1.  [Skapa en arbetsyta i Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Överför befintliga data](machine-learning-walkthrough-2-upload-data.md)
3.  [Skapa ett nytt experiment](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Träna och utvärdera modellerna](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Distribuera webbtjänsten](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Få åtkomst till webbtjänsten](machine-learning-walkthrough-6-access-web-service.md)

Den här genomgången är baserad på en förenklad version av exempelexperimentet [Binär klassificering: kreditriskanalys](http://go.microsoft.com/fwlink/?LinkID=525270) i [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/).



<!--HONumber=sep16_HO1-->


