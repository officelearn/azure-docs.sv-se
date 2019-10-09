---
title: Skapa ditt första automatiserade ML-klassificerings experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en klassificerings modell med automatiserad maskin inlärning i Azure Machine Learnings landnings sida för arbets ytan (för hands version).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: dcd6f2ea6f5c79664af0c2431da07549e71c26bc
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035680"
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

Du har slutfört följande experiment konfiguration och kör steg på sidan landnings sidan för arbets ytan, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap. Landnings sidan för arbets ytan stöds inte i Internet Explorer-webbläsare.

1. Logga in på [sidan med landnings sidan för arbets ytan](https://ml.azure.com/workspaceportal/).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **Kom igång**.

1. I det vänstra fönstret väljer du **Automatisk ml** under avsnittet **författare** .

   Eftersom det här är ditt första automatiserade ML experiment visas skärmen kom igång.

   ![Azure Machine Learning-studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Välj **Skapa experiment**. 

1. Ange följande experiment namn: `my-1st-automl-experiment`

1. Välj **skapa en ny beräkning** och konfigurera beräknings målet. Ett beräknings mål är en lokal eller molnbaserad resurs miljö som används för att köra ditt utbildnings skript eller vara värd för tjänst distributionen. För det här experimentet använder vi en molnbaserad beräkning. 

   Fält | Beskrivning | Värde för självstudier
   ----|---|---
   Compute-namn |Ett unikt namn som identifierar din beräknings kontext.|automl – beräkning
   Virtual @ no__t-0machine @ no__t-1size| Välj storlek på den virtuella datorn för din beräkning.|Standard_DS12_V2
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
    
       När uppladdningen är klar fylls inställningarna och förhands gransknings formuläret i förväg baserat på filtypen. 
       
    1. Kontrol lera att **inställningarna och förhands gransknings** formuläret är ifyllt enligt följande och välj **Nästa**.
        
        Fält|Beskrivning| Värde för självstudier
        ---|---|---
        Fil format|Definierar layout och typ av data som lagras i en fil.| Avgränsade
        Avgränsare|Ett eller flera tecken för att ange avgränsningen mellan @ no__t-0 separata, oberoende regioner i oformaterad text eller andra data strömmar. |Kommaseparerade
        Kodning|Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.| UTF-8
        Kolumn rubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.| Alla filer har samma rubriker
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.| Inga
    
        ![Konfiguration av fliken för hands version](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Välj **klassificering** som förutsägelse uppgift.

1. Välj **y** som mål kolumn, vad du vill förutsäga. Den här kolumnen visar om klienten prenumererar på en term insättning eller inte.

1. Expandera **Avancerade inställningar** och fyll i fälten enligt följande. De här inställningarna är för att bättre styra utbildnings jobbet. Annars tillämpas standardvärdena utifrån experiment val och data.

   >[!NOTE]
   > I den här självstudien får du inte ange mått poäng eller Max kärnor per upprepnings tröskel. Du kan inte heller blockera algoritmer från att testas.
   
   Avancerade @ no__t – 0settings|Beskrivning|Värde @ no__t-0for @ no__t-1tutorial
   ------|---------|---
   Primärt mått| Bedömnings mått som ska mätas av Machine Learning-algoritmen.|AUC_weighted
   Avslutnings villkor| Om ett villkor uppfylls stoppas utbildnings jobbet. |Utbildning @ no__t-0job @ no__t-1time: 5 <br> <br> Max @ no__t-0 @ no__t-1 @ no__t-2of @ no__t-3iterations&#58;10
   Förbearbeta| Möjliggör förbehandling som genomförs av automatisk maskin inlärning. Detta inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner.| Aktivera
   Validerings typ | Välj en typ av kors validering.|K-vikning kors validering
   Antal valideringar | Antal tester. | 2 kors valideringar 
   Samtidighet| Maximalt antal samtidiga iterationer.|5
   
1. Välj **Starta** för att köra experimentet. En skärm visas med ett status meddelande när experiment förberedelsen börjar.

>[!IMPORTANT]
> Förberedelserna tar **10-15 minuter** för att förbereda experiment körningen. När du har kört det tar det **2-3 minuter för varje iteration**.  
>
> I produktion skulle du förmodligen gå undan för en bit. Men i den här själv studie kursen föreslår vi att du börjar utforska upprepnings resultatet när de är klara medan de andra fortfarande körs. 

##  <a name="explore-iteration-results"></a>Utforska upprepnings resultat

När experimentet uppdateras, uppdaterar skärmen **upprepnings diagrammet** och **upprepnings listan** med olika iterationer (modeller) som skapats när de är klara. Som standard sorteras iterationerna efter mått poäng. I den här självstudien visas modellen som visar den högsta baserat på det valda **AUC_weighted** -måttet överst i listan.

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
    Distributions namn| My-automl – Deploy
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
+ Mer information om klassificerings mått och diagram finns i artikeln [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md#classification) .

>[!NOTE]
> Den här data uppsättningen för bank marknadsföring görs tillgänglig i [Creative Commons (CCO: Offentlig domän) licens @ no__t-0. Alla rättigheter i det enskilda innehållet i databasen licensieras enligt [licensen för databas innehåll](https://creativecommons.org/publicdomain/zero/1.0/) och är tillgängliga på [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Den här data uppsättningen var ursprungligen tillgänglig i den [Machine Learning databasen](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez och P. Rita. En data driven metod för att förutse framgång för bank telemarketing. Besluts support system, Elsevier, 62:22-31, juni 2014.
