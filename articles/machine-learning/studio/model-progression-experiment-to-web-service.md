---
title: Hur en Azure Machine Learning-modell blir en webbtjänst | Microsoft Docs
description: En översikt över hur din Azure Machine Learning model utvecklas från en utveckling experimentera till en produktionsslutpunkt webbtjänst säkerhetsnivån.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 4045b677b99eaa47e80f0a04ebf7f478eb6229d6
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265193"
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Hur en Machine Learning-modell övergår från ett experiment till en produktionsslutpunkt webbtjänst
Azure Machine Learning Studio tillhandahåller en interaktiv arbetsyta där du kan utveckla, köra, testa och iterera ett ***experimentera*** som representerar en prediktiv analysmodell. Det finns en mängd olika moduler som kan:

* Indata i experimentet
* Ändra data
* Träna en modell med hjälp av maskininlärningsalgoritmer
* Poängsätt modellen
* Utvärdera resultaten
* Slutvärdena för utdata

När du är nöjd med ditt experiment kan du distribuera den som en ***klassiska Azure Machine Learning-webbtjänster*** eller en ***nya Azure Machine Learning-webbtjänster*** så att användarna kan skicka nya data och få tillbaka resultat.

Vi ger en översikt över hur din modell utvecklas i Machine Learning från en utveckling experimentera till en produktionsslutpunkt webbtjänst säkerhetsnivån i den här artikeln.

