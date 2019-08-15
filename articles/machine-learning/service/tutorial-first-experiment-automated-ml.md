---
title: Skapa ditt första automatiserade maskin inlärnings experiment
titleSuffix: Azure Machine Learning service
description: Lär dig hur du tränar och distribuerar en klassificerings modell med automatisk maskin inlärning i Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7ef19db472b30d82f14a5dd650cb8f4cb1f3ed3a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990082"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>Självstudier: Använd automatisk maskin inlärning för att träna och distribuera din första klassificerings modell (för hands version)

I den här självstudien får du lära dig hur du skapar ditt första automatiserade maskin inlärnings experiment i Azure Portal. I det här exemplet skapas en klassificerings modell för att förutsäga om en klient kommer att prenumerera på en term insättning med banken.

Genom att använda de automatiserade maskin inlärnings funktionerna i tjänsten och Azure Portal startar du den automatiserade Machine Learning-processen. Valet av val och inställning för min parameter görs åt dig. Den automatiserade Machine Learning-tekniken upprepas för många kombinationer av algoritmer och disponeringsparametrarna tills den hittar den bästa modellen baserat på ditt kriterium, allt utan att du behöver skriva en enda rad kod.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Konfigurera en Azure Machine Learning-tjänstarbetsyta.
> * Skapa ett experiment.
> * Automatisk träna en klassificerings modell.
> * Visa information om tränings körningar.
> * Distribuera modellen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).

