---
title: Kickstart experiment från exempel
titleSuffix: ML Studio (classic) Azure
description: Lär dig att använda exempel Machine Learning-experiment för att skapa nya experiment med Azure AI Gallery och Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: fa23612070a5b0c1a33c2effb9cd9e533ca5eada
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670648"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Skapa Azure Machine Learning Studio (klassiska) experiment från arbets exempel i Azure AI Gallery

Lär dig hur du kommer igång med exempelexperiment från [Azure AI-galleriet](https://gallery.azure.ai/) i stället för att skapa maskininlärningsexperiment från grunden. Du kan använda exemplen för att skapa din egen maskininlärningslösning.

Galleriet har exempel på experiment av Microsoft Azure Machine Learning Studio (klassiskt)-teamet samt exempel som delas av Machine Learning communityn. Du kan även ställa frågor eller lämna kommentarer om experiment.

Om du vill se hur du använder galleriet kan du titta på den 3 minuter långa videon [Copy other people's work to do data science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Kopiera andras arbete för datavetenskap) från serien [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) (Datavetenskap för nybörjare).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Sök ett experiment att kopiera i Azure AI-galleriet
Du kan se vilka experiment som är tillgängliga genom att öppna [Galleriet](https://gallery.azure.ai/) och klicka på **Experiment** högst uppe på sidan.

### <a name="find-the-newest-or-most-popular-experiments"></a>Hitta de senaste eller mest populära experimenten
På den här sidan kan du se **nyligen tillagda** experiment eller bläddra neråt för att se **populära experiment** eller de senaste **populära Microsoft-experimenten**.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Leta efter ett experiment som uppfyller specifika krav
Bläddra bland alla experiment:

1. Klicka på **Browse all** längst upp på sidan.
2. Till vänster, under **Förfina efter** i avsnittet **Kategorier**, väljer du **Experiment** för att visa alla experiment i galleriet.
3. Du kan hitta experiment som uppfyller kraven på ett par olika sätt:
   * **Välj filter till vänster.** Om du till exempel vill bläddra igenom experiment som använder en algoritm för PCA-baserad avvikelseidentifiering gör du så här: Under **Kategorier** klickar du på **Experiment**. Under **Använda algoritmer** klickar du sedan på **Visa alla**, och i dialogrutan väljer du **PCA-baserad avvikelseidentifiering**. Du kanske måste bläddra för att se det.<br></br>
     ![Välj filter](./media/sample-experiments/choose-an-algorithm.png)
   * **Använd sökrutan.** Om du till exempel vill hitta experiment från Microsoft som rör sifferigenkänning som använder en algoritm för stödvektormaskin med två klasser anger du ”digit recognition” i sökrutan. Välj sedan filtren **Experiment**, **Endast Microsoft innehåll** och **Stödvektormaskin med två klasser**:<br></br>
     ![Använd sökrutan](./media/sample-experiments/search-for-experiments.png)
4. Klicka på ett experiment om du vill veta mer om det.
5. Om du vill köra och/eller ändra experimentet klickar du på **Open in Studio** på experimentsidan. <br></br>

    ![Exempelexperiment](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Skapa ett nytt experiment med ett exempel som mall
Du kan också skapa ett nytt experiment i den klassiska versionen av Machine Learning Studio med hjälp av ett galleri exempel som en mall.

1. Skapa ett experiment genom att logga in i [Studio](https://studio.azureml.net) med dina autentiseringsuppgifter för Microsoft-kontot och klicka sedan på **Nytt**.
2. Bläddra igenom exemplen och klicka på ett.

Ett nytt experiment skapas på din Machine Learning Studio (klassiska) arbets yta med hjälp av exempel experimentet som en mall.

## <a name="next-steps"></a>Nästa steg
* [Importera data från olika källor](import-data.md)
* [Snabbstartssjälvstudier till R-språket i Machine Learning](r-quickstart.md)
* [Distribuera en Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md)
