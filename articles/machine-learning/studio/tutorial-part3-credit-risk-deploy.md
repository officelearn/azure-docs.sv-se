---
title: 'Självstudie 3: Distribuera en kreditriskmodell'
titleSuffix: Azure Machine Learning Studio
description: En detaljerad självstudie om hur du skapar en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning Studio. Självstudien är del tre i en självstudieserie i tre delar. Den visar hur du distribuerar en modell som en webbtjänst.
keywords: kreditrisk, lösning för förutsägelseanalys, riskbedömning, distribuera, webbtjänst
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6cdccd54546296c85864f1588b71109ed8b8f79f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734803"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>Självstudie 3: Distribuera en kreditriskmodell – Azure Machine Learning Studio

I den här självstudien ska vi titta närmare på hur du utvecklar en lösning för förutsägelseanalys. Du ska utveckla en enkel modell i Machine Learning Studio.  Därefter distribuerar du modellen som en Azure Machine Learning-webbtjänst.  Den här distribuerade modellen kan göra förutsägelser med nya data. Självstudien är **del tre i en självstudieserie i tre delar**.

Anta att du behöver förutsäga kreditrisken för en person baserat på den information som han eller hon fyller i på en kreditansökan.  

Kreditriskbedömning är ett komplext problem, men den här självstudien kommer att förenkla processen. Du använder det som ett exempel på hur du kan skapa en lösning för förutsägelseanalys med hjälp av Microsoft Azure Machine Learning Studio. Du använder Azure Machine Learning Studio och en Machine Learning-webbtjänst i den här lösningen. 

I den här självstudien i tre delar börjar du med offentligt tillgängliga kreditriskdata.  Därefter utvecklar du och tränar en förutsägelsemodell.  Slutligen ska du distribuera modellen som en webbtjänst.

I [del ett av självstudien](tutorial-part1-credit-risk.md) skapade du en Machine Learning Studio-arbetsyta, överförde data och skapade ett experiment.

I [del två av självstudien](tutorial-part2-credit-risk-train.md) fick du träna och utvärdera modeller.

I den här delen av självstudien ska du:

> [!div class="checklist"]
> * Förbereda för distribution
> * Distribuera webbtjänsten
> * Testa webbtjänsten
> * Hantera webbtjänsten
> * Få åtkomst till webbtjänsten

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför [del två av självstudien](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution
För att ge andra chansen att använda den förutsägande modell som du utvecklade i självstudien, måste du distribuera den som en webbtjänst i Azure.

Hittills har du experimenterat med att träna vår modell. Men den distribuerade tjänsten kommer inte längre att tränas – den kommer att generera nya förutsägelser genom bedömning av användarindata baserat på vår modell. Vi kommer att göra vissa förberedelser för att konvertera det här experimentet från ett ***tränings***experiment till ett ***förutsägelse***experiment. 

Att förbereda distributionen är en process i tre steg:  

1. Ta bort en av modellerna
1. Konvertera det *träningsexperiment* som du skapade till ett *förutsägelseexperiment*
1. Distribuera förutsägelseexperimentet som en webbtjänst

### <a name="remove-one-of-the-models"></a>Ta bort en av modellerna

Först behöver du minska experimentet lite. du har just nu två olika modeller i experimentet, men du ska bara använda en modell när du distribuerar det som en webbtjänst.  

Vi antar att du har bestämt dig för att trädmodellen presterade bättre än SVM-modellen. Så det första du ska göra är att ta bort modulen [Tvåklassig dator för vektorstöd][two-class-support-vector-machine] och de moduler som användes för att träna den. Du kan göra en kopia av experimentet först genom att klicka på **Spara som** längst ned på arbetsytan för experimentet.

du måste ta bort följande moduler:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine] (Tvåklassig dator för vektorstöd)
* Modulerna [Train Model][train-model] (Träningsmodell) och [Score Model][score-model] (Poängmodell) som var anslutna till den
* [Normalisera data][normalize-data] (båda två)
* [Evaluate Model][evaluate-model] (Utvärdera modellen) (eftersom vi är klara med modellutvärderingen)

Markera varje modul och tryck på Delete-tangenten, eller högerklicka på modulen och välj **Ta bort**. 

