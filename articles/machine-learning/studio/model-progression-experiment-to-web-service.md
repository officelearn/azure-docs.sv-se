---
title: Hur en Azure Machine Learning-modell blir en webbtjänst | Microsoft Docs
description: En översikt över hur din Azure Machine Learning modellen fortlöper från en utveckling experimentera till en operationalized webbtjänst säkerhetsnivån.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
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
ms.openlocfilehash: 7b9aec2815f836b3b220de37fe6428c54d39c3e5
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835580"
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Hur en Machine Learning-modell övergår från ett experiment i en operationalized-webbtjänst
Azure Machine Learning Studio tillhandahåller en interaktiv arbetsyta där du kan utveckla, köra, testa och iterera ett ***experimentera*** som representerar en prediktiv analysmodell. Det finns en mängd olika moduler som kan:

* Indata i experimentet
* Manipulera data
* Träna en modell med hjälp av maskininlärningsalgoritmer
* Poängsätt modellen
* Utvärdera resultaten
* Sista utdatavärden

När du är nöjd med ditt experiment kan du distribuera den som en ***klassiska Azure Machine Learning-webbtjänst*** eller en ***nya Azure Machine Learning-webbtjänst*** så att användarna kan skicka det nya data och få tillbaka resultat.

Vi ger en översikt över hur din modell fortlöper i Machine Learning från en utveckling experimentera till en operationalized webbtjänst säkerhetsnivån i den här artikeln.

