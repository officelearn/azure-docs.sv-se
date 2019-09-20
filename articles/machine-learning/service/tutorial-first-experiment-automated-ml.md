---
title: Skapa ditt första automatiserade maskin inlärnings experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en klassificerings modell med automatiserad maskin inlärning i Azure Machine Learnings landnings sida för arbets ytan (för hands version).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 8d91768d46d3e4a793982418da91f2d1877c5a79
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162550"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Självstudier: Skapa din första klassificerings modell med automatisk maskin inlärning

I den här självstudien får du lära dig hur du skapar ditt första automatiserade maskin inlärnings experiment via sidans landnings sida (för hands version) utan att skriva en enda rad kod. I det här exemplet skapas en klassificerings modell för att förutsäga om en klient kommer att prenumerera på en fast term insättning med ett finansiellt institut.

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

1. Ange följande experiment namn:`my-1st-automl-experiment`

1. Välj **skapa en ny beräkning** och konfigurera beräknings målet. Ett beräknings mål är en lokal eller molnbaserad resurs miljö som används för att köra ditt utbildnings skript eller vara värd för tjänst distributionen. För det här experimentet använder vi en molnbaserad beräkning. 

   Fält | Beskrivning | Värde för självstudier
   ----|---|---
   Namn på Machine Learning-beräkning |Ett unikt namn som identifierar din beräknings kontext.|automl – beräkning
   Storlek&nbsp;på&nbsp;virtuell dator| Välj storlek på den virtuella datorn för din beräkning.|Standard_DS12_V2
   Min/max-noder (i avancerade inställningar)| Du måste ange 1 eller fler noder för att kunna profilera data.|Minsta antal noder: 1<br>Högsta antal noder: 6

   >[!NOTE]
   >I den här självstudien använder du det standard lagrings konto och den behållare som skapats med din nya beräkning. De fylls i automatiskt i formuläret.
    
1. Välj **skapa** för att hämta beräknings målet. 
   **Det tar några minuter att slutföra.** 

1. När du har skapat väljer du det nya beräknings målet i list rutan och väljer **Nästa**.

1. Välj **överför från lokal fil** för att börja skapa en ny data uppsättning. 

    1. Välj **Bläddra**.
    
    1. Välj filen **bankmarketing_train. csv** på den lokala datorn. Det här är den fil som du laddade ned som en [förutsättning](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Ge din data uppsättning ett unikt namn och ange en valfri beskrivning. 

    1. Välj **Nästa** längst ned till vänster för att överföra den till standard behållaren som konfigurerades automatiskt när arbets ytan skapades. Den offentliga för hands versionen stöder endast lokala fil överföringar. 

    1. När uppladdningen är klar fylls **inställningarna och förhands gransknings** formuläret i intelligent baserat på filtypen. Se till att formuläret är ifyllt på följande sätt.
        
        Fält|Value
        ---|---
        Filformat| Avgränsad
        Avgränsare| Komma
        Kodning| UTF-8
        Kolumnrubriker| Alla filer har samma rubriker
        Hoppa över rader | Inga

        Välj **Nästa**.
    
    1. Med hjälp av **schema** formuläret kan du ytterligare konfigurera dina data för det här experimentet. I det här exemplet väljer du växlings växeln för **day_of_week** -funktionen, så att den inte inkluderas för det här experimentet. Välj **klar**för att slutföra fil uppladdning och skapa data uppsättningen för experimentet.

        ![Konfiguration av fliken för hands version](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Välj **klassificering** som förutsägelse uppgift.

1. Välj **y** som mål kolumn, vad du vill förutsäga. Den här kolumnen visar om klienten prenumererar på en term insättning eller inte.

1. Expandera **Avancerade inställningar** och fyll i fälten enligt följande.

   >[!NOTE]
   > För det här experimentet ställer du inte in mått poäng eller Max kärnor per upprepnings tröskel. Du kan inte heller blockera algoritmer från att testas.
   
    Avancerade&nbsp;inställningar|Beskrivning|Värde&nbsp;för&nbsp;självstudier
    ------|---------|---
    Primärt mått| Bedömnings mått som ska mätas av Machine Learning-algoritmen.|**AUC_weighted** 
    Avslutnings villkor| När något av dessa villkor uppfylls avslutas utbildnings jobbet även om det inte har slutförts fullständigt. |Utbildnings&nbsp;jobb&nbsp;tid&nbsp;(minuter): **5**  <br> <br> Max&nbsp;# för iterationer&#58;10&nbsp;&nbsp; 
    Förbearbeta| Möjliggör förbehandling som genomförs av automatisk maskin inlärning. Detta inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner.| Aktivera
    Verifiering| Validerings typ och antal tester. | **K-vikning** kors validering<br><br>  kors valideringar: **2** 
    Samtidighet| Maximalt antal samtidiga iterationer.|**5**

1. Välj **Starta** för att köra experimentet. En skärm visas med ett status meddelande när experiment förberedelsen börjar.

>[!IMPORTANT]
> Förberedelserna tar **10-15 minuter** för att förbereda experiment körningen. När du har kört det tar det **2-3 minuter för varje iteration**.  
>
> I produktion skulle du förmodligen gå undan för en bit. Men i den här själv studie kursen föreslår vi att du börjar utforska upprepnings resultatet när de är klara medan de andra fortfarande körs. 

##  <a name="explore-iteration-results"></a>Utforska upprepnings resultat

När experimentet uppdateras, uppdaterar skärmen **upprepnings diagrammet** och **upprepnings listan** med de olika iterationer (modeller) som skapas när de slutförs och sorterar dem efter metriska poäng. Som standard är modellen som visar högsta baserat på det valda **AUC_weighted** -måttet överst i listan.

Medan du väntar på att alla experiment iterationer ska slutföras, väljer du **namnet** på en slutförd iteration för att utforska dess prestanda information. 
   
Följande visar de diagram och körnings mått som genereras för varje iteration, till exempel en precisions återställnings kurva, en Förväxlings mat ris, viktade noggrannhets resultat osv. 

![Kör upprepnings information](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Distribuera modellen

Med automatisk maskin inlärning på sidan landnings plats kan du distribuera den bästa modellen som en webb tjänst med några få steg. Distribution är integreringen av modellen så att den kan förutsäga nya data och identifiera potentiella områden i affärs möjligheten. För det här experimentet innebär distribution till en webb tjänst att finans Institutet nu har en iterativ och skalbar webb lösning för att identifiera potentiella fasta, långsiktiga kunder. 

När körningen är klar går du tillbaka till detalj sidan **upprepnings diagram** och **upprepnings lista** . 

I den här experiment kontexten betraktas **VotingEnsemble** som den bästa modellen, baserat på **AUC_weighted** mått.  Vi distribuerar den här modellen, men vi rekommenderar att distributionen tar ungefär 20 minuter att slutföra. Distributions processen innehåller flera steg som att registrera modellen, generera resurser och konfigurera dem för webb tjänsten.

1. Välj knappen **distribuera bästa modell** i det övre högra hörnet.

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
