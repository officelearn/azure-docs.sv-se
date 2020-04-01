---
title: Prognos cykel delning efterfrågan med automatiserade ML experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en modell för efterfrågeprognoser med automatiserad maskininlärning i Azure Machine Learning studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088244"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Självstudiekurs: Prognos cykeldelning efterfrågan med automatiserad maskininlärning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här självstudien använder du automatiserad maskininlärning, eller automatiserad ML, i Azure Machine Learning-studion för att skapa en prognosmodell för tidsserier för att förutsäga hyresbehovet för en cykeldelningstjänst.

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa och läs in en datauppsättning.
> * Konfigurera och kör ett automatiserat ML-experiment.
> * Utforska experimentresultaten.
> * Distribuera den bästa modellen.

## <a name="prerequisites"></a>Krav

* En Arbetsyta för Azure Machine Learning i Företagsutgåva. Om du inte har en arbetsyta [skapar du en arbetsyta för Enterprise Edition](how-to-manage-workspace.md). 
    * Automatiserad maskininlärning i Azure Machine Learning-studion är endast tillgänglig för arbetsytor i Företagsutgåvor. 
* Ladda ner [datafilen bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Kom igång i Azure Machine Learning studio

För den här självstudien skapar du din automatiska ML-experimentkörning i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller verktygsinlärningsverktyg för att utföra datavetenskapsscenarier för datavetenskapsutövare på alla färdighetsnivåer. Studion stöds inte i webbläsare i Internet Explorer.

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com).

1. Välj din prenumeration och arbetsytan som du skapade.

1. Välj **Kom igång**.

1. Välj **Automatiserat ML** i den vänstra rutan under avsnittet **Författare.**

1. Välj **+Ny automatiserad ML-körning**. 

## <a name="create-and-load-dataset"></a>Skapa och läsa in datauppsättning

Innan du konfigurerar experimentet laddar du upp datafilen till arbetsytan i form av en Azure Machine Learning-datauppsättning. På så sätt kan du se till att dina data är korrekt formaterade för experimentet.