> [!NOTE]
> Det finns andra sätt att utveckla och distribuera machine learning-modeller, men den här artikeln fokuserar på hur du använder Machine Learning Studio. Till exempel en beskrivning av hur du skapar en klassiska webbtjänst förutsägande med R finns i blogginlägget [bygga och distribuera förutsägande Web Apps med hjälp av RStudio och Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Azure Machine Learning Studio är utformat för att hjälpa dig att utveckla och distribuera en *prediktiv analysmodell*, det är möjligt att du använder Studio för att utveckla ett experiment som inte innehåller en prediktiv analysmodell. Ett experiment kan till exempel bara indata, ändra dem och sedan matar ut resultaten. Du kan distribuera den här icke-förutsägelseexperiment som en webbtjänst precis som ett experiment med förutsägande analys, men det är en enklare process eftersom experimentet inte utbildning eller bedömning en maskininlärningsmodell. Det är inte en typisk du använder Studio på det här sättet, kommer vi inkludera den i diskussionen så att vi kan ge en fullständig förklaring av hur Studio fungerar.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Utveckla och distribuera en förutsägbar webbtjänst
Här följer stegen som en typisk lösning följer du utvecklar och distribuerar den med hjälp av Machine Learning Studio:

![Distributionsflödet](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Bild 1 – faser i en typisk prediktiv analysmodell*

### <a name="the-training-experiment"></a>Träningsexperimentet
Den ***träningsexperiment*** är den första fasen i att utveckla webbtjänsten i Machine Learning Studio. Syftet med träningsexperimentet är att ge dig en plats för att utveckla, testa, iterera och så småningom träna en maskininlärningsmodell. Du kan även skapa flera modeller samtidigt som du leta efter den bästa lösningen, men när du är klar experimentera du väljer en enda tränas modellera och eliminera resten från experimentet. Ett exempel i att utveckla en förutsägande analys experiment finns i [utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Förutsägbart experiment
När du har en tränad modell i experimentet utbildning, klickar du på **konfigurera Web Service** och välj **förutsägande webbtjänsten** i Machine Learning Studio för att starta processen för att konvertera utbildning Experimentera till en ***förutsägelseexperiment***. Syftet med förutsägelseexperiment är att använda den tränade modellen för att rangordna nya data, med målet att så småningom blir operationaliserat som Azure-webbtjänst.

Den här konverteringen görs för dig att göra följande:

* Konvertera en uppsättning moduler som används för träning i en enda modul och spara den som en träningsmodell
* Eliminera alla överflödig moduler som inte rör bedömning
* Lägg till inkommande och utgående portar som ska användas av eventuell Web-tjänsten

Det kan finnas fler ändringar som du vill göra att dina förutsägelseexperiment blir redo att distribuera som en webbtjänst. Om du vill att webbtjänsten för att mata ut endast en delmängd av resultaten kan du till exempel lägga till en modul som filtrerande innan på utdataporten.

I den här konverteringen träningsexperimentet inte tas bort. När processen är klar har du två flikar i Studio: en för träningsexperimentet och en för förutsägbart experiment. Det här sättet kan du ändra träningsexperimentet innan du distribuerar webbtjänsten och återskapa förutsägbart experiment. Eller du kan spara en kopia av träningsexperiment att starta en annan rad med experimentering.

> [!NOTE]
> När du klickar på **förutsägande webbtjänsten** du startar en automatisk process för att konvertera din träningsexperiment till ett förutsägelseexperiment och detta fungerar bra i de flesta fall. Om din träningsexperiment är komplex (exempel: du har flera sökvägar för utbildning som du ansluter till tillsammans), kanske du föredrar att göra den här konverteringen manuellt. Mer information finns i [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>Webbtjänsten
När du är nöjd att din förutsägelseexperiment är klar, kan du distribuera din tjänst som antingen en klassisk webbtjänst eller en ny webbtjänst baserat på Azure Resource Manager. Att driftsätta modellen genom att distribuera den som en *klassiska Machine Learning-webbtjänster*, klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]**. Distribuera som *nya Machine Learning-webbtjänster*, klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [nyhet]**. Användare kan nu skicka data till din modell med hjälp av webbtjänsten REST API och få tillbaka resultaten. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Icke-vanliga fallet: skapa en icke-predictive webbtjänst
Om experimentet inte träna en prediktiv analysmodell och du behöver inte skapa både ett träningsexperiment och en arbetsflödesbaserad experiment – det är bara ett experiment och du kan distribuera den som en webbtjänst. Machine Learning Studio identifierar om experimentet innehåller en förutsägbar modell genom att analysera de moduler som du har använt.

När du har resultatuppsättningen kan upprepas på experimentet och är nöjd med den:

1. Klicka på **konfigurera Web Service** och välj **Retraining webbtjänsten** – indata och utdata noder läggs till automatiskt
2. Klicka på **kör**
3. Klicka på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [nyhet]** beroende på miljön som du vill distribuera.

Din webbtjänst har nu distribuerats och du kan komma åt och hanterar det precis som en förutsägbar webbtjänst.

## <a name="updating-your-web-service"></a>Uppdatera din webbtjänst
Nu när du har distribuerat ditt experiment som en webbtjänst, vad händer om du behöver uppdatera det?

Det beror på vad du behöver uppdatera:

**Du vill ändra indata eller utdata eller du vill ändra hur webbtjänsten manipulerar data**

Om du inte ändrar modellen, men bara ändrar hur webbtjänsten hanterar data, du kan redigera förutsägbart experiment och klicka sedan på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [nyhet]** igen. Webbtjänsten har stoppats, uppdaterade förutsägelseexperiment har distribuerats och webbtjänsten har startats om.

Här är ett exempel: Anta att dina förutsägelseexperiment Returnerar hela raden indata med förväntade resultatet. Du kan välja som du vill att webbtjänsten för att bara returnera resultatet. Så du kan lägga till en **Projektkolumner** modul i förutsägelseexperiment, precis före utdataporten att utesluta kolumner utom resultatet. När du klickar på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [nyhet]** igen, webbtjänsten har uppdaterats.

**Du vill att träna modellen med nya data**

Om du vill behålla dina machine learning-modell, men du vill att träna med nya data, har du två alternativ:

1. **Träna modellen medan webbtjänsten körs** -om du vill att träna din modell medan förutsägande webbtjänsten körs, kan du göra detta genom att göra några ändringar träningsexperiment så att de blir en ***retraining Experimentera***, och du kan distribuera den som en  ***omtränings web* service**. Anvisningar för hur du gör detta finns i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).
2. **Gå tillbaka till det ursprungliga träningsexperimentet och använda olika utbildningsdata för att utveckla din modell** – din förutsägelseexperiment är länkad till webbtjänsten, men träningsexperimentet länkas inte direkt på det här sättet. Om du ändrar det ursprungliga träningsexperimentet och klicka på **konfigurera Web Service**, skapas en *nya* förutsägande experimentera som, när de distribueras, skapar en *nya* Web tjänsten. Uppdaterar den inte bara den ursprungliga webbtjänsten.
   
   Om du vill ändra träningsexperimentet kan öppna den och klickar på **Spara som** att göra en kopia. Detta lämnar intakta ursprungliga träningsexperimentet, förutsägbart experiment och webbtjänsten. Du kan nu skapa en ny webbtjänst med ändringarna. När du har distribuerat den nya webbtjänsten som sedan kan du bestämma om du vill stoppa tidigare webbtjänsten eller att det ska fungera tillsammans med den nya servern.

**Du vill skapa en annan modell**

Om du vill göra ändringar i din ursprungliga förutsägelseexperiment, till exempel att välja en annan maskininlärningsalgoritmen, skulle en annan utbildning metoden osv., måste du följa den andra proceduren som beskrivs ovan för att träna din modell: öppna den utbildning experiment, klickar du på **Spara som** att göra en kopia och starta sedan ned den nya sökvägen för att utveckla din modell, skapa förutsägbart experiment och distribuera webbtjänsten. Detta skapar en ny webbplats som tjänsten inte är relaterade till det ursprungliga – du kan bestämma vilken som, eller båda, kan fortsätta att köras.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar och experiment finns i följande artiklar:

* konvertering av experimentet - [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* distribuera webbtjänsten - [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md)
* Träna modellen - [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md)

Exempel på hela processen, se:

* [Självstudie om maskininlärning: skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md)
* [Genomgång: Utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

