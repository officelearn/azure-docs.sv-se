---
title: Skapa ditt första automatiserade ML-klassificerings experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en klassificerings modell med automatisk maskin inlärning i Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 98ebc8d9408d937730643056f65c3d8011c1fdc4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493455"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Självstudie: skapa din första klassificerings modell med automatiserad maskin inlärning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

I den här självstudien får du lära dig hur du skapar ditt första automatiserade maskin inlärnings experiment genom Azure Machine Learning Studio utan att skriva en enda rad kod. I det här exemplet skapas en klassificerings modell för att förutsäga om en klient kommer att prenumerera på en fast term insättning med ett finansiellt institut.

Med automatisk maskin inlärning kan du automatisera tids krävande uppgifter. Automatisk maskin inlärning itererar snabbt över många kombinationer av algoritmer och egenskaper för att hjälpa dig att hitta den bästa modellen utifrån ett lyckat mått på hur du väljer.

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-arbetsyta.
> * Kör ett automatiserat maskin inlärnings experiment.
> * Visa experiment information.
> * Distribuera modellen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).

* Hämta data filen [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . **Y** -kolumnen visar om en kund prenumererar på en fast term insättning, som senare identifieras som mål kolumn för förutsägelser i den här självstudien. 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten. 

Du skapar en arbets yta via Azure Machine Learning Studio, en webbaserad konsol för att hantera dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="create-and-run-the-experiment"></a>Skapa och kör experimentet

Du har slutfört följande experiment med att ställa in och köra steg i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap. Studio stöds inte i Internet Explorer-webbläsare.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **Kom igång**.

1. I det vänstra fönstret väljer du **Automatisk ml** under avsnittet **författare** .

   Eftersom det här är ditt första automatiserade ML-experiment ser du en tom lista och länkar till dokumentationen.

   ![Azure Machine Learning-studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Välj **ny automatisk ml-körning**. 

1. Skapa en ny data uppsättning genom att välja **från lokala filer** från List rutan **+ skapa data uppsättning** . 

    1. Välj **Bläddra**.
    
    1. Välj filen **bankmarketing_train. csv** på den lokala datorn. Det här är den fil som du laddade ned som en [förutsättning](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Välj **tabell** som data uppsättnings typ. 

    1. Ge din data uppsättning ett unikt namn och ange en valfri beskrivning. 

    1. Välj **Nästa** längst ned till vänster för att överföra den till standard behållaren som konfigurerades automatiskt när arbets ytan skapades.  
    
       När uppladdningen är klar fylls inställningarna och förhands gransknings formuläret i förväg baserat på filtypen. 
       
    1. Kontrol lera att **inställningarna och förhands gransknings** formuläret är ifyllt enligt följande och välj **Nästa**.
        
        Fält|Beskrivning| Värde för självstudier
        ---|---|---
        Fil format|Definierar layout och typ av data som lagras i en fil.| Avgränsade
        Avgränsare|Ett eller flera tecken för att ange avgränsningen mellan&nbsp; separata, oberoende regioner i oformaterad text eller andra data strömmar. |Kommaseparerade
        Encoding|Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.| UTF-8
        Kolumn rubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.| Alla filer har samma rubriker
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.| Ingen

    1. Med hjälp av **schema** formuläret kan du ytterligare konfigurera dina data för det här experimentet. I det här exemplet väljer du växlings växeln för **day_of_week** -funktionen, så att den inte inkluderas för det här experimentet. Välj **Nästa**.

        ![Konfiguration av fliken för hands version](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

    1. I formuläret **bekräfta information** kontrollerar du att informationen stämmer överens med den **grundläggande informationen** och **inställningarna och för hands** formulären.
    1. Klicka på **skapa** för att slutföra skapandet av din data uppsättning.
    1. Välj din data uppsättning när den visas i listan.
    1. Granska **data förhands granskningen** för att se till att du inte inkluderar **day_of_week** och välj sedan **OK**.

    1. Välj **Nästa**.

1. Fyll i formuläret **Konfigurera körning** enligt följande:
    1. Ange följande experiment namn: `my-1st-automl-experiment`

    1. Välj **y** som mål kolumn, vad du vill förutsäga. Den här kolumnen visar om klienten prenumererar på en term insättning eller inte.
    1. Välj **skapa en ny beräkning** och konfigurera beräknings målet. Ett beräknings mål är en lokal eller molnbaserad resurs miljö som används för att köra ditt utbildnings skript eller som värd för tjänst distributionen. För det här experimentet använder vi en molnbaserad beräkning. 

        Fält | Beskrivning | Värde för självstudier
        ----|---|---
        Compute-namn |Ett unikt namn som identifierar din beräknings kontext.|automl – beräkning
        Virtuell&nbsp;dator&nbsp;storlek| Välj storlek på den virtuella datorn för din beräkning.|Standard_DS12_V2
        Min/max-noder (i avancerade inställningar)| Du måste ange 1 eller fler noder för att kunna profilera data.|Minsta antal noder: 1<br>Max noder: 6
  
        1. Välj **skapa** för att hämta beräknings målet. 

            **Det tar några minuter att slutföra.** 

        1. När du har skapat väljer du det nya beräknings målet i den nedrullningsbara listan.

    1. Välj **Nästa**.

1. I formuläret **uppgifts typ och inställningar** väljer du **klassificering** som typ av Machine Learning-aktivitet.

    1. Välj **Visa ytterligare konfigurations inställningar** och fyll i fälten enligt följande. De här inställningarna är för att bättre styra utbildnings jobbet. Annars tillämpas standardvärdena utifrån experiment val och data.

        >[!NOTE]
        > I den här självstudien får du inte ange mått poäng eller Max kärnor per upprepnings tröskel. Du kan inte heller blockera algoritmer från att testas.
   
        Ytterligare&nbsp;konfigurationer|Beskrivning|Värde&nbsp;för&nbsp;själv studie kurs
        ------|---------|---
        Primärt mått| Bedömnings mått som ska mätas av Machine Learning-algoritmen.|AUC_weighted
        Automatisk funktionalisering| Aktiverar för bearbetning. Detta inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner.| Aktivera
        Blockerade algoritmer | Algoritmer som du vill undanta från utbildnings jobbet| Ingen
        Avslutnings kriterium| Om ett villkor uppfylls stoppas utbildnings jobbet. |Utbildning&nbsp;jobb&nbsp;tid (timmar): 1 <br> Mått&nbsp;Poäng&nbsp;tröskel: ingen
        Validering | Välj en kors validerings typ och antalet tester.|Validerings typ:<br>&nbsp;k-vikning&nbsp;kors validering <br> <br> Antal valideringar: 2
        Samtidighet| Maximalt antal utförda parallella iterationer och kärnor som används per iteration| Max&nbsp;samtidiga&nbsp;iterationer: 5<br> Max&nbsp;kärnor&nbsp;per&nbsp;iteration: ingen
        
        Välj **OK**.

1. Välj **skapa** för att köra experimentet. Skärmen **körnings information** öppnas med **körnings statusen** när experiment förberedelsen börjar.

>[!IMPORTANT]
> Förberedelserna tar **10-15 minuter** för att förbereda experiment körningen.
> När du har kört det tar det **2-3 minuter för varje iteration**.  
> Välj **Uppdatera** regelbundet för att se statusen för körningen när experimentet fortskrider.
>
> I produktion skulle du förmodligen gå undan för en bit. I den här självstudien rekommenderar vi att du börjar utforska de testade algoritmerna på fliken modeller när de är klara medan de andra fortfarande körs. 

##  <a name="explore-models"></a>Utforska modeller

Gå till fliken **modeller** om du vill se vilka algoritmer (modeller) som har testats. Som standard sorteras modellerna efter mått poäng när de är klara. I den här självstudien visas modellen som visar den högsta baserat på det valda **AUC_weighted** -måttet överst i listan.

Medan du väntar på att alla experiment modeller ska slutföras väljer du **algoritmens namn** för en slutförd modell för att utforska dess prestanda information. 

Följande navigerar i flikarna **modell information** och **visualiseringar** för att visa den valda modellens egenskaper, mått och prestanda diagram. 

![Kör upprepnings information](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Distribuera modellen

Med automatisk maskin inlärning i Azure Machine Learning Studio kan du distribuera den bästa modellen som en webb tjänst med några få steg. Distribution är integreringen av modellen så att den kan förutsäga nya data och identifiera potentiella områden i affärs möjligheten. 

För det här experimentet innebär distribution till en webb tjänst att finans Institutet nu har en iterativ och skalbar webb lösning för att identifiera potentiella fasta, långsiktiga kunder. 

När körningen är klar går du tillbaka till sidan **körnings information** och väljer fliken **modeller** . Välj **Uppdatera**. 

I den här experiment kontexten betraktas **VotingEnsemble** som den bästa modellen, baserat på **AUC_weighted** mått.  Vi distribuerar den här modellen, men vi rekommenderar att distributionen tar ungefär 20 minuter att slutföra. Distributions processen innehåller flera steg som att registrera modellen, generera resurser och konfigurera dem för webb tjänsten.

1. Välj knappen **distribuera bästa modell** i det nedre vänstra hörnet.

1. Fyll i fönstret **distribuera bästa modell** enligt följande:

    Fält| Värde
    ----|----
    Distributions namn| My-automl – Deploy
    Distributions Beskrivning| Min första automatiserade test distribution av Machine Learning
    Bedömnings skript| Skapa
    Miljö skript| Skapa
    
1. Välj **Distribuera**.  

    I fönstret **Rekommenderad modell** visas ett meddelande om att distributionen lyckades under **distribuera status** när distributionen är klar. Välj **Uppdatera** regelbundet för att kontrol lera distributions statusen.
    
Nu har du en fungerande webb tjänst för att generera förutsägelser. 

Fortsätt till [**Nästa steg**](#next-steps) om du vill lära dig mer om hur du använder din nya webb tjänst och testa dina förutsägelser med hjälp av Power BI har inbyggd Azure Machine Learning support.

## <a name="clean-up-resources"></a>Rensa resurser

Distributions filer är större än data-och experiment-filer, så att de kostar mer att lagra. Ta bara bort distributions filerna för att minimera kostnaderna till ditt konto, eller om du vill behålla arbets ytan och experimentet. Annars tar du bort hela resurs gruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributions instansen

Ta bara bort distributions instansen från Azure Machine Learning Studio om du vill behålla resurs gruppen och arbets ytan för andra självstudier och utforskningar. 

1. Gå till [Azure Machine Learning Studio](https://ml.azure.com/). Gå till arbets ytan och välj **slut punkter**i fönstret **till** vänster. 

1. Välj den distribution som du vill ta bort och välj **ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resurs gruppen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här automatiska självstudien för maskin inlärning använde du Azure Machine Learning Studio för att skapa och distribuera en klassificerings modell. Se de här artiklarna för mer information och nästa steg:

> [!div class="nextstepaction"]
> [Använda en webb tjänst](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Läs mer om för [bearbetning](how-to-create-portal-experiments.md#preprocess).
+ Läs mer om [data profilering](how-to-create-portal-experiments.md#profile).
+ Lär dig mer om [Automatisk maskin inlärning](concept-automated-ml.md).
+ Mer information om klassificerings mått och diagram finns i artikeln [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md#classification) .

>[!NOTE]
> Den här data uppsättningen för bank marknadsföring görs tillgänglig i [CCO-licensen för Creative Commons (: offentlig domän)](https://creativecommons.org/publicdomain/zero/1.0/). Alla rättigheter i det enskilda innehållet i databasen licensieras enligt [licensen för databas innehåll](https://creativecommons.org/publicdomain/zero/1.0/) och är tillgängliga på [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Den här data uppsättningen var ursprungligen tillgänglig i den [Machine Learning databasen](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez och P. Rita. En data driven metod för att förutse framgång för bank telemarketing. Besluts support system, Elsevier, 62:22-31, juni 2014.
