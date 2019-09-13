---
title: Skapa ditt första automatiserade maskin inlärnings experiment
titleSuffix: Azure Machine Learning service
description: Lär dig hur du tränar och distribuerar en klassificerings modell med automatiserad maskin inlärning i Azure Machine Learnings landnings sida för arbets ytan (för hands version).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 0dd4447736469644875dff914c6284b087be87d0
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910225"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Självstudier: Skapa din första klassificerings modell med automatisk maskin inlärning

I den här självstudien får du lära dig hur du skapar ditt första automatiserade maskin inlärnings experiment via sidans landnings sida (för hands version) utan att skriva en enda rad kod. I det här exemplet skapas en klassificerings modell för att förutsäga om en klient kommer att prenumerera på en fast term insättning med ett finansiellt institut.

Med automatisk maskin inlärning kan du automatisera tids krävande uppgifter. Automatisk maskin inlärning itererar snabbt över många kombinationer av algoritmer och egenskaper för att hjälpa dig att hitta den bästa modellen utifrån ett lyckat mått på hur du väljer.

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning service-arbetsyta.
> * Kör ett automatiserat maskin inlärnings experiment.
> * Visa experiment information.
> * Distribuera modellen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).

* Hämta data filen [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . **Y** -kolumnen visar om en kund prenumererar på en fast term insättning, som senare identifieras som mål kolumn för förutsägelser i den här självstudien. 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten. 

Du skapar en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="create-and-run-the-experiment"></a>Skapa och kör experimentet

Du har slutfört följande experiment konfiguration och kör steg på sidan landnings sidan för arbets ytan, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap.

1. Logga in på [sidan med landnings sidan för arbets ytan](https://ml.azure.com/workspaceportal/).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **Kom igång**.

1.  Välj **automatiserad ml** under avsnittet **redigering** i det vänstra fönstret.
Skärmen **Kom igång** visas, eftersom det första experimentet med automatiserade Machine Learning.

    ![Azure Machine Learning-studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Välj **Skapa experiment**. 

1. Ange **mitt-1st-automl-experiment** som namn på experimentet.

1. Välj **skapa en ny beräkning**. 

    1. Konfigurera din beräknings kontext för det här experimentet.
        
        Fält | Value
        ----|---
        Namn på Machine Learning-beräkning |  Ange ett unikt namn som identifierar din beräknings kontext. I det här exemplet använder du **automl-Compute**.
        Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning. Använd standard **Standard_DS12_V2**.
        Ytterligare inställningar| *Min nod*: 1. Om du vill aktivera data profilering måste du ha en eller flera noder. <br> *Högsta nod*: 6.
 
    1. Om du vill skapa din nya beräkning väljer du **skapa**. Det tar några minuter att slutföra. 

    1. När du har skapat det väljer du den nya beräkningen i list rutan och väljer sedan **Nästa**.

    >[!NOTE]
    >I den här självstudien använder du det standard lagrings konto och den behållare som skapats med din nya beräkning. De fylls i automatiskt i formuläret.

1. Välj **Ladda upp från lokal fil**. Härifrån skapar du en ny data uppsättning med **bankmarketing_train. csv** -filen som du tidigare hämtade i den här självstudien. 

    1. Välj **Bläddra** och välj sedan filen **bankmarketing_train. csv** på den lokala datorn. 

    1. Ge din data uppsättning ett unikt namn och ange en valfri beskrivning. 

    1. Välj **Nästa** för att överföra den till standard behållaren som konfigurerades automatiskt när arbets ytan skapades. Den offentliga för hands versionen stöder endast lokala fil överföringar. 

    1. När uppladdningen är klar fylls **inställningarna och förhands gransknings** formuläret i intelligent baserat på filtypen. Se till att formuläret är ifyllt på följande sätt.
        
        Fält|Value
        ---|---
        Fil format| Avgränsade
        Avgränsare| Komma
        Kodning| UTF-8
        Kolumnrubriker| Alla filer har samma huvuden hoppa över rader | Inga

        >[!NOTE]
        > Om någon av inställningarna i det här formuläret uppdateras så uppdateras förhands granskningen enligt detta.

        Välj **Nästa**.
    

    1. Med hjälp av **schema** formuläret kan du ytterligare konfigurera dina data för det här experimentet. I det här exemplet väljer du växlings växeln för **day_of_week** -funktionen, så att den inte inkluderas för det här experimentet. Välj **klar**för att slutföra fil uppladdning och skapa data uppsättningen för experimentet.

        ![Konfiguration av fliken för hands version](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Välj **klassificering** som förutsägelse uppgift.

1. Välj **y** som mål kolumn, vad du vill förutsäga. Den här kolumnen visar om klienten prenumererar på en term insättning eller inte.

1. Expandera **Avancerade inställningar** och fyll i fälten enligt följande.

    Avancerade inställningar|Value
    ------|------
    Primärt mått| AUC_weighted 
    Avslutnings villkor| När något av dessa villkor uppfylls avslutas utbildnings jobbet innan det slutförs fullständigt: <br> *Utbildnings jobb tid (minuter)* : 5  <br> *Max antal iterationer*: 10 
    Förbearbeta| Möjliggör förbehandling som genomförs av automatisk maskin inlärning. Detta inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner.
    Validering| Välj m-vik kors validering och **2** för antalet kors valideringar. 
    Samtidighet| Välj **5** för maximalt antal samtidiga iterationer.

   >[!NOTE]
   > I det här experimentet ställer du inte in mått eller Max kärnor per upprepnings tröskel. Du kan inte heller blockera algoritmer från att testas.

1. Välj **Starta** för att köra experimentet.

   När experimentet startar visas en tom skärm med ett status meddelande längst upp.

Experiment förberedelse processen tar flera minuter. När processen har slutförts körs status meddelandet för **körningen**.

##  <a name="view-experiment-details"></a>Visa experiment information

När experimentet är klart uppdaterar skärmen **upprepnings diagrammet** och **upprepnings listan** med de olika iterationer (modeller) som körs. Listan iterationer sorteras efter mått poäng. Som standard är modellen som visar högsta baserat på vårt **AUC_weighted** -mått överst i listan.

>[!WARNING]
> Utbildnings jobb tar flera minuter för varje pipeline att slutföras.

[![Körnings informations instrument panel](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Distribuera modellen

Genom att använda Automatisk maskin inlärning i landnings sidan för arbets ytan kan du distribuera den bästa modellen som en webb tjänst för att förutse nya data och identifiera potentiella områden i affärs möjligheten. För det här experimentet innebär distributionen att det finansiella institutet nu har en iterativ och skalbar lösning för att identifiera potentiella fasta, långsiktiga kunder.

I den här experiment kontexten betraktas **VotingEnsemble** som den bästa modellen, baserat på **AUC_weighted** mått.  Vi distribuerar den här modellen, men vi rekommenderar att distributionen tar ungefär 20 minuter att slutföra.

1. På sidan **körnings information** väljer du knappen **distribuera bästa modell** i det övre högra hörnet.

1. Fyll i fönstret **distribuera bästa modell** enligt följande:

    Fält| Value
    ----|----
    Distributionsnamn| My-automl – Deploy
    Distributions Beskrivning| Min första automatiserade test distribution av Machine Learning
    Bedömnings skript| Skapa
    Miljö skript| Skapa
    
1. Välj **Distribuera**.

    Ett meddelande om slutförd distribution visas när distributionen har slutförts.
    
Nu har du en fungerande webb tjänst för att generera förutsägelser.

## <a name="clean-up-resources"></a>Rensa resurser

Distributions filer är större än data-och experiment-filer, så att de kostar mer att lagra. Ta bara bort distributions filerna för att minimera kostnaderna till ditt konto, eller om du vill behålla arbets ytan och experimentet. Annars tar du bort hela resurs gruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributions instansen

Ta bara bort distributions instansen från Azure Portal, om du vill behålla resurs gruppen och arbets ytan för andra självstudier och utforskningar. 

1. Gå till [Azure-portalen](https://portal.azure.com//). Navigera till din arbets yta och välj **distributioner**till vänster under fönstret **till gångar** . 

1. Välj den distribution som du vill ta bort och välj **ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resurs gruppen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här automatiska självstudien för maskin inlärning använde du sidan för att skapa och distribuera en klassificerings modell. Se de här artiklarna för mer information och nästa steg:

> [!div class="nextstepaction"]
> [Använda en webb tjänst](how-to-consume-web-service.md)

+ Läs mer om för [bearbetning](how-to-create-portal-experiments.md#preprocess).
+ Läs mer om [data profilering](how-to-create-portal-experiments.md#profile).
+ Lär dig mer om [Automatisk maskin inlärning](concept-automated-ml.md).

>[!NOTE]
> Den här data uppsättningen för bank marknadsföring görs tillgänglig [under Creative Commons (CCO: Offentlig domän) licens](https://creativecommons.org/publicdomain/zero/1.0/). Alla rättigheter i det enskilda innehållet i databasen licensieras enligt [licensen för databas innehåll](https://creativecommons.org/publicdomain/zero/1.0/) och är tillgängliga på [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Den här data uppsättningen var ursprungligen tillgänglig i den [Machine Learning databasen](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez och P. Rita. En data driven metod för att förutse framgång för bank telemarketing. Besluts support system, Elsevier, 62:22-31, juni 2014.
