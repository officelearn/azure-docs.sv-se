---
title: Hur en modell blir en webbtjänst
titleSuffix: ML Studio (classic) - Azure
description: En översikt över mekaniken för hur din Klassiska Azure Machine Learning Studio-modell fortskrider från ett utvecklingsexperiment till en webbtjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217926"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Hur en machine learning studio -modell (klassisk) går från ett experiment till en webbtjänst

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure Machine Learning Studio (klassisk) innehåller en interaktiv arbetsyta som gör att du kan utveckla, köra, testa och iterera ett ***experiment*** som representerar en förutsägande analysmodell. Det finns ett brett utbud av moduler som kan:

* Mata in data i experimentet
* Manipulera data
* Träna en modell med hjälp av maskininlärningsalgoritmer
* Poängsätt modellen
* Utvärdera resultatet
* Utdata slutliga värden

När du är nöjd med experimentet kan du distribuera det som en ***klassisk Azure Machine Learning Web-tjänst*** eller en ny Azure Machine Learning ***Web-tjänst*** så att användarna kan skicka nya data och ta emot tillbaka resultat.

I den här artikeln ger vi en översikt över mekaniken i hur din Machine Learning-modell fortskrider från ett utvecklingsexperiment till en operationaliserad webbtjänst.

> [!NOTE]
> Det finns andra sätt att utveckla och distribuera maskininlärningsmodeller, men den här artikeln fokuserar på hur du använder Machine Learning Studio (klassisk). Om du till exempel vill läsa en beskrivning av hur du skapar en klassisk prediktiv webbtjänst med R läser du blogginlägget [Build & Deploy Predictive Web Apps Using RStudio och Azure Machine Learning studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Medan Azure Machine Learning Studio (klassisk) är utformad för att hjälpa dig att utveckla och distribuera en *modell för förutsägande analys,* är det möjligt att använda Studio (klassisk) för att utveckla ett experiment som inte innehåller en förutsägande analysmodell. Ett experiment kan till exempel bara mata in data, ändra dem och sedan mata ut resultaten. Precis som ett förutsägelseanalysexperiment kan du distribuera det här icke-förutsägelseexperimentet som en webbtjänst, men det är en enklare process eftersom experimentet inte tränar eller poängsätter en maskininlärningsmodell. Även om det inte är det typiska att använda Studio (klassisk) på detta sätt, kommer vi att inkludera det i diskussionen så att vi kan ge en fullständig förklaring av hur Studio (klassisk) fungerar.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Utveckla och distribuera en prediktiv webbtjänst
Här är de stadier som en typisk lösning följer när du utvecklar och distribuerar den med Machine Learning Studio (klassisk):

![Distributionsflöde](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figur 1 - Stadier av en typisk prediktiv analysmodell*

### <a name="the-training-experiment"></a>Utbildningsexperimentet
***Utbildningsexperimentet*** är den inledande fasen av utvecklingen av din webbtjänst i Machine Learning Studio (klassisk). Syftet med träningsexperimentet är att ge dig en plats att utveckla, testa, iterera och så småningom träna en maskininlärningsmodell. Du kan även träna flera modeller samtidigt när du letar efter den bästa lösningen, men när du är klar med experimentet väljer du en enda tränad modell och eliminerar resten från experimentet. Ett exempel på att utveckla ett experiment med prediktiv analys finns i [Utveckla en prediktiv analyslösning för kreditriskbedömning i Azure Machine Learning Studio (klassiskt).](tutorial-part1-credit-risk.md)

### <a name="the-predictive-experiment"></a>Det prediktiva experimentet
När du har en tränad modell i ditt träningsexperiment klickar du på **Konfigurera webbtjänst** och väljer **Predictive Web Service** i Machine Learning Studio (klassisk) för att initiera processen med att konvertera träningsexperimentet till ett ***förutsägelseexperiment***. Syftet med förutsägelseexperimentet är att använda din tränade modell för att få nya data, med målet att så småningom bli operativ som en Azure Web-tjänst.

Den här konverteringen görs för dig genom följande steg:

* Konvertera uppsättningen moduler som används för utbildning till en enda modul och spara den som en utbildad modell
* Eliminera eventuella främmande moduler som inte är relaterade till poängsättning
* Lägga till in- och utdataportar som den slutliga webbtjänsten kommer att använda

Det kan finnas fler ändringar som du vill göra för att få ditt förutsägelseexperiment redo att distribueras som en webbtjänst. Om du till exempel vill att webbtjänsten bara ska mata ut en delmängd av resultaten kan du lägga till en filtreringsmodul före utdataporten.

I den här konverteringsprocessen ignoreras inte utbildningsexperimentet. När processen är klar har du två flikar i Studio (klassisk): en för träningsexperimentet och en för det prediktiva experimentet. På så sätt kan du göra ändringar i utbildningsexperimentet innan du distribuerar webbtjänsten och återskapar förutsägelseexperimentet. Du kan också spara en kopia av träningsexperimentet för att starta en annan experimentrad.

> [!NOTE]
> När du klickar på **Predictive Web Service** startar du en automatisk process för att konvertera träningsexperimentet till ett förutsägelseexperiment, och det fungerar bra i de flesta fall. Om ditt träningsexperiment är komplext (du har till exempel flera sökvägar för utbildning som du går samman med) kanske du föredrar att göra den här konverteringen manuellt. Mer information finns i [Så här förbereder du din modell för distribution i Azure Machine Learning Studio (klassiskt).](convert-training-experiment-to-scoring-experiment.md)
>
>

### <a name="the-web-service"></a>Webbtjänsten
När du är säker på att ditt förutsägelseexperiment är klart kan du distribuera tjänsten som antingen en klassisk webbtjänst eller en ny webbtjänst baserad på Azure Resource Manager. Om du vill använda modellen genom att distribuera den som en *webbkameratjänst för klassisk maskininlärning*klickar du på **Distribuera webbtjänst** och väljer **Distribuera webbtjänst [Klassisk]**. Om du vill distribuera som *webbtjänst för ny maskininlärning*klickar du på **Distribuera webbtjänst** och väljer **Distribuera webbtjänst [Ny]**. Användare kan nu skicka data till din modell med hjälp av REST API för webbtjänsten och få tillbaka resultaten. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Det icke-typiska fallet: skapa en icke-prediktiv webbtjänst
Om experimentet inte tränar en modell för förutsägelseanalys behöver du inte skapa både ett träningsexperiment och ett bedömningsexperiment – det finns bara ett experiment och du kan distribuera det som en webbtjänst. Machine Learning Studio (klassisk) identifierar om experimentet innehåller en förutsägande modell genom att analysera de moduler du har använt.

När du har itererat på experimentet och är nöjd med det:

1. Klicka på **Konfigurera webbtjänst** och välj **Omskolningswebbtjänst** - in- och utdatanoder läggs till automatiskt
2. Klicka på **Kör**
3. Klicka på **Distribuera webbtjänst** och välj **Distribuera webbtjänst [Klassisk]** eller **Distribuera webbtjänst [Ny]** beroende på vilken miljö du vill distribuera till.

Webbtjänsten har nu distribuerats och du kan komma åt och hantera den precis som en förutsägande webbtjänst.

## <a name="updating-your-web-service"></a>Uppdatera webbtjänsten
Nu när du har distribuerat experimentet som en webbtjänst, vad händer om du behöver uppdatera det?

Det beror på vad du behöver uppdatera:

**Du vill ändra indata eller utdata eller ändra hur webbtjänsten manipulerar data**

Om du inte ändrar modellen, men bara ändrar hur webbtjänsten hanterar data, kan du redigera förutsägelseexperimentet och sedan klicka på **Distribuera webbtjänst** och välja **Distribuera webbtjänst [Klassisk]** eller **Distribuera webbtjänst [Ny]** igen. Webbtjänsten stoppas, det uppdaterade förutsägelseexperimentet distribueras och webbtjänsten startas om.

Här är ett exempel: Anta att ditt förutsägelseexperiment returnerar hela raden med indata med det förväntade resultatet. Du kan bestämma att du vill att webbtjänsten bara ska returnera resultatet. Så du kan lägga till en **projektkolumnermodul** i förutsägelseexperimentet, precis före utdataporten, för att utesluta andra kolumner än resultatet. När du klickar på **Distribuera webbtjänst** och väljer **Distribuera webbtjänst [Klassisk]** eller **Distribuera webbtjänst [Ny]** uppdateras webbtjänsten.

**Du vill träna om modellen med nya data**

Om du vill behålla din maskininlärningsmodell, men vill träna om den med nya data, har du två alternativ:

1. **Träna om modellen medan webbtjänsten körs** - Om du vill träna om din modell medan den prediktiva webbtjänsten körs kan du göra detta genom att göra ett par ändringar i utbildningsexperimentet för att göra det till ett ***omskolningsexperiment***, kan du distribuera den som en ** *omskolningswebbtjänst* **. Instruktioner om hur du gör detta finns i [Omskola machine learning-modeller programmässigt](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Gå tillbaka till det ursprungliga träningsexperimentet och använd olika träningsdata för att utveckla din modell** - Ditt prediktiva experiment är kopplat till webbtjänsten, men utbildningsexperimentet är inte direkt kopplat på detta sätt. Om du ändrar det ursprungliga utbildningsexperimentet och klickar på **Konfigurera webbtjänst**skapas ett *nytt* förutsägelseexperiment som, när det distribueras, skapar en *ny* webbtjänst. Det uppdaterar inte bara den ursprungliga webbtjänsten.

   Om du behöver ändra träningsexperimentet öppnar du det och klickar på **Spara som** för att göra en kopia. Detta kommer att lämna intakt den ursprungliga utbildningen experiment, prediktiva experiment och webbtjänst. Nu kan du skapa en ny webbtjänst med ändringarna. När du har distribuerat den nya webbtjänsten kan du sedan bestämma om du vill stoppa den tidigare webbtjänsten eller låta den köras tillsammans med den nya.

**Du vill träna en annan modell**

Om du vill göra ändringar i det ursprungliga förutsägelseexperimentet, till exempel välja en annan maskininlärningsalgoritm, prova en annan träningsmetod osv., måste du följa den andra proceduren som beskrivs ovan för omskolning av din modell: öppna träningsexperimentet, klicka på **Spara som** för att göra en kopia och börja sedan ned den nya vägen för att utveckla din modell, skapa det prediktiva experimentet och distribuera webbtjänsten. Detta kommer att skapa en ny webbtjänst som inte är relaterad till den ursprungliga - du kan bestämma vilken, eller båda, att hålla igång.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar och experimenterar finns i följande artiklar:

* konvertera experimentet – [Så här förbereder du din modell för distribution i Azure Machine Learning Studio (klassiskt)](convert-training-experiment-to-scoring-experiment.md)
* distribuera webbtjänsten – [distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md)
* omskolning av modellen - [Omskolning Machine Learning modeller programmässigt](/azure/machine-learning/studio/retrain-machine-learning-model)

Exempel på hela processen finns i:

* [Självstudiekurs för maskininlärning: Skapa ditt första experiment i Azure Machine Learning Studio (klassisk)](create-experiment.md)
* [Genomgång: Utveckla en lösning för prediktiv analys för kreditriskbedömning i Azure Machine Learning](tutorial-part1-credit-risk.md)