* Data filen **bankmarketing_train. csv** . [Hämta den](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Skapa ett experiment

1. Gå till den vänstra rutan i din arbets yta. Välj **Automatisk maskin inlärning** under avsnittet **redigering (för hands version)** .

    ![Azure Portal navigerings fönster](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Eftersom det här är ditt första experiment med automatiserade Machine Learning visas skärmen **Välkommen till automatiserat Machine Learning** . 

1. Välj **Skapa experiment**. Ange sedan **mitt-1st-automl-experiment** som namn på experimentet.

1. Välj **skapa en ny beräkning** och konfigurera din beräknings kontext för det här experimentet.

    Fält| Value
    ---|---
    Namn på Machine Learning-beräkning| Ange ett unikt namn som identifierar din beräknings kontext. I det här exemplet använder vi **automl-Compute**.
    Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning. Vi använder **Standard_DS12_V2**.
    Ytterligare inställningar| *Min nod*: 1. Om du vill aktivera data profilering måste du ha en eller flera noder. <br> *Högsta nod*: 6. 

    Om du vill skapa din nya beräkning väljer du **skapa**. Det tar en stund. 

    När du har skapat det väljer du den nya beräkningen i list rutan och väljer sedan **Nästa**.

1. I den här självstudien använder vi standard lagrings kontot och den behållare som skapats med din nya beräkning. De fylls i automatiskt i formuläret.

1. Välj **överför** och välj filen **bankmarketing_train. csv** från den lokala datorn för att överföra den till standard behållaren. Den offentliga för hands versionen stöder endast lokala fil överföringar och Azure Blob Storage-konton. När uppladdningen är klar väljer du filen i listan. 

    [![Välj datafil](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. På fliken för **hands version** kan vi ytterligare konfigurera våra data för det här experimentet.

    På fliken **förhands granskning** anger du att data innehåller rubriker. Tjänsten använder som standard alla funktioner (kolumner) för utbildning. I det här exemplet ska du bläddra till höger och **Ignorera** funktionen **day_of_week** .

    ![Konfiguration av fliken för hands version](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Data profilering är inte tillgängligt med beräkningar som har noll lägsta noder.

1. Välj **klassificering** som förutsägelse uppgift.

1. Välj **y** som mål kolumn, där vi vill göra förutsägelser. Den här kolumnen visar om klienten prenumererar på en term insättning eller inte.

1. Expandera **Avancerade inställningar** och fyll i fälten enligt följande.

    Avancerade inställningar|Value
    ------|------
    Primärt mått| AUC_weighted 
    Avslutnings villkor| När något av dessa villkor uppfylls avslutas utbildnings jobbet innan det slutförs fullständigt: <br> *Utbildnings jobb tid (minuter)* : 5  <br> *Max antal iterationer*: 10 
    Förbearbeta| Möjliggör förbehandling som genomförs av automatisk maskin inlärning. Detta inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner.
    Validering| Välj m-vik kors validering och **2** för antalet kors valideringar. 
    Samtidighet| Välj **5** för maximalt antal samtidiga iterationer.

   >[!NOTE]
   > För det här experimentet ställer vi inte in mått eller Max kärnor per upprepnings tröskel. Vi blockerar inte heller algoritmer från att testas.

1. Välj **Starta** för att köra experimentet.

   När experimentet startar visas en tom körnings **informations** skärm med följande status överst. 

      ![Kör förberedelse](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
Experiment förberedelse processen tar några minuter. När processen har slutförts **körs**status meddelandet för körningen.

##  <a name="view-experiment-details"></a>Visa experiment information

När experimentet fortsätter uppdateras upprepnings diagrammet och listan med de olika iterationer (modeller) som körs på skärmen **Kör information** . Listan iterationer sorteras efter mått poäng. Som standard är modellen som visar högsta baserat på vårt **AUC_weighted** -mått överst i listan.

>[!TIP]
> Utbildnings jobb tar flera minuter för varje pipeline att slutföras.

[![Körnings informations instrument panel](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Distribuera modellen

I det här experimentet betraktas **VotingEnsemble** som den bästa modellen, baserat på **AUC_weighted** mått. Genom att använda Automatisk maskin inlärning i Azure Portal kan vi distribuera den här modellen som en webb tjänst för att förutse nya data. 

1. På sidan **körnings information** väljer du knappen **distribuera bästa modell** .

1. Fyll i fönstret **distribuera bästa modell** enligt följande:

    Fält| Value
    ----|----
    Distributionsnamn| My-automl – Deploy
    Distributions Beskrivning| Min första automatiserade test distribution av Machine Learning
    Bedömnings skript| Skapa
    Miljö skript| Skapa
    
1. Välj **Distribuera**. Distributionen tar ungefär 20 minuter att slutföra.

    Följande meddelande visas när distributionen har slutförts:

    ![Distributionen är klar](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    Klart! Du har en fungerande webb tjänst för att generera förutsägelser.

## <a name="clean-up-resources"></a>Rensa resurser

Distributions filer är större än data-och experiment-filer, så att de kostar mer att lagra. Ta bara bort distributions filerna för att minimera kostnaderna till ditt konto, eller om du vill behålla arbets ytan och experimentet. Annars tar du bort hela resurs gruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributions instansen

Ta bara bort distributions instansen från Azure Portal, om du vill behålla resurs gruppen och arbets ytan för andra självstudier och utforskningar. 

1. Gå till fönstret **till gångar** till vänster och välj **distributioner**. 

1. Välj den distribution som du vill ta bort och välj **ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resurs gruppen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här automatiska självstudien om Machine Learning använde du Azure Portal för att skapa och distribuera en klassificerings modell. Se de här artiklarna för mer information och nästa steg:

+ Lär dig hur du [använder en webb tjänst](how-to-consume-web-service.md).
+ Läs mer om för [bearbetning](how-to-create-portal-experiments.md#preprocess).
+ Läs mer om [data profilering](how-to-create-portal-experiments.md#profile).
+ Lär dig mer om [Automatisk maskin inlärning](concept-automated-ml.md).

>[!NOTE]
> Den här data uppsättningen för bank marknadsföring görs tillgänglig [under Creative Commons (CCO: Offentlig domän) licens](https://creativecommons.org/publicdomain/zero/1.0/). Alla rättigheter i det enskilda innehållet i databasen licensieras enligt [licensen för databas innehåll](https://creativecommons.org/publicdomain/zero/1.0/) och är tillgängliga på [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Den här data uppsättningen var ursprungligen tillgänglig i den [Machine Learning databasen](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> Vänligen citera följande arbete: <br> [Moro et al., 2014] S. Moro, P. Cortez och P. Rita. En data driven metod för att förutse framgång för bank telemarketing. Besluts support system, Elsevier, 62:22-31, juni 2014.
