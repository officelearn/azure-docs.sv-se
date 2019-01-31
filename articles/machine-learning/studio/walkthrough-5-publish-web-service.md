---
title: 'Steg 5: Distribuera webbtjänst - Azure Machine Learning Studio | Microsoft Docs'
description: 'Steg 5 i utveckla en förutsägelselösning genomgång: Distribuera ett förutsägbart experiment i Machine Learning Studio som en webbtjänst.'
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 9d55b7ebf78e076714f4e937ffa1de896b2ec6e8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474606"
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-studio-web-service"></a>Genomgång steg 5: Distribuera webbtjänsten Azure Machine Learning Studio
Detta är det femte steget av den här genomgången [utveckla en lösning för förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. **Distribuera webbtjänsten**
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
För att ge andra prova att använda förutsägande modell i den här genomgången har vi utvecklat, kan vi distribuera den som en webbtjänst på Azure.

Hittills har vi experimenterat med utbildning vår modell. Men den distribuerade tjänsten inte längre kommer att göra utbildning – det kommer att generera nya förutsägelser genom bedömning användarindata baserat på vår modell. Vi kommer att göra vissa förberedelser för att konvertera det här experimentet från en ***utbildning*** experimentera till en ***förutsägande*** experimentera. 

Det här är en process i tre steg:  

1. Ta bort en av modeller
2. Konvertera den *träningsexperiment* har vi skapat i en *förutsägbart experiment*
3. Distribuera förutsägelseexperiment som en webbtjänst

## <a name="remove-one-of-the-models"></a>Ta bort en av modeller

Vi måste först att trimma det här experimentet ned lite. Vi har för närvarande två olika modeller i experimentet, men vi vill bara använder en modell när vi distribuera den som en webbtjänst.  

Anta att vi har bestämt dig för att bättre trädet modellen presterade bättre än SVM modellen. Så det första du ska göra är att ta bort den [två-Class Support Vector Machine] [ two-class-support-vector-machine] modulen och de moduler som användes för att träna den. Du kanske vill göra en kopia av experimentet först genom att klicka på **Spara som** längst ned på arbetsytan för experimentet.

Vi måste du ta bort följande moduler:  

* [Två-Class Support Vector Machine][two-class-support-vector-machine]
* [Träna modellen] [ train-model] och [Poängmodell] [ score-model] moduler som var anslutna till den
* [Normalisera Data] [ normalize-data] (både av dem)
* [Utvärdera modellen] [ evaluate-model] (eftersom vi är klar i modellutvärdering)

Välj varje modul och tryck på DELETE eller högerklickar du på modulen och välj **ta bort**. 

![Ta bort SVM modellen][3a]

Vår modell bör nu se ut ungefär så här:

![Ta bort SVM modellen][3]

Nu vi är redo att distribuera den här modellen med hjälp av den [Tvåklassförhöjt beslutsträd][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägbart experiment

Vi måste konvertera den här träningsexperiment till ett förutsägbart experiment för att få den här modellen klar för distribution. Detta omfattar tre steg:

1. Spara modellen som vi har tränat och Ersätt sedan vår utbildningsmoduler
2. Trimma experimentet för att ta bort moduler som behövdes endast för utbildning
3. Definiera där webbtjänsten ska ta emot indata och där det genererar utdata

Vi kan göra detta manuellt, men som tur är alla tre stegen kan utföras genom att klicka på **konfigurera Web Service** längst ned på arbetsytan för experimentet (och välja den **förutsägande webbtjänsten** alternativet).

> [!TIP]
> Om du vill veta mer på vad som händer när du konvertera ett träningsexperiment till ett förutsägbart experiment, se [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

När du klickar på **konfigurera Web Service**, flera saker:

* Den tränade modellen konverteras till en enda **Trained Model** modulen och lagras på modulpaletten till vänster om arbetsytan för experimentet (du hittar den under **tränade modeller**)
* Moduler som har använts för utbildning tas bort; mer specifikt:
  * [Tvåklassförhöjt beslutsträd][two-class-boosted-decision-tree]
  * [Träna modell][train-model]
  * [Dela Data][split]
  * andra [kör R-skript] [ execute-r-script] modul som användes för testdata
* Den sparade tränade modellen har lagts till i experimentet
* **Web service indata** och **Web service utdata** moduler har lagts till (dessa identifierar där användarens data kommer att ange modellen och vilka data som returneras när webbtjänsten används)

> [!NOTE]
> Du kan se att experimentet sparas i två delar under flikar som lagts till överst i arbetsytan för experimentet. Det ursprungliga träningsexperimentet är under fliken **träningsexperiment**, och den nyligen skapade förutsägelseexperiment är **förutsägelseexperiment**. Förutsägbart experiment är det vi ska distribuera som en webbtjänst.

Vi behöver göra ytterligare ett steg med det här viss experimentet.
Vi har lagt till två [kör R-skript] [ execute-r-script] moduler att tillhandahålla en viktas funktion till data. Det var bara en knep som vi behövde för träning och testning, så vi kan ta reda på dessa moduler i den slutliga modellen.
Machine Learning Studio bort en [kör R-skript] [ execute-r-script] modulen när den tas bort den [dela] [ split] modulen. Nu kan vi ta bort den andra och Anslut [Metadata redigeraren] [ metadata-editor] direkt till [Poängmodell][score-model].    

Vår experimentet bör se ut så här:  

![Bedömning av den tränade modellen][4]  

> [!NOTE]
> Du kanske undrar varför vi lämnade kvar UCI tyska kreditkortsdata datauppsättningen i förutsägbart experiment. Tjänsten kommer att bedöma användarens data, inte den ursprungliga datauppsättningen, så vi varför den ursprungliga datauppsättningen i modellen?
> 
> Det gäller att tjänsten inte behöver den ursprungliga informationen för kreditkort. Men det måste schemat för dessa data, som innehåller information, till exempel hur många kolumner finns och vilka kolumner är numeriska. Den här schemainformationen är nödvändigt att tolka användarens data. Vi lämnar dessa komponenter som är anslutna så att poängsättningsmodul har schemat för datauppsättningen när tjänsten körs. Informationen används inte bara schemat.  
> 
>En viktig sak att tänka på är att om din ursprungliga datauppsättningen finns etiketten, sedan det förväntade schemat från web-indata också förväntas innehålla en kolumn med etiketten! Ett sätt undvika det här problemet är att ta bort etiketten och andra data som fanns i datamängd för träning, men inte i indata för webb, innan du ansluter web indata och datamängd för träning i en gemensam modul. 
> 

Kör experimentet en sista gång (klicka på **kör**.) Om du vill kontrollera att modellen fortfarande fungerar, klicka på utdata från den [Poängmodell] [ score-model] modul och välj **visa resultat**. Du kan se att den ursprungliga informationen visas tillsammans med risk-Kreditvärde (”poängsatta etiketter”) och bedömnings sannolikhetsvärde (”poängsätts sannolikhet”.) 

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera experimentet som antingen en klassisk webbtjänst eller som en ny webbtjänst som baseras på Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuera som en klassisk webbtjänst
Om du vill distribuera en klassisk webbtjänst som härletts från våra experiment, klickar du på **distribuera webbtjänsten** under arbetsytan och välj **distribuera webbtjänsten [klassisk]**. Machine Learning Studio distribuerar experiment som en webbtjänst och går till instrumentpanelen för webbtjänsten. Från den här sidan som du kan återgå till arbetsytan för experimentet (**visa ögonblicksbild** eller **Visa senaste**) och kör ett enkelt test av webbtjänsten (finns i **testa webbtjänsten** nedan). Det finns även information här för att skapa program som har åtkomst till webbtjänsten (Mer information om det finns i nästa steg i den här genomgången).

![Instrumentpanelen för webbtjänsten][6]

Du kan konfigurera tjänsten genom att klicka på den **CONFIGURATION** fliken. Här kan du ändra namnet på tjänsten (nu har fått experimentnamnet som standard) och en beskrivning. Du kan också ge mer egna etiketter för inkommande och utgående data.  

![Konfigurera webbtjänsten][5]  

### <a name="deploy-as-a-new-web-service"></a>Distribuera som en ny webbtjänst

> [!NOTE] 
> Du måste ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten för att distribuera en ny webbtjänst. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

Distribuera en ny webbtjänst som härrör från våra experiment:

1. Klicka på **distribuera webbtjänsten** under arbetsytan och välj **distribuera webbtjänsten [nyhet]**. Machine Learning Studio överför du till Azure Machine Learning-webbtjänster **distribuera Experiment** sidan.

2. Ange ett namn för webbtjänsten. 

3. För **prisplan**, du kan välja en befintlig prisavtal eller välj ”Skapa ny” och namnge den nya planen och välja alternativet månatlig plan. Plan för nivåerna standard för planer för din standardregion och din webbtjänst distribueras till den regionen.

4. Klicka på **distribuera**.

Efter några minuter, den **snabbstarten** för webbtjänsten öppnas.

Du kan konfigurera tjänsten genom att klicka på den **konfigurera** fliken. Här kan du ändra tjänsten rubrik och en beskrivning. 

Om du vill testa webbtjänsten klickar du på den **testa** fliken (se **testa webbtjänsten** nedan). Mer information om hur du skapar program som kan få åtkomst till webbtjänsten klickar du på **förbruka** fliken (nästa steg i den här genomgången kommer gå in i detalj).

> [!TIP]
> När du har distribuerat den kan du uppdatera webbtjänsten. Till exempel om du vill ändra din modell, och du kan redigera träningsexperimentet, justera parametrarna modell och klickar på **distribuera webbtjänsten**, välja **distribuera webbtjänsten [klassisk]** eller **Distribuera webbtjänst [nyhet]**. När du distribuerar experimentet igen ersätter webbtjänsten nu med uppdaterade modellen.  
> 
> 

## <a name="test-the-web-service"></a>Testa webbtjänsten

När webbtjänsten används användarens data kommer in via den **Web service indata** modulen där den skickas till den [Poängmodell] [ score-model] modulen och poängsätts. På sätt som vi har konfigurerat förutsägbart experiment, modellen förväntas data i samma format som den ursprungliga kredit risk datauppsättningen.
Resultaten returneras till användaren från webbtjänsten via den **Web service utdata** modulen.

> [!TIP]
> Hur vi har förutsägelseexperiment som har konfigurerats, hela resultat av den [Poängmodell] [ score-model] modulen returneras. Detta inkluderar alla indata plus Kreditvärde för risk och bedömnings sannolikheten. Men du kan returnera något annat om du vill – du kan till exempel returnera bara Kreditvärde för risk. Gör detta genom att infoga en [Projektkolumner] [ project-columns] modulen mellan [Poängmodell] [ score-model] och **Web service utdata**att ta bort kolumner som du inte vill webbtjänsten ska returneras. 
> 
> 

Du kan testa en klassisk web service antingen i **Machine Learning Studio** eller i den **Azure Machine Learning Web Services** portal.
Du kan testa en ny webbplats tjänsten endast på den **Machine Learning Web Services** portal.

> [!TIP]
> När du testar i Azure Machine Learning Web Services-portalen kan ha du portalen Skapa exempeldata som du kan använda för att testa tjänsten begäranden och svar. På den **konfigurera** väljer ”Ja” för **Sample Data aktiverat?**. När du öppnar fliken begäranden och svar på den **Test** , portalen fyller i exempeldata som kommer från den ursprungliga kredit risk datauppsättningen.

### <a name="test-a-classic-web-service"></a>Testa en klassisk webbtjänst

Du kan testa en klassisk webbtjänst i Machine Learning Studio eller i Machine Learning Web Services-portalen. 

#### <a name="test-in-machine-learning-studio"></a>Testa i Machine Learning Studio

1. På den **INSTRUMENTPANELEN** för webbtjänsten och klicka på den **Test** knappen **standard-slutpunkt**. En dialogruta visas, och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kredit risk datauppsättningen.  

2. Ange en uppsättning data och klicka sedan på **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testa i Machine Learning Web Services-portalen

1. På den **INSTRUMENTPANELEN** för webbtjänsten och klicka på den **Test förhandsversion** länka **standard-slutpunkt**. Testsida i Azure Machine Learning Web Services-portalen för slutpunkten för webbtjänsten öppnas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kredit risk datauppsättningen.

2. Klicka på **testa Request-Response**. 

### <a name="test-a-new-web-service"></a>Testa en ny webbtjänst

Du kan testa en ny webbtjänst endast i Machine Learning Web Services-portalen.

1. I den [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) klickar du på **Test** överst på sidan. Den **Test** öppnas och du kan ange data för tjänsten. Indatafält visas motsvarar de kolumner som visas i den ursprungliga kredit risk datauppsättningen. 

2. Ange en uppsättning data och klicka sedan på **Test Request-Response**.

Resultatet av testet visas till höger på sidan i utdatakolumnen. 


## <a name="manage-the-web-service"></a>Hantera webbtjänsten

När du har distribuerat din webbtjänst om klassisk eller nya kan du hantera den från den [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portal.

Övervaka prestanda för din webbtjänst:

1. Logga in på den [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portal
2. Klicka på **webbtjänster**
3. Klicka på din webbtjänst
4. Klicka på den **instrumentpanel**

- - -
**Nästa: [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


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
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