> [!NOTE]
> Det finns andra sätt att utveckla och distribuera machine learning-modeller, men den här artikeln fokuserar på hur du använder Machine Learning Studio. Till exempel en beskrivning av hur du skapar en klassiska förutsägande webbtjänst med R finns i bloggposten [Build & Distribuera förutsägande Web Apps med RStudio och Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Azure Machine Learning Studio är utformat för att hjälpa dig att utveckla och distribuera en *prediktiv analysmodell*, det är möjligt att använda Studio för att utveckla ett experiment som inte innehåller en prediktiv analysmodell. Ett experiment kan till exempel bara inkommande data, ändra dem och skriver resultatet. Du kan distribuera den här icke-prediktivt experiment som en webbtjänst precis som en förutsägbar analys experiment, men det är en enklare process eftersom experimentet inte utbildning eller poängsättning av en modell för maskininlärning. När den inte är det vanligt att använda Studio på det här sättet kommer vi inkludera den i diskussionen så att vi kan ge en fullständig förklaring av hur Studio fungerar.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Utveckla och distribuera en prediktiv webbtjänst
Här följer de steg som en typisk lösning följer du utvecklar och distribuerar den med hjälp av Machine Learning Studio:

![Distributionsflödet](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Bild 1 - led i en typisk prediktiv analysmodell*

### <a name="the-training-experiment"></a>Utbildning experimentet
Den ***träningsexperiment*** den första fasen i utveckla webbtjänsten i Machine Learning Studio. Syftet med träningsexperiment är att ge dig en plats för att utveckla, testa, iterera och slutligen träna machine learning-modellen. Du kan även träna flera modeller samtidigt som du letar efter den bästa lösningen, men när du är klar experimentera du väljer en enda tränats modell och eliminera resten från experimentet. Ett exempel för att utveckla ett experiment förutsägbar analys finns [utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Prediktivt experiment
När du har en tränad modell i experimentet utbildning klickar du på **konfigurera Web Service** och välj **förutsägande webbtjänsten** i Machine Learning Studio för att starta en process där experimentet utbildning till en ***prediktivt experiment***. Syftet med prediktivt experiment är att använda din tränad modell för att samla in nya data, med målet att så småningom blir operationalized som Azure-webbtjänst.

Den här konverteringen görs av du igenom följande steg:

* Omvandla uppsättningen moduler som används för träning i en enda modul och spara den som en tränad modell
* Ta bort överflödig moduler som inte rör bedömningen
* Lägg till inkommande och utgående portarna som eventuell webbtjänsten ska använda

Det kan finnas fler ändringar som du vill göra att hämta experimentet förutsägande klar att distribueras som en webbtjänst. Till exempel om du vill webbtjänsten till utdata endast en delmängd av resultat, du kan lägga till en filtrering modul innan på utdataporten.

I den här konverteringen utbildning experimentet inte tas bort. När processen är klar har du två flikar i Studio: en för experimentet utbildning och en för prediktivt experiment. Det här sättet kan du ändra experimentet utbildning innan du distribuerar webbtjänsten och återskapa prediktivt experiment. Eller spara en kopia av utbildning försöket att starta ytterligare en rad i experiment.

> [!NOTE]
> När du klickar på **förutsägande webbtjänsten** du startar en automatisk process för att konvertera experimentet utbildning till prediktivt experiment, och det fungerar bra i de flesta fall. Om din träningsexperiment är komplex (t.ex, du har flera sökvägar för träning som du ansluter tillsammans), kanske du föredrar att göra den här konverteringen manuellt. Mer information finns i [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>Webbtjänsten
När du är nöjd att experimentet förutsägande är klar, kan du distribuera din tjänst som antingen en klassiska webbtjänst eller en ny webbtjänst baserat på Azure Resource Manager. Att driftsätta modellen genom att distribuera den som en *klassiska Machine Learning-webbtjänst*, klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]**. Att distribuera som *nya Machine Learning-webbtjänst*, klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [ny]**. Användare kan nu skicka data till din modell med hjälp av webbtjänsten REST-API och få tillbaka resultatet. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Icke-vanligast: skapa en icke-förutsägande webbtjänst
Om experimentet inte tränar en prediktiv analysmodell och du behöver inte skapa både en träningsexperiment och ett bedömningsprofil experiment – det finns bara ett experiment och du kan distribuera den som en webbtjänst. Machine Learning Studio identifierar om experimentet innehåller en förutsägelsemodell genom att analysera de moduler som du har använt.

När du har hävdade på experimentet och du är nöjd med den:

1. Klicka på **konfigurera Web Service** och välj **Omtränings webbtjänsten** - indata och utdata noder läggs till automatiskt
2. Klicka på **kör**
3. Klicka på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [ny]** beroende på miljö som du vill distribuera.

Webbtjänsten nu har distribuerats och du kan komma åt och hantera den precis som en förutsägbar webbtjänst.

## <a name="updating-your-web-service"></a>Uppdatera webbtjänsten
Nu när du har distribuerat experimentet som en webbtjänst vad händer om du behöver uppdatera den?

Det beror på vad du behöver uppdatera:

**Du vill ändra indata eller utdata, eller om du vill ändra hur webbtjänsten manipulerar data**

Om du inte ändrar modellen, men bara ändrar hur webbtjänsten hanterar data, du kan redigera prediktivt experiment och klicka sedan på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [ny]** igen. Webbtjänsten har stoppats, uppdaterade prediktivt experiment distribueras och webbtjänsten har startats om.

Här är ett exempel: Anta att dina prediktivt experiment Returnerar hela raden som indata med det förväntade resultatet. Du kan bestämma att du vill att webbtjänsten för att bara returnera resultatet. Så att du kan lägga till en **Projektkolumner** modul i prediktivt experiment, precis före utdataporten för att utesluta kolumner utom resultatet. När du klickar på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [ny]** igen, webbtjänsten har uppdaterats.

**Du vill träna om modellen med nya data**

Om du vill behålla din modell för maskininlärning, men du vill träna om den med nya data, har du två alternativ:

1. **Träna om modellen när webbtjänsten körs** -om du vill träna om din modell medan förutsägande webbtjänsten körs, kan du göra detta genom att göra några ändringar i träningsexperiment så att den en ***via programmering Experimentera***, och du kan distribuera den som en  ***omtränings web* tjänsten**. Instruktioner om hur du gör detta finns [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).
2. **Gå tillbaka till den ursprungliga träningsexperiment och använder olika utbildningsdata för att utveckla din modell** - experimentet förutsägande är kopplad till webbtjänsten, men utbildning experimentet länkas inte direkt på detta sätt. Om du ändrar den ursprungliga träningsexperiment och klicka på **konfigurera Web Service**, skapas en *nya* förutsägande experiment som, när de distribueras, skapar en *nya* webbtjänsten. Uppdaterar den inte bara ursprungliga webbtjänsten.
   
   Om du behöver ändra experimentet utbildning öppnas och klicka **Spara som** att göra en kopia. Detta lämnar kvar det ursprungliga träningsexperiment prediktivt experiment och webbtjänsten. Du kan nu skapa en ny webbtjänst med dina ändringar. När du har distribuerat den nya webbtjänsten som sedan kan du bestämma om du vill stoppa tidigare webbtjänsten eller att det ska fungera tillsammans med en ny.

**Vill du tränar en annan modell**

Om du vill göra ändringar i din ursprungliga prediktivt experiment, till exempel att välja en annan maskininlärningsalgoritmen, försök med en annan utbildning metoden osv., måste du följa den andra proceduren som beskrivs ovan för omtränings din modell: öppna utbildning experiment, klicka på **Spara som** att göra en kopia och sedan starta ned den nya sökvägen för att utveckla din modell , skapa prediktivt experiment och distribuera webbtjänsten. Detta skapar en ny webbplats som tjänsten inte är relaterade till det ursprungliga - du kan välja vilken eller både och fortsätta att köra.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar och experiment, finns i följande artiklar:

* konvertering av experimentet - [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* distribuera webbtjänsten - [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md)
* omtränings modell - [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md)

För exempel på hela processen, se:

* [Självstudie om maskininlärning: skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md)
* [Genomgång: Utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