1. I formuläret **Välj datauppsättning** väljer du **Från lokala filer** i listrutan **+Skapa datauppsättning.** 

    1. I formuläret **Grundläggande information** ger du ditt datauppsättning ett namn och ger en valfri beskrivning. Datauppsättningstypen ska som standard **tabellforma**, eftersom automatiserad ML i Azure Machine Learning studio för närvarande endast stöder tabelldatauppsättningar.
    
    1. Välj **Nästa** längst ned till vänster

    1. I formuläret **Datastore och filval** väljer du det standarddatalager som konfigurerades automatiskt när arbetsytan skapades, **workspaceblobstore (Azure Blob Storage)**. Det här är lagringsplatsen där du laddar upp datafilen. 

    1. Välj **Bläddra**. 
    
    1. Välj **filen bike-no.csv** på den lokala datorn. Det här är filen som du hämtade som en [förutsättning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Välj **Nästa**

       När överföringen är klar fylls formuläret Inställningar och förhandsgranskning ifyllt baserat på filtypen. 
       
    1. Kontrollera att formuläret **Inställningar och förhandsgranskning** är ifyllt på följande sätt och välj **Nästa**.
        
        Field|Beskrivning| Värde för självstudiekurs
        ---|---|---
        Filformat|Definierar layout och typ av data som lagras i en fil.| Avgränsade
        Avgränsare|Ett eller flera tecken för att&nbsp; ange gränsen mellan separata, oberoende regioner i oformaterad text eller andra dataströmmar. |Komma
        Kodning|Identifierar vilken bit till tecken schematabell som ska användas för att läsa datauppsättningen.| UTF-8
        Kolumnrubriker| Anger hur eventuella rubriker för datauppsättningen ska behandlas.| Använda rubriker från den första filen
        Hoppa över rader | Anger hur många, om några, rader som hoppas över i datauppsättningen.| Inget

    1. **Schemaformuläret** möjliggör ytterligare konfiguration av dina data för det här experimentet. 
    
        1. I det här exemplet väljer du att ignorera de **tillfälliga** och **registrerade** kolumnerna. Dessa kolumner är en uppdelning av **cnt** kolumnen så därför tar vi inte med dem.

        1. Lämna även standardvärdena för **egenskaper** och **typ**. 
        
        1. Välj **Nästa**.

    1. Kontrollera informationen i formuläret **Bekräfta information** som matchar vad som tidigare har fyllts i i **formulären Grundläggande information** och Inställningar och **förhandsgranska.**

    1. Välj **Skapa** för att slutföra skapandet av datauppsättningen.

    1. Markera datauppsättningen när den visas i listan.

    1. Välj **Nästa**.

## <a name="configure-experiment-run"></a>Konfigurera experimentkörning

När du har laddat och konfigurerat dina data ställer du in fjärrberäkningsmålet och väljer vilken kolumn i dina data du vill förutsäga.

1. Fyll i formuläret **Konfigurera körning** enligt följande:
    1. Ange ett experimentnamn:`automl-bikeshare`

    1. Välj **cnt** som målkolumn, vad du vill förutsäga. Den här kolumnen anger antalet totala cykeldelningsuthyrningar.

    1. Välj **Skapa en ny beräkning** och konfigurera beräkningsmålet. Automatiserad ML stöder endast Azure Machine Learning-beräkning. 

        Field | Beskrivning | Värde för självstudiekurs
        ----|---|---
        Beräkningsnamn |Ett unikt namn som identifierar din beräkningskontext.|cykel-beräkning
        Storlek&nbsp;&nbsp;på virtuell dator| Välj storleken på den virtuella datorn för din beräkning.|Standard_DS12_V2
        Min / Max noder (i avancerade inställningar)| Om du vill profilera data måste du ange 1 eller fler noder.|Min noder: 1<br>Max noder: 6
  
        1. Välj **Skapa** för att hämta beräkningsmålet. 

            **Detta tar ett par minuter att slutföra.** 

        1. När du har skapat det väljer du det nya beräkningsmålet i listrutan.

    1. Välj **Nästa**.

## <a name="select-task-type-and-settings"></a>Välj uppgiftstyp och inställningar

Slutför installationen för det automatiska ML-experimentet genom att ange maskininlärningsuppgiftstyp och konfigurationsinställningar.

1. I formuläret **Aktivitetstyp och inställningar** väljer du **Prognostisering av tidsserier** som aktivitetstyp för maskininlärning.

1. Välj **datum** som **kolumnen Tid** och lämna Grupp efter **kolumn(er)** tom. 

    1. Välj **Visa ytterligare konfigurationsinställningar** och fyll i fälten enligt följande. Dessa inställningar är att bättre kontrollera utbildning jobbet. Annars används standardvärden baserat på val av experiment och data.

  
        Ytterligare&nbsp;konfigurationer|Beskrivning|Värde&nbsp;&nbsp;för självstudiekurs
        ------|---------|---
        Primärt mått| Utvärderingsmått som maskininlärningsalgoritmen ska mätas med.|Normaliserat rotmedelvärde i kvadratfel
        Automatisk featurization| Aktiverar förbearbetning. Detta inkluderar automatisk datarensning, förberedelse och omvandling för att generera syntetiska funktioner.| Aktivera
        Förklara bästa modellen (förhandsgranskning)| Visar automatiskt explainability på den bästa modellen som skapats av automatiserade ML.| Aktivera
        Blockerade algoritmer | Algoritmer som du vill utesluta från utbildningsjobbet| Extrema slumpmässiga träd
        Ytterligare prognosinställningar| De här inställningarna hjälper till att förbättra modellens noggrannhet <br><br> _**Prognoshorisont:**_ hur lång tid in i framtiden du vill förutsäga <br> _**Prognosmål släpar efter:**_ hur långt tillbaka du vill konstruera eftersläpningar av en målvariabel <br> _**Målrullningsfönstret**_: anger storleken på det rullande fönstret över vilka funktioner, till exempel *max, min* och *summa,* som ska genereras. |Prognoshorisont: 14 <br> &nbsp;Prognosmål&nbsp;släpar efter: Ingen <br> &nbsp;Målrullfönstrets&nbsp;&nbsp;storlek: Ingen
        Avslutningskriterium| Om ett villkor uppfylls stoppas utbildningsjobbet. |Utbildning&nbsp;&nbsp;jobbtid (timmar): 3 <br> Tröskelvärde&nbsp;för måttpoäng:&nbsp;Ingen
        Validering | Välj en korsvalideringstyp och antal tester.|Typ av validering:<br>&nbsp;k-fold&nbsp;korsvalidering <br> <br> Antal valideringar: 5
        Samtidighet| Det maximala antalet parallella iterationer som utförs per iteration| Max&nbsp;samtidiga&nbsp;iterationer: 6
        
        Välj **Spara**.

## <a name="run-experiment"></a>Kör experiment

Om du vill köra experimentet väljer du **Slutför**. Skärmen **Kör information** öppnas med **körstatusen** högst upp bredvid körningsnumret. Den här statusen uppdateras under experimentet.

>[!IMPORTANT]
> Förberedelser tar **10-15 minuter** att förbereda experimentkörningen.
> När du kör, det tar **2-3 minuter mer för varje iteration**.  <br> <br>
> I produktionen, skulle du sannolikt gå bort för lite som denna process tar tid. Medan du väntar föreslår vi att du börjar utforska de testade algoritmerna på fliken **Modeller** när de är klara. 

##  <a name="explore-models"></a>Utforska modeller

Navigera till fliken **Modeller** för att se de algoritmer (modeller) som testats. Som standard sorteras modellerna efter måttpoäng när de slutförs. För den här självstudien är den modell som får högst poäng baserat på det valda **normaliserade rotmedelvärdet för kvadratfel** högst upp i listan.

Medan du väntar på att alla experimentmodeller ska slutföras väljer du **algoritmnamnet på** en färdig modell för att utforska dess prestandainformation. 

I följande exempel navigeras du genom **flikarna Modellinformation** och **Visualiseringar** för att visa den valda modellens egenskaper, mått och prestandadiagram. 

![Kör detalj](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Distribuera modellen

Med automatiserad maskininlärning i Azure Machine Learning studio kan du distribuera den bästa modellen som en webbtjänst i några få steg. Distribution är integreringen av modellen så att den kan förutsäga nya data och identifiera potentiella möjligheter. 

För det här experimentet innebär distribution till en webbtjänst att cykelandelsföretaget nu har en iterativ och skalbar webblösning för prognostisering av cykeldelningsuthyrningsbehov. 

När körningen är klar navigerar du tillbaka till sidan **Körinformation** och väljer fliken **Modeller.**

I det här experimentsammanhanget anses **StackEnsemble** vara den bästa modellen, baserat på det **normaliserade rotmedelvärdet för kvadratfel.**  Vi distribuerar den här modellen, men det tar cirka 20 minuter att slutföra distributionen. Distributionsprocessen innebär flera steg, bland annat genom att registrera modellen, generera resurser och konfigurera dem för webbtjänsten.

1. Välj knappen **Distribuera bästa modell** i det nedre vänstra hörnet.

1. Fyll i **fönstret Distribuera en modell** enligt följande:

    Field| Värde
    ----|----
    Namn på distribution| bikeshare-distribuera
    Beskrivning av distribution| distribution av cykelandelsbehov
    Beräkningstyp | Välj Azure Compute Instance (ACI)
    Aktivera autentisering| Inaktivera. 
    Använda anpassade distributionsresurser| Inaktivera. Om du inaktiverar den standarddrivrutinsfil (bedömningsskript) och miljöfilen kan du skapa automatiskt. 
    
    I det här exemplet använder vi standardinställningarna i *menyn Avancerat.* 

1. Välj **Distribuera**.  

    Ett grönt meddelande visas högst upp på **skärmen Kör** som anges att distributionen har startats. Förloppet för distributionen kan hittas  
    i fönstret **Rekommenderad modell** under **Distributionsstatus**.
    
När distributionen lyckas har du en fungerande webbtjänst för att generera förutsägelser. 

Gå vidare till [**nästa steg**](#next-steps) om du vill veta mer om hur du använder din nya webbtjänst och testa dina förutsägelser med hjälp av Power BI:s inbyggda Azure Machine Learning-support.

## <a name="clean-up-resources"></a>Rensa resurser

Distributionsfiler är större än data- och experimentfiler, så de kostar mer att lagra. Ta bara bort distributionsfilerna för att minimera kostnaderna för ditt konto, eller om du vill behålla arbetsytan och experimentfilerna. Annars tar du bort hela resursgruppen om du inte planerar att använda någon av filerna.  

### <a name="delete-the-deployment-instance"></a>Ta bort distributionsinstansen

Ta bara bort distributionsinstansen från Azure Machine Learning-studion, om du vill behålla resursgruppen och arbetsytan för andra självstudier och utforskning. 

1. Gå till [Azure Machine Learning-studion](https://ml.azure.com/). Navigera till arbetsytan och till vänster under **fönstret Tillgångar** väljer du **Slutpunkter**. 

1. Markera den distribution som du vill ta bort och välj **Ta bort**. 

1. Välj **Fortsätt**.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du automatiserad ML i Azure Machine Learning-studion för att skapa och distribuera en prognosmodell för tidsserier som förutsäger cykeldelningsuthyrningsbehov. 

I den här artikeln finns några steg om hur du skapar ett Power BI-stödschema som stöds för att underlätta förbrukningen av din nyligen distribuerade webbtjänst:

> [!div class="nextstepaction"]
> [Använda en webbtjänst](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Den här cykelresursdatauppsättningen har ändrats för den här självstudien. Denna datauppsättning gjordes tillgänglig som en del av en [Kaggle konkurrens](https://www.kaggle.com/c/bike-sharing-demand/data) och var ursprungligen tillgänglig via [Capital Bikeshare](https://www.capitalbikeshare.com/system-data). Det finns också i [UCI Machine Learning Database](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Källa: Fanaee-T, Hadi, och Gama, Joao, Event märkning som kombinerar ensembledetektorer och bakgrundskunskap, Framsteg inom artificiell intelligens (2013): sid. 1-15, Springer Berlin Heidelberg.