![Visar vilka moduler tas bort för att ta bort modellen dator för Vektorstöd](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Nu bör modellen se ut ungefär så här:

![Resulterande experiment när modellen dator för Vektorstöd tas bort](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nu är vi redo att distribuera modellen med hjälp av [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Tvåklassigt förbättrat beslutsträd).

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägelseexperiment

För att förbereda den här modellen för distribution, måste du konvertera träningsexperimentet till ett förutsägelseexperiment. Detta omfattar tre steg:

1. Spara modellen som du har tränat och ersätt sedan träningsmodulerna
1. Minska storleken på experimentet genom att ta bort moduler som endast behövdes till träningen
1. Definiera var webbtjänsten ska ta emot indata och var den genererar utdata

du kan göra detta manuellt om du vill, men som tur är kan alla tre stegen utföras genom att klicka på **Set Up Web Service** (Konfigurera webbtjänst) längst ned på arbetsytan för experimentet (och välja alternativet **Predictive Web Service** (Förutsägelsewebbtjänst)).

> [!TIP]
> Om du vill veta mer vad som händer när du konverterar ett träningsexperiment till ett förutsägelseexperiment, kan du läsa [Så här förbereder du din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

När du klickar på **Set Up Web Service** (Konfigurera webbtjänst) händer flera saker:

* Den tränade modellen konverteras till den enda modulen för **Trained Model** (Tränad modell) och lagras i modulpaletten till vänster om arbetsytan för experimentet (du hittar den under **Trained Models** (Tränade modeller))
* Moduler som har använts för träning tas bort, exempelvis:
  * [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Tvåklassigt förbättrat beslutsträd)
  * [Train Model][train-model] (Träningsmodell)
  * [Split Data][split] (Dela data)
  * den andra modulen [Kör R-skript][execute-r-script] som användes för testdata
* Den sparade tränade modellen har lagts tillbaka i experimentet
* Modulerna **Web service input** (Webbtjänstens indata) och **Web service output** (Webbtjänstens utdata) har lagts till (de identifierar var användarens data kommer in i modellen, vilka data som returneras och när webbtjänsten används)

> [!NOTE]
> Du kan se att experimentet sparas i två delar under flikar som lagts till överst i arbetsytan för experimentet. Det ursprungliga träningsexperimentet finns under fliken **Träningsexperiment** och det nyligen skapade förutsägelseexperiment finns i **Förutsägelseexperiment**. Du ska distribuera förutsägelseexperimentet som en webbtjänst.

du måste vidta ytterligare en åtgärd i det här experimentet.
du har lagt till två moduler för [Kör R-skript][execute-r-script] för att få en viktad funktion till datan. Det var bara något som användes i din träning och testning, så du kan ta bort dessa moduler i den slutliga modellen.
Machine Learning Studio tog bort en modul för [Kör R-skript][execute-r-script] när den tog bort modulen [Dela][split]. Nu kan du ta bort den andra och ansluta [Metadata Editor][metadata-editor] direkt till [Score Model][score-model] (Poängmodell).    

Vårt experiment bör nu se ut så här:  

![Bedömning av den tränade modellen](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Du kanske undrar varför du lämnade kvar datamängden UCI German Credit Card Data i förutsägelseexperimentet. Tjänsten kommer att bedöma användarens data, inte den ursprungliga datauppsättningen, så varför ska vi ha kvar den ursprungliga datauppsättningen i modellen?
> 
> Det stämmer att tjänsten inte behöver den ursprungliga kreditkortsinformationen. Men den behöver schemat för dessa data, där det exempelvis finns information om hur många kolumner som finns och vilka kolumner som är numeriska. Den här schemainformationen är nödvändig för att vi ska kunna tolka användarens data. du kan låta dessa komponenter vara anslutna så att bedömningsmodulen har datamängdsschemat när tjänsten körs. Datan används inte, bara schemat.  
> 
>Observera att om din ursprungliga datamängd innehöll etiketten, kommer det förväntade schemat från webbindatan också förväntas innehålla en kolumn med etiketten! Du kan undvika problemet genom att ta bort etiketten och andra data som finns i datamängden för träning men som inte ska finnas i webbindatan, innan du kopplar webbindatan och datamängden för träning till en gemensam modul. 
> 

Kör experimentet en sista gång (klicka på **Kör**.) Om du vill kontrollera att modellen fortfarande fungerar, klickar du på utdatan från modulen [Score Model][score-model] (Poängmodell) och väljer **Visa resultat**. Du kan se att ursprungliga data visas tillsammans med kreditriskvärdet (”Scored Labels” (Poängsatta etiketter)) och bedömningens sannolikhetsvärde (”Scored Probabilities” (Poängsatt sannolikhet).) 

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera experimentet som antingen en klassisk webbtjänst eller som en ny webbtjänst som baseras på Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuera som en klassisk webbtjänst
Om du vill distribuera en klassisk webbtjänst som härletts från vårt experiment, klickar du på **Distribuera webbtjänst** under arbetsytan och väljer **Distribuera webbtjänst [klassisk]**. Machine Learning Studio distribuerar experimentet som en webbtjänst och tar dig till instrumentpanelen för webbtjänsten. Från den här sidan kan du återgå till experimentet (**Visa ögonblicksbild** eller **Visa senaste**) och köra ett enkelt test av webbtjänsten (finns i **Testa webbtjänsten** nedan). Det finns även information här för att skapa program som har åtkomst till webbtjänsten (mer information finns i nästa steg i den här självstudien).

![Instrumentpanel för webbtjänsten](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Du kan konfigurera tjänsten genom att klicka på fliken **KONFIGURATION**. Här kan du ändra namnet på tjänsten (som har fått experimentnamnet som standard) och ange en beskrivning. Du kan också ange fler egna etiketter för inkommande och utgående data.  

![Konfigurera webbtjänsten](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Distribuera som en ny webbtjänst

> [!NOTE] 
> Du måste ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten till när du distribuerar en ny webbtjänst. Mer information finns i [Hantera en webbtjänst med hjälp av portalen för Azure Machine Learning-webbtjänster](manage-new-webservice.md). 

Distribuera en ny webbtjänst från vårt experiment:

1. Klicka på **Distribuera webbtjänst** under arbetsytan och välj **Distribuera webbtjänst [nytt]**. Machine Learning Studio överför dig till Azure Machine Learning-webbtjänsterna på sidan **Distribuera experiment**.

1. Ange ett namn på webbtjänsten. 

1. I **Prisplan** kan du välja en befintlig prisplan eller ”Skapa ny”, namnge den nya planen och välja alternativet för en månatlig plan. Planen får som standard nivåerna för planer i din standardregion och din webbtjänst distribueras till den regionen.

1. Klicka på **Distribuera**.

Efter några minuter öppnas sidan **Snabbstart** för din webbtjänst.

Du kan konfigurera tjänsten genom att klicka på fliken **Konfigurera**. Här kan du ändra tjänstens rubrik och ge den en beskrivning. 

Om du vill testa webbtjänsten klickar du på fliken **Testa** (se **Testa webbtjänsten** nedan). Om du vill ha mer information om hur du skapar program med åtkomst till webbtjänsten, klickar du på fliken **Förbruka** (nästa steg i den här självstudien beskriver detta mer detaljerat).

> [!TIP]
> Du kan uppdatera webbtjänsten efter att du har distribuerat den. Om du till exempel vill ändra modell kan du redigera träningsexperimentet, justera modellparametrarna och klicka på **Distribuera webbtjänst**, välja **Distribuera webbtjänst [klassisk]** eller **Distribuera webbtjänst [nytt]**. När du distribuerar experimentet igen ersätts webbtjänsten med den uppdaterade modellen.  
> 
> 

## <a name="test-the-web-service"></a>Testa webbtjänsten

När webbtjänsten används kommer användarens data in via modulen **Webbtjänstens indata** där den skickas till modulen [Poängmodell][score-model] och poängsätts. På det sätt som du har konfigurerat förutsägelseexperimentet förväntar sig modellen data i samma format som den ursprungliga kreditriskdatamängden.
Resultaten returneras till användaren från webbtjänsten via modulen **Webbtjänstens utdata**.

> [!TIP]
> På det sätt som förutsägelseexperimentet har konfigurerats, returneras hela resultatet från modulen [Poängmodell][score-model]. Detta inkluderar alla indata plus kreditriskvärdet och bedömningen av sannolikheten. Men du kan returnera något annat om du vill – du kan till exempel returnera enbart kreditriskvärdet. Gör detta genom att infoga en [Välj kolumner] [ select-columns] modulen mellan [Poängmodell] [ score-model] och **Web service utdata**att ta bort kolumner som du inte vill webbtjänsten ska returneras. 
> 
> 

Du kan testa en klassisk webbtjänst antingen i **Machine Learning Studio** eller i portalen för **Azure Machine Learning-webbtjänster**.
Du kan enbart testa en ny webbtjänst i portalen för **Machine Learning-webbtjänster**.

> [!TIP]
> När du testar i Azure Machine Learning-portalen för webbtjänster kan du låta portalen skapa exempeldata som du kan använda för att testa tjänsten med begäran–svar. På sidan **Konfigurera** väljer du ”Ja” i **Sample Data Enabled** (Aktivera exempeldata?). När du öppnar fliken Begäran–svar på sidan **Test**, fyller portalen i exempeldata som hämtas från den ursprungliga kreditriskdatamängden.

### <a name="test-a-classic-web-service"></a>Testa en klassisk webbtjänst

Du kan testa en klassisk webbtjänst i Machine Learning Studio eller i portalen för Machine Learning-webbtjänster. 

#### <a name="test-in-machine-learning-studio"></a>Testa i Machine Learning Studio

1. På sidan **INSTRUMENTPANEL** för webbtjänsten klickar du på knappen **Test** under **Standardslutpunkt**. En dialogruta visas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kreditriskdatamängden.  

1. Ange en uppsättning data och klicka sedan på **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testa i portalen för Machine Learning-webbtjänster

1. På sidan **INSTRUMENTPANEL** i webbtjänsten klickar du på länken **Testa förhandsversion** under **Standardslutpunkt**. Testsidan i portalen för Azure Machine Learning-webbtjänster för webbtjänstens slutpunkt öppnas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kreditriskdatamängden.

2. Klicka på **Testa begäran–svar**. 

### <a name="test-a-new-web-service"></a>Testa en ny webbtjänst

Du kan enbart testa en ny webbtjänst i portalen för Machine Learning-webbtjänster.

1. I portalen för [Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) klickar du på **Test** överst på sidan. Sidan **Test** öppnas och du kan ange data för tjänsten. Indatafälten som visas motsvarar de kolumner som visades i den ursprungliga kreditriskdatamängden. 

1. Ange en uppsättning med data och klicka sedan på **Testa begäran–svar**.

Resultatet av testet visas till höger på sidan i utdatakolumnen. 


## <a name="manage-the-web-service"></a>Hantera webbtjänsten

När du har distribuerat din webbtjänst (klassisk eller ny) kan du hantera den från portalen för [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart).

Övervaka webbtjänstens prestanda:

1. Logga in på portalen för [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart)
1. Klicka på **Webbtjänster**
1. Klicka på din webbtjänst
1. Klicka på **Instrumentpanel**

## <a name="access-the-web-service"></a>Få åtkomst till webbtjänsten

Du har distribuerat en webbtjänst som använder din kreditriskförutsägelsemodell i föregående steg i den här självstudien. Användarna kan nu skicka data till den och ta emot resultaten. 

Webbtjänsten är en Azure-webbtjänst som kan ta emot och returnera data med hjälp av REST API:er på ett av följande två sätt:  

* **Begäran/svar** – Användaren skickar en eller flera rader med kreditdata till tjänsten med hjälp av ett HTTP-protokoll och tjänsten svarar med en eller flera resultatuppsättningar.
* **Batchkörning** – Användaren lagrar en eller flera rader med kreditdata i en Azure-blob och skickar sedan blobplatsen till tjänsten. Tjänsten bedömer alla rader med data i indatabloben, lagrar resultatet i en annan blob och returnerar URL:en för containern.  

Det snabbaste och enklaste sättet att få åtkomst till en klassisk webbtjänst är via [Azure ML Request-Response Service Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) eller [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Dessa webbappmallar kan skapa en anpassad webbapp som känner till din webbtjänsts indata och vad den ska returnera. Allt du behöver göra är att ge åtkomst till din webbtjänst och dina data så sköter mallen resten.

Mer information om hur du använder webbappmallar finns i [Använda en Azure Machine Learning-webbtjänst med en webbappmall](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört dessa steg:

> [!div class="checklist"]
> * Förbereda för distribution
> * Distribuera webbtjänsten
> * Testa webbtjänsten
> * Hantera webbtjänsten
> * Få åtkomst till webbtjänsten

Du kan också skapa ett anpassat program som får åtkomst till webbtjänsten med hjälp av startkod som finns i programmeringsspråken R, C# och Python.

> [!div class="nextstepaction"]
> [Använda en Azure Machine Learning-webbtjänst](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
