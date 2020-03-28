---
title: Skapa automatiserade ML-klassificeringsmodeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar & distribuera klassificeringsmodeller med Azure Machine Learnings automatiserade datorinlärningsgränssnitt (automated ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 03/04/2020
ms.openlocfilehash: b5a335a3f215ad5883b1b223245ca9d3f9967c3b
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366533"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Självstudiekurs: Skapa en klassificeringsmodell med automatiserad ML i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här självstudien får du lära dig hur du skapar en grundläggande klassificeringsmodell utan att skriva en enda kodrad med Azure Machine Learnings automatiska maskininlärningsgränssnitt. Denna klassificeringsmodell förutsäger om en kund kommer att prenumerera på en fast löptid insättning hos ett finansinstitut.

Med automatiserad maskininlärning kan du automatisera bort tidsintensiva uppgifter. Automatiserad maskininlärning itererar snabbt över många kombinationer av algoritmer och hyperparametrar för att hjälpa dig att hitta den bästa modellen baserat på ett framgångsmått som du väljer.

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-arbetsyta.
> * Köra ett automatiserat maskininlärningsexperiment.
> * Visa experimentinformation.
> * Distribuera modellen.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://aka.ms/AMLFree).

* Ladda ner datafilen [**bankmarketing_train.csv.**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) Kolumnen **y** anger om en kund prenumererar på en fast löptid insättning, som senare identifieras som målkolumnen för förutsägelser i den här självstudien. 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning-arbetsyta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskininlärningsmodeller. Det binder din Azure-prenumeration och resursgrupp till ett lätt förbrukat objekt i tjänsten. 

Du skapar en arbetsyta via Azure-portalen, en webbaserad konsol för hantering av dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Ta del av din **arbetsyta** och **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="create-and-run-the-experiment"></a>Skapa och kör experimentet

Du slutför följande experimentinstallations- och körningssteg https://ml.azure.comvia Azure Machine learning at , ett konsoliderat webbgränssnitt som innehåller verktygsinlärningsverktyg för att utföra datavetenskapsscenarier för datavetenskapsutövare på alla färdighetsnivåer. Det här gränssnittet stöds inte i webbläsare i Internet Explorer.

1. Logga in på [Azure Machine Learning](https://ml.azure.com).

1. Välj din prenumeration och arbetsytan som du skapade.

1. Välj **Kom igång**.

1. Välj **Automatiserat ML** i den vänstra rutan under avsnittet **Författare.**

   Eftersom detta är ditt första automatiserade ML-experiment visas en tom lista och länkar till dokumentation.

   ![Sidan Kom igång](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Välj **Ny automatiserad ML-körning**. 

1. Skapa en ny datauppsättning genom att välja **Från lokala filer** i listrutan **+Skapa datauppsättning.** 

    1. I formuläret **Grundläggande information** ger du ditt datauppsättning ett namn och ger en valfri beskrivning. Det automatiska ML-gränssnittet stöder för närvarande endast TabularDatasets, så datauppsättningstypen ska som standard *tabellform*.

    1. Välj **Nästa** längst ned till vänster

    1. I formuläret **Datastore och filval** väljer du det standarddatalager som konfigurerades automatiskt när arbetsytan skapades, **workspaceblobstore (Azure Blob Storage)**. Här laddar du upp datafilen för att göra den tillgänglig på arbetsytan.

    1. Välj **Bläddra**.
    
    1. Välj **filen bankmarketing_train.csv** på den lokala datorn. Det här är filen som du hämtade som en [förutsättning](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Ge din datauppsättning ett unikt namn och ge en valfri beskrivning. 

    1. Välj **Nästa** längst ned till vänster om du vill överföra den till standardbehållaren som konfigurerades automatiskt när arbetsytan skapades.  
    
       När överföringen är klar fylls formuläret Inställningar och förhandsgranskning ifyllt baserat på filtypen. 
       
    1. Kontrollera att formuläret **Inställningar och förhandsgranskning** är ifyllt på följande sätt och välj **Nästa**.
        
        Field|Beskrivning| Värde för självstudiekurs
        ---|---|---
        Filformat|Definierar layout och typ av data som lagras i en fil.| Avgränsade
        Avgränsare|Ett eller flera tecken för att&nbsp; ange gränsen mellan separata, oberoende regioner i oformaterad text eller andra dataströmmar. |Komma
        Kodning|Identifierar vilken bit till tecken schematabell som ska användas för att läsa datauppsättningen.| UTF-8
        Kolumnrubriker| Anger hur eventuella rubriker för datauppsättningen ska behandlas.| Alla filer har samma rubriker
        Hoppa över rader | Anger hur många, om några, rader som hoppas över i datauppsättningen.| Inget

    1. **Schemaformuläret** möjliggör ytterligare konfiguration av dina data för det här experimentet. I det här exemplet väljer du **day_of_week** växlingsknappen för day_of_week-funktionen, så att den inte tas med för det här experimentet. Välj **Nästa**.

        ![Konfiguration av fliken Förhandsgranska](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. Kontrollera informationen i formuläret **Bekräfta information** som matchar vad som tidigare har fyllts i i **formulären Grundläggande information** och Inställningar och **förhandsgranska.**
    1. Välj **Skapa** för att slutföra skapandet av datauppsättningen.
    1. Markera datauppsättningen när den visas i listan.
    1. Granska **förhandsgranskningen av data** för att se till att du inte har **day_of_week** sedan väljer **du OK**.

    1. Välj **Nästa**.

1. Fyll i formuläret **Konfigurera körning** enligt följande:
    1. Ange det här experimentnamnet:`my-1st-automl-experiment`

    1. Välj **y** som målkolumn, vad du vill förutsäga. Den här kolumnen anger om kunden prenumererar på en terminsdeposition eller inte.
    1. Välj **Skapa en ny beräkning** och konfigurera beräkningsmålet. Ett beräkningsmål är en lokal eller molnbaserad resursmiljö som används för att köra ditt utbildningsskript eller vara värd för din tjänstdistribution. För det här experimentet använder vi en molnbaserad beräkning. 

        Field | Beskrivning | Värde för självstudiekurs
        ----|---|---
        Beräkningsnamn |Ett unikt namn som identifierar din beräkningskontext.|automl-beräkna
        Storlek&nbsp;&nbsp;på virtuell dator| Välj storleken på den virtuella datorn för din beräkning.|Standard_DS12_V2
        Min / Max noder (i avancerade inställningar)| Om du vill profilera data måste du ange 1 eller fler noder.|Min noder: 1<br>Max noder: 6
  
        1. Välj **Skapa** för att hämta beräkningsmålet. 

            **Detta tar ett par minuter att slutföra.** 

        1. När du har skapat det väljer du det nya beräkningsmålet i listrutan.

    1. Välj **Nästa**.

1. I formuläret **Aktivitetstyp och inställningar** väljer du **Klassificering** som maskininlärningsuppgiftstyp.

    1. Välj **Visa ytterligare konfigurationsinställningar** och fyll i fälten enligt följande. Dessa inställningar är att bättre kontrollera utbildning jobbet. Annars används standardvärden baserat på val av experiment och data.

        >[!NOTE]
        > I den här självstudien anger du inte en måttpoäng eller max kärnor per iterationströskel. Inte heller kommer du att blockera algoritmer från att testas.
   
        Ytterligare&nbsp;konfigurationer|Beskrivning|Värde&nbsp;&nbsp;för självstudiekurs
        ------|---------|---
        Primärt mått| Utvärderingsmått som maskininlärningsalgoritmen ska mätas med.|AUC_weighted
        Automatisk featurization| Aktiverar förbearbetning. Detta inkluderar automatisk datarensning, förberedelse och omvandling för att generera syntetiska funktioner.| Aktivera
        Blockerade algoritmer | Algoritmer som du vill utesluta från utbildningsjobbet| Inget
        Avslutningskriterium| Om ett villkor uppfylls stoppas utbildningsjobbet. |Utbildning&nbsp;&nbsp;jobbtid (timmar): 1 <br> Tröskelvärde&nbsp;för måttpoäng:&nbsp;Ingen
        Validering | Välj en korsvalideringstyp och antal tester.|Typ av validering:<br>&nbsp;k-fold&nbsp;korsvalidering <br> <br> Antal valideringar: 2
        Samtidighet| Det maximala antalet parallella iterationer som utförs per iteration| Max&nbsp;samtidiga&nbsp;iterationer: 5
        
        Välj **Spara**.

1. Välj **Slutför** om du vill köra experimentet. Skärmen **Kör detalj** öppnas med **körstatusen** högst upp när experimentförberedelserna börjar.

>[!IMPORTANT]
> Förberedelser tar **10-15 minuter** att förbereda experimentkörningen.
> När du kör, det tar **2-3 minuter mer för varje iteration**.  
> Välj **Uppdatera** regelbundet om du vill visa körningens status under experimentets gång.
>
> I produktionen, skulle du gå förmodligen bort för lite. Men för den här guiden föreslår vi att du börjar utforska de testade algoritmerna på fliken **Modeller** när de slutför medan de andra fortfarande körs. 

##  <a name="explore-models"></a>Utforska modeller

Navigera till fliken **Modeller** för att se de algoritmer (modeller) som testats. Som standard sorteras modellerna efter måttpoäng när de slutförs. För den här självstudien är den modell som får högst poäng baserat på det valda **AUC_weighted** måttet överst i listan.

Medan du väntar på att alla experimentmodeller ska slutföras väljer du **algoritmnamnet på** en färdig modell för att utforska dess prestandainformation. 

Följande navigerar genom **flikarna Modellinformation** och **Visualiseringar** för att visa den valda modellens egenskaper, mått och prestandadiagram. 

![Kör iterationsdetalj](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Distribuera den bästa modellen

Med det automatiska maskininlärningsgränssnittet kan du distribuera den bästa modellen som en webbtjänst i några få steg. Distribution är integreringen av modellen så att den kan förutsäga nya data och identifiera potentiella möjligheter. 

För det här experimentet innebär distribution till en webbtjänst att finansinstitutet nu har en iterativ och skalbar webblösning för att identifiera potentiella kunder med fast löptid. 

När körningen är klar navigerar du tillbaka till sidan **Körinformation** och väljer fliken **Modeller.**

I det här experimentsammanhanget anses **VotingEnsemble** vara den bästa modellen, baserat på **det AUC_weighted** måttet.  Vi distribuerar den här modellen, men det tar cirka 20 minuter att slutföra distributionen. Distributionsprocessen innebär flera steg, bland annat genom att registrera modellen, generera resurser och konfigurera dem för webbtjänsten.

1. Välj knappen **Distribuera bästa modell** i det nedre vänstra hörnet.

1. Fyll i **fönstret Distribuera en modell** enligt följande:

    Field| Värde
    ----|----
    Namn på distribution| min-automl-distribuera
    Beskrivning av distribution| Min första automatiserade maskininlärningsexperimentdistribution
    Beräkningstyp | Välj Azure Compute Instance (ACI)
    Aktivera autentisering| Inaktivera. 
    Använda anpassade distributioner| Inaktivera. Gör att standarddrivrutinsfilen (bedömningsskript) och miljöfilen kan skapas automatiskt. 
    
    I det här exemplet använder vi standardinställningarna i *menyn Avancerat.* 

1. Välj **Distribuera**.  

    Ett grönt meddelande visas högst upp på **skärmen Kör** och i fönstret **Rekommenderad modell** visas ett statusmeddelande under **Distributionsstatus**. Välj **Uppdatera** regelbundet om du vill kontrollera distributionsstatus.
    
Nu har du en fungerande webbtjänst för att generera förutsägelser. 

Gå vidare till [**nästa steg**](#next-steps) om du vill veta mer om hur du använder din nya webbtjänst och testa dina förutsägelser med hjälp av Power BI:s inbyggda Azure Machine Learning-support.

## <a name="clean-up-resources"></a>Rensa resurser

Distributionsfiler är större än data- och experimentfiler, så de kostar mer att lagra. Ta bara bort distributionsfilerna för att minimera kostnaderna för ditt konto, eller om du vill behålla arbetsytan och experimentfilerna. Annars tar du bort hela resursgruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributionsinstansen

Ta bara bort distributionsinstansen\/från Azure Machine Learning på https: /ml.azure.com/, om du vill behålla resursgruppen och arbetsytan för andra självstudier och utforskning. 

1. Gå till [Azure Machine Learning](https://ml.azure.com/). Navigera till arbetsytan och till vänster under **fönstret Tillgångar** väljer du **Slutpunkter**. 

1. Markera den distribution som du vill ta bort och välj **Ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här automatiska datorinlärningshandledningen använde du Azure Machine Learnings automatiserade ML-gränssnitt för att skapa och distribuera en klassificeringsmodell. Mer information finns i de här artiklarna:

> [!div class="nextstepaction"]
> [Använda en webbtjänst](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Läs mer om [automatiserad maskininlärning](concept-automated-ml.md).
+ Mer information om klassificeringsmått och diagram finns i artikeln [Förstå automatiska maskininlärningsresultat.+](how-to-understand-automated-ml.md#classification) Läs mer om [featurization](how-to-use-automated-ml-for-ml-models.md#featurization).
+ Läs mer om [dataprofilering](how-to-use-automated-ml-for-ml-models.md#profile).


>[!NOTE]
> Denna Bank Marketing datauppsättning görs tillgänglig under [Creative Commons (CCO: Public Domain) License](https://creativecommons.org/publicdomain/zero/1.0/). Alla rättigheter i enskilda innehåll i databasen licensieras under [licensen för databasinnehåll](https://creativecommons.org/publicdomain/zero/1.0/) och finns på [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Den här datauppsättningen var ursprungligen tillgänglig i [UCI Machine Learning Database](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro m.fl., 2014] S. Moro, P. Cortez och P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Beslutsstödssystem, Elsevier, 62:22-31, juni 2014.
