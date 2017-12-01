---
title: "Steg 5: Distribuera webbtjänsten för Machine Learning | Microsoft Docs"
description: "Steg 5 i utveckla en förutsägelselösning genomgång: distribuera en prediktivt experiment i Machine Learning Studio som en webbtjänst."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: ba8f1678d87159088c58cf0e05e0fe5a6579b358
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Genomgång steg 5: Distribuera Azure Machine Learning-webbtjänsten
Det här är det femte steget i den här genomgången [utveckla en förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. **Distribuera webbtjänsten**
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
Om du vill ge andra användare möjlighet att använda förutsägelsemodell som vi har utvecklat i den här genomgången, kan vi distribuera den som en webbtjänst på Azure.

I det här läget har vi experimentera med vår modell. Men den distribuerade tjänsten inte längre kommer att göra utbildning - kommer att generera nya förutsägelser av bedömningen användarindata baserat på vår modell. Så är det dags att göra vissa förberedelser för att konvertera experimentet från en ***utbildning*** experimentera till en ***förutsägande*** experiment. 

Detta är en process i tre steg:  

1. Ta bort en av modeller
2. Omvandla den *träningsexperiment* har vi skapat till en *prediktivt experiment*
3. Distribuera prediktivt experiment som en webbtjänst

## <a name="remove-one-of-the-models"></a>Ta bort en av modeller

Vi måste först trim experimentet ned lite. Vi har två olika modeller i försöket, men vi vill använda en modell när vi distribuera den som en webbtjänst.  

Anta att vi har valt att ökat trädet modellen presterade bättre än SVM modellen. Så att det första du gör är att ta bort den [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulen och moduler som användes för att träna den. Du kanske vill göra en kopia av experimentet först genom att klicka på **Spara som** längst ned i arbetsytan för experimentet.

Vi måste du ta bort följande moduler:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine]
* [Träna modellen] [ train-model] och [Poängmodell] [ score-model] moduler som är anslutna till den
* [Normalisera Data] [ normalize-data] (båda)
* [Utvärdera modellen] [ evaluate-model] (eftersom vi är klar med att utvärdera modellerna)

Markera varje modul och tryck på DELETE eller högerklicka på modulen och välj **ta bort**. 

![Ta bort SVM modellen][3a]

Vår modell bör nu se ut ungefär så här:

![Ta bort SVM modellen][3]

Nu vi är redo att distribuera den här modellen med hjälp av den [två-Tvåklassförhöjt beslutsträd][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Omvandla utbildning experimentet till prediktivt experiment

För att förbereda den här modellen för distribution, måste vi du konvertera den här träningsexperiment till prediktivt experiment. Detta omfattar tre steg:

1. Spara modellen vi har tränat och Ersätt vår utbildningsmoduler
2. Trim experiment om du vill ta bort moduler som behövdes endast för utbildning
3. Definiera där webbtjänsten ska ta emot indata och där det genererar utdata

Vi kan göra detta manuellt, men som tur är alla tre stegen kan utföras genom att klicka på **konfigurera Web Service** längst ned i arbetsytan för experimentet (och välja den **förutsägande webbtjänsten** alternativet).

> [!TIP]
> Om du vill ha mer information om vad som händer när du konverterar en träningsexperiment till en prediktivt experiment, se [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

När du klickar på **konfigurera Web Service**, flera saker:

* Den tränade modellen konverteras till en enda **Trained Model** modulen och lagras på modulpaletten till vänster om arbetsytan för experimentet (du hittar den under **tränade modeller**)
* Moduler som användes för träning tas bort; specifikt:
  * [Two-Class Tvåklassförhöjda beslutsträdet][two-class-boosted-decision-tree]
  * [Träningsmodell][train-model]
  * [Dela Data][split]
  * andra [köra R-skriptet] [ execute-r-script] modul som användes för testdata
* Den sparade tränade modellen har lagts till i experimentet igen
* **Web service indata** och **Web service utdata** moduler läggs (dessa identifiera där användarens data kommer att ange modellen och vilka data som returneras när webbtjänsten används)

> [!NOTE]
> Du kan se att experimentet sparas i två delar under flikar som lagts till överst på arbetsytan för experimentet. Den ursprungliga träningsexperiment är under fliken **träningsexperiment**, och det nyligen skapade prediktivt experimentet är **Prediktivt experiment**. Prediktivt experiment är det som vi ska distribuera som en webbtjänst.

Vi behöver vidta ytterligare ett steg med viss experimentet.
Vi har lagt till två [köra R-skriptet] [ execute-r-script] moduler för att tillhandahålla en viktas funktion för data. Som var bara en lura vi behövs för träning och testning, så att vi kan ta reda på dessa moduler i den slutliga modellen.
Machine Learning Studio bort en [köra R-skriptet] [ execute-r-script] modulen när den tas bort i [dela] [ split] modul. Nu när vi kan ta bort det andra och ansluta [Metadata Editor] [ metadata-editor] direkt till [Poängmodell][score-model].    

Vår experimentet bör nu se ut så här:  

![Poängsättning av den tränade modellen][4]  

> [!NOTE]
> Du kanske undrar varför vi kvar UCI tyska kreditkortdata datauppsättningen i prediktivt experiment. Tjänsten kommer att poängsätta användarens data, inte den ursprungliga datauppsättningen, så varför lämnar den ursprungliga datauppsättningen i modellen?
> 
> Det gäller att tjänsten inte behöver den ursprungliga informationen för kreditkort. Men den behöver schemat för dessa data, som innehåller information, till exempel hur många kolumner finns och vilka kolumner som är numeriska. Den här schemainformation är nödvändigt att tolka användarens data. Vi lämnar komponenterna ansluten så att poängsättningsmodul har dataset-schema när tjänsten körs. Informationen används inte bara schemat.  
> 
> 

Kör experimentet en sista gång (klicka på **kör**.) Om du vill kontrollera att modellen fortfarande fungerar, klickar du på utdata från den [Poängmodell] [ score-model] modulen och välj **visa resultat**. Du kan se att ursprungliga data visas, tillsammans med kredit risk värdet (”poängsatta etiketter”) och bedömningsprofil sannolikhetsvärdet (”bedömas sannolikhet”.) 

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera experimentet som antingen en klassisk webbtjänst eller som en ny webbtjänst som baseras på Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Distribuera som ett klassiskt-webbtjänst
Om du vill distribuera en klassisk webbtjänst som härletts från våra experiment, klickar du på **distribuera webbtjänsten** under arbetsytan och välj **distribuera webbtjänsten [klassisk]**. Machine Learning Studio distribuerar experiment som en webbtjänst och går till instrumentpanelen för webbtjänsten. Från den här sidan kan du återgå till experimentet (**visa ögonblicksbild** eller **Visa senaste**) och kör ett enkelt test av webbtjänsten (se **testa webbtjänsten** nedan). Det finns även information här för att skapa program som har åtkomst till webbtjänsten (Mer information om det finns i nästa steg i den här genomgången).

![Web service-instrumentpanelen][6]

Du kan konfigurera tjänsten genom att klicka på den **CONFIGURATION** fliken. Här kan du ändra tjänstens namn (den har fått namnet experiment som standard) och en beskrivning. Du kan också ge mer egna etiketter för inkommande och utgående data.  

![Konfigurera webbtjänsten][5]  

### <a name="deploy-as-a-new-web-service"></a>Distribuera som en ny webbtjänst

> [!NOTE] 
> Du måste ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten för att distribuera en ny webbtjänst. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

Att distribuera en ny webbtjänst som härrör från våra experiment:

1. Klicka på **distribuera webbtjänsten** under arbetsytan och välj **distribuera webbtjänsten [ny]**. Machine Learning Studio överför du till Azure Machine Learning-webbtjänster **distribuera Experiment** sidan.

2. Ange ett namn för webbtjänsten. 

3. För **prisplan**, du kan välja en befintlig prisavtal eller välj ”Skapa nya” och namnge den nya planen och väljer alternativet månatliga plan. Planera nivåerna standard att planer för standardregion och webbtjänsten har distribuerats till den regionen.

4. Klicka på **distribuera**.

Efter några minuter på **Quickstart** öppnas sidan för webbtjänsten.

Du kan konfigurera tjänsten genom att klicka på den **konfigurera** fliken. Här kan du ändra tjänsten title och en beskrivning. 

Om du vill testa webbtjänsten klickar du på den **testa** fliken (se **testa webbtjänsten** nedan). Mer information om hur du skapar program som har åtkomst till webbtjänsten klickar du på **förbruka** fliken (nästa steg i den här genomgången hamnar i detalj).

> [!TIP]
> När du har distribuerat den, kan du uppdatera webbtjänsten. Till exempel om du vill ändra modellen, och sedan kan du redigera utbildning experiment, justera modellparametrarna och på **distribuera webbtjänsten**, välja **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [ny]**. När du distribuerar experimentet igen ersätter webbtjänsten nu och Använd uppdaterade modellen.  
> 
> 

## <a name="test-the-web-service"></a>Testa webbtjänsten

När webbtjänsten används anger användarens data via den **Web service indata** modulen där den skickas till den [Poängmodell] [ score-model] modulen och poängsätts. Hur vi har konfigurerat prediktivt experiment, modellen förväntar data i samma format som den ursprungliga kredit risk datauppsättningen.
Resultaten returneras till användaren från webbtjänsten via den **Web service utdata** modul.

> [!TIP]
> Hur vi har prediktivt experiment som konfigurerats för hela resultat av den [Poängmodell] [ score-model] modulen returneras. Detta omfattar alla indata plus värdet kredit risk och sannolikheten för bedömningsprofil. Men returnerar något annat om du vill, t.ex, du kan returnera bara kredit risk värde. Gör detta genom att infoga en [Projektkolumner] [ project-columns] modulen mellan [Poängmodell] [ score-model] och **Web service utdata** att ta bort kolumner som du inte vill webbtjänsten för att returnera. 
> 
> 

Du kan testa en klassisk web service antingen i **Machine Learning Studio** eller i den **Azure Machine Learning-webbtjänster** portal.
Du kan testa en ny webbplats tjänsten i den **Machine Learning-webbtjänster** portal.

> [!TIP]
> Testa i Azure Machine Learning-webbtjänster portal, kan du ha portal Skapa exempeldata som du kan använda för att testa tjänsten begäran och svar. På den **konfigurera** väljer ”Ja” för **exempel Data aktiverat?**. När du öppnar fliken begäran och svar på de **Test** sidan portalen fylls exempeldata från den ursprungliga kredit risk datauppsättningen.

### <a name="test-a-classic-web-service"></a>Testa en klassisk-webbtjänst

Du kan testa en klassisk webbtjänst i Machine Learning Studio eller i Machine Learning-webbtjänster-portalen. 

#### <a name="test-in-machine-learning-studio"></a>Testa i Machine Learning Studio

1. På den **INSTRUMENTPANELEN** för webbtjänsten, klickar du på den **Test** knappen **standard Endpoint**. En dialogruta visas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kredit risk datauppsättningen.  

2. Ange en uppsättning data och klicka sedan på **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testa i Machine Learning-webbtjänster portal

1. På den **INSTRUMENTPANELEN** för webbtjänsten, klickar du på den **Test preview** länken under **standard Endpoint**. Testsida i Azure Machine Learning-webbtjänster portal för webbtjänstslutpunkt öppnas och du uppmanas att ange indata för tjänsten. Det här är samma kolumner som fanns i den ursprungliga kredit risk datauppsättningen.

2. Klicka på **testa begäran och svar**. 

### <a name="test-a-new-web-service"></a>Testa en ny webbtjänst

Du kan testa en ny webbtjänst endast i Machine Learning-webbtjänster portal.

1. I den [Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) klickar du på **Test** överst på sidan. Den **Test** sidan öppnas och du kan ange data för tjänsten. Inmatningsfält visas motsvarar de kolumner som fanns i den ursprungliga kredit risk datauppsättningen. 

2. Ange en uppsättning data och klicka sedan på **Test-svar på begäranden**.

Resultatet av testet visas till höger på sidan i utdatakolumnen. 


## <a name="manage-the-web-service"></a>Hantera webbtjänsten

När du har distribuerat din webbtjänst om klassiska eller nya kan du hantera den från den [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) portal.

Att övervaka prestanda för webbtjänsten:

1. Logga in på den [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) portal
2. Klicka på **webbtjänster**
3. Klicka på din web service
4. Klicka på den **instrumentpanelen**

- - -
**Nästa: [få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)**

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
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
