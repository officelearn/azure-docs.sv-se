<properties
    pageTitle="Använda exempelexperiment för att skapa nya experiment | Microsoft Azure"
    description="Skapa nya Azure Machine Learning-experiment från exempelexperiment och experiment som delas av communityn."
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="chhavib;olgali"/>

# Använda exempelexperiment för att skapa nya experiment
När du vill skapa ett experiment kan du söka efter exempel i [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/). Där hittar du exempelexperiment från Machine Learning-teamet och experiment som delats av den bredare Machine Learning-communityn. Du kan även ställa frågor eller lämna kommentarer om experiment.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Bläddra i Cortana Intelligence Gallery
Du kan se vilka experiment som är tillgängliga i galleriet genom att öppna [galleriet](http://gallery.cortanaintelligence.com/) och klicka på **Experiments** högst uppe på sidan.
På den här sidan kan du se **nyligen tillagda** experiment, titta på **populära experiment** eller se de senaste **populära Microsoft-experimenten**.

Bläddra bland alla experiment:

1. Klicka på **Browse all** längst upp på sidan.
2. Under **Refine by** väljer du **Experiment** om du vill se alla experiment i galleriet.
3. Du kan begränsa listan ytterligare med filtren på vänster sida. Om du till exempel vill bläddra bland experiment som använder en PCA-baserad algoritm för avvikelseidentifiering väljer du **Experiment** under **Categories** och **PCA-Based Anomality Detection** under **Algorithms Used** (om du inte ser den algoritmen klickar du på **Show all** längst ned i listan).<br></br>
![](./media/machine-learning-sample-experiments/refine-the-view.png) 
4. Du kan använda sökrutan för att ytterligare begränsa urvalet. Om du till exempel vill hitta experiment från Microsoft som rör sifferigenkänning som använder en algoritm för stödvektormaskin med två klasser anger du ”digit recognition” i sökrutan och väljer **Experiment**, **Microsoft content only** och **Two-Class Support Vector Machine**:
![](./media/machine-learning-sample-experiments/search-for-experiments.png) 
5. Klicka på ett experiment om du vill veta mer om det. Om du vill köra och/eller ändra experimentet klickar du på **Open in Studio** på experimentsidan.

> [AZURE.NOTE] För att kunna öppna ett experiment i Machine Learning Studio måste du logga in med autentiseringsuppgifterna för ditt Microsoft-konto. Om du inte har någon arbetsyta i Machine Learning än skapas en kostnadsfri utvärderingsversion. [Mer information om vad som ingår i den kostnadsfria utvärderingsversionen av Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/)

![](./media/machine-learning-sample-experiments/example-experiment.png) 


## Använda en mall i Machine Learning Studio

Du kan även skapa ett nytt försök i Machine Learning Studio med ett exempel från galleriet som mall.

1. Skapa ett nytt experiment genom att logga in i [Studio](https://studio.azureml.net) med dina Microsoft-autentiseringsuppgifter och klicka på **Nytt***.
2. Bläddra igenom exemplen och klicka på ett.

Ett nytt experiment skapas i arbetsytan med exempelexperimentet som mall. 

## Nästa steg
- [Förbereda dina data](machine-learning-data-science-import-data.md)
- [Testa att använda R i experimentet](machine-learning-r-quickstart.md)
- [Se R-exempelexperiment](machine-learning-r-csharp-web-service-examples.md)
- [Skapa ett webbtjänst-API](machine-learning-publish-a-machine-learning-web-service.md)
- [Bläddra bland appar som är färdiga att användas](https://datamarket.azure.com/browse?query=machine+learning)



<!--HONumber=Jun16_HO2-->


