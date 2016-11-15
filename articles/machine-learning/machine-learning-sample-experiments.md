---
title: Kopiera Machine Learning-provexperiment | Microsoft Docs
description: "Lär dig hur du använder provexperiment med Machine Learning för att skapa nya experiment med Cortana Intelligence-galleriet och Microsoft Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: cgronlun;chhavib;olgali
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: df9009646627226acc8209486f990688efe95622


---
# <a name="copy-sample-experiments-to-create-new-machine-learning-experiments"></a>Kopiera provexperiment för att skapa nya Machine Learning-experiment
Lär dig hur du startar med provexperiment från [Cortana Intelligence-galleriet](http://gallery.cortanaintelligence.com/) istället för att skapa Machine Learning-experiment från början. Du kan använda exemplen för att skapa din egen Machine Learning-lösning.

I galleriet finns provexperiment från Microsoft Azure Machine Learning-teamet samt exempel som delats av Machine Learning-communityn. Du kan även ställa frågor eller lämna kommentarer om experiment.

Om du vill se hur du använder galleriet kan du titta på den 3 minuter långa videon [Copy other people's work to do data science](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Kopiera andras arbete för datavetenskap) från serien [Data Science for Beginners](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) (Datavetenskap för nybörjare).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-cortana-intelligence-gallery"></a>Hitta ett experiment att kopiera i Cortana Intelligence-galleriet
Du kan se vilka experiment som är tillgängliga genom att öppna [Galleriet](http://gallery.cortanaintelligence.com/) och klicka på **Experiment** högst uppe på sidan.

### <a name="find-the-newest-or-most-popular-experiments"></a>Hitta de senaste eller mest populära experimenten
På den här sidan kan du se **nyligen tillagda** experiment eller bläddra neråt för att se **populära experiment** eller de senaste **populära Microsoft-experimenten**.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Leta efter ett experiment som uppfyller specifika krav
Bläddra bland alla experiment:

1. Klicka på **Browse all** längst upp på sidan.
2. Under **Förfina efter** väljer du **Experiment** om du vill se alla experiment i galleriet.
3. Du kan hitta experiment som uppfyller kraven på ett par olika sätt:
   * **Välj filter till vänster.** Om du till exempel vill bläddra bland experiment som använder en PCA-baserad algoritm för avvikelseidentifiering väljer du **Experiment** under **Kategorier** och **PCA-baserad avvikelseidentifiering** under **Algoritmer som används**. (Om du inte ser den algoritmen klickar du på **Visa alla** längst ned i listan.)<br></br>
     ![](./media/machine-learning-sample-experiments/refine-the-view.png)
   * **Använd sökrutan.** Om du till exempel vill hitta experiment från Microsoft som rör sifferigenkänning som använder en algoritm för stödvektormaskin med två klasser anger du ”digit recognition” i sökrutan. Välj sedan filtren **Experiment**, **Endast Microsoft innehåll** och **Stödvektormaskin med två klasser**: ![](./media/machine-learning-sample-experiments/search-for-experiments.png) 
4. Klicka på ett experiment om du vill veta mer om det.
5. Om du vill köra och/eller ändra experimentet klickar du på **Open in Studio** på experimentsidan.
   
   > [!NOTE]
   > För att kunna öppna ett experiment i Machine Learning Studio måste du logga in med autentiseringsuppgifterna för ditt Microsoft-konto. Om du inte har någon arbetsyta i Machine Learning än skapas en kostnadsfri utvärderingsversion. [Mer information om vad som ingår i den kostnadsfria utvärderingsversionen av Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/)
   > 
   > 
   
    ![](./media/machine-learning-sample-experiments/example-experiment.png) 

## <a name="use-a-template-in-machine-learning-studio"></a>Använda en mall i Machine Learning Studio
Du kan även skapa ett nytt försök i Machine Learning Studio med ett exempel från galleriet som mall.

1. Skapa ett nytt experiment genom att logga in i [Studio](https://studio.azureml.net) med dina Microsoft-autentiseringsuppgifter och klicka sedan på **Nytt**.
2. Bläddra igenom exemplen och klicka på ett.

Ett nytt experiment skapas i arbetsytan med exempelexperimentet som mall.

## <a name="next-steps"></a>Nästa steg
* [Förbereda dina data](machine-learning-data-science-import-data.md)
* [Testa att använda R i experimentet](machine-learning-r-quickstart.md)
* [Se R-exempelexperiment](machine-learning-r-csharp-web-service-examples.md)
* [Skapa ett webbtjänst-API](machine-learning-publish-a-machine-learning-web-service.md)
* [Bläddra bland appar som är färdiga att användas](https://datamarket.azure.com/browse?query=machine+learning)




<!--HONumber=Nov16_HO2-->


