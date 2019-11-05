---
title: Förbered modell för distribution
titleSuffix: Azure Machine Learning Studio (classic)
description: Förbereda din tränade modell för distribution som en webb tjänst genom att konvertera din Machine Learning Studio (klassiska) utbildnings experiment till ett förutsägelse experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: caaed83417ac1eaadc407fb12dc8bb360aae45ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493267"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Förbereda din modell för distribution i Azure Machine Learning Studio (klassisk)

Azure Machine Learning Studio (klassisk) ger dig de verktyg du behöver för att utveckla en förutsägelse analys modell och sedan operationalisera den genom att distribuera den som en Azure-webbtjänst.

Det gör du genom att använda den klassiska versionen av Studio för att skapa ett experiment som kallas ett *utbildnings experiment* – där du tränar, visar poäng och redigerar din modell. När du är nöjd kan du distribuera din modell genom att konvertera ditt utbildnings experiment till ett *förutsägande experiment* som har kon figurer ATS för att Visa användar data.

Du kan se ett exempel på den här processen i [självstudie 1: förutsägelse kredit risk](tutorial-part1-credit-risk.md).

I den här artikeln får du en översikt över hur ett utbildnings experiment kommer att konverteras till ett förutsägelse experiment och hur det förutsägande experimentet distribueras. Genom att förstå dessa uppgifter kan du lära dig hur du konfigurerar din distribuerade modell så att den blir mer effektiv.



## <a name="overview"></a>Översikt 

Processen med att konvertera ett utbildnings experiment till ett förutsägelse experiment innefattar tre steg:

1. Ersätt modulerna för Machine Learning-algoritm med din tränaa modell.
2. Trimma experimentet till endast de moduler som behövs för att kunna ta poäng. Ett utbildnings experiment innehåller ett antal moduler som är nödvändiga för utbildning, men som inte behövs när modellen har tränats.
3. Definiera hur din modell ska ta emot data från webb tjänst användaren och vilka data som ska returneras.

> [!TIP]
> I ditt utbildnings experiment har du lärt dig att träna och göra en bedömning av din modell med dina egna data. Men när de har distribuerats skickar användarna nya data till din modell så att de returnerar förutsägelse resultat. Så när du konverterar ditt utbildnings experiment till ett förutsägelse experiment för att förbereda den för distribution, bör du tänka på hur modellen kommer att användas av andra.
> 
> 

## <a name="set-up-web-service-button"></a>Knappen Konfigurera webb tjänst
När du har kört experimentet (klicka på **Kör** längst ned i experiment arbets ytan) klickar du på knappen **Konfigurera webb tjänst** (Välj alternativet för **förutsägelse webb tjänst** ). **Konfigurera webb tjänsten** utför för dig de tre stegen för att konvertera ditt utbildnings experiment till ett förutsägande experiment:

1. Den sparar din tränade modell i avsnittet **tränade modeller** i modulen modul (till vänster om experimentets arbets yta). Den ersätter sedan Machine Learning-algoritmen och [tränar modell][train-model] moduler med den sparade tränade modellen.
2. Det analyserar experimentet och tar bort moduler som används tydligt för utbildning och som inte längre behövs.
3. Den infogar moduler för indata och _utdata_ för _webb tjänst_ på standard platserna i experimentet (dessa moduler accepterar och returnerar användar data).

Följande experimenterar exempelvis en modell med två klasser som har förbättrats i besluts fattandet med exempel på inventerings data:

![Utbildnings experiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Modulerna i det här experimentet utför i princip fyra olika funktioner:

![Modul funktioner](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

När du konverterar det här inlärnings experimentet till ett förutsägelse experiment behövs inte längre några av de här modulerna, eller så har de nu ett annat syfte:

* **Data** -data i den här exempel data uppsättningen används inte under poängsättningen – användaren av webb tjänsten kommer att ange de data som ska poängas. Men metadata från den här data uppsättningen, till exempel data typer, används av den tränade modellen. Därför måste du behålla data uppsättningen i förutsägande experiment så att den kan tillhandahålla dessa metadata.

* **Prep** – beroende på vilka användar data som kommer att skickas för att komma fram kan dessa moduler vara nödvändiga för att bearbeta inkommande data. Knappen **Konfigurera webb tjänst** vidrör inte dessa – du måste bestämma hur du vill hantera dem.
  
    I det här exemplet kan exempel data uppsättningen ha saknade värden, så en [rensad][clean-missing-data] datamodul som saknas inkluderades för att hantera dem. Dessutom innehåller exempel data uppsättningen kolumner som inte behövs för att träna modellen. Därför inkluderades en [Välj kolumner i data uppsättnings][select-columns] modulen för att utesluta de extra kolumnerna från data flödet. Om du vet att de data som ska skickas för poängsättning via webb tjänsten saknar värden som saknas kan du ta bort modulen [Rensa data som saknas][clean-missing-data] . Eftersom modulen [Välj kolumner i data uppsättning][select-columns] hjälper till att definiera kolumner med data som den tränade modellen förväntar sig, måste modulen vara kvar.

* **Träna** – dessa moduler används för att träna modellen. När du klickar på **Konfigurera webb tjänsten**ersätts dessa moduler med en enda modul som innehåller den modell som du har tränat. Den här nya modulen sparas i avsnittet **tränade modeller** i modulen modul.

* **Score** – i det här exemplet används modulen [dela data][split] för att dela upp data strömmen i test data och tränings data. I det förutsägande experimentet har vi inte längre utbildning, så [delade data][split] kan tas bort. På samma sätt används den andra [Poäng modell][score-model] modulen och modulen [utvärdera modell][evaluate-model] för att jämföra resultat från test data, så dessa moduler behövs inte i förutsägelse experimentet. Den återstående [poängen modell][score-model] -modulen behövs dock för att returnera ett resultat resultat via webb tjänsten.

Så här ser vårt exempel ut efter att du har klickat på **Konfigurera webb tjänsten**:

![Konverterat förutsägelse experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Arbetet som du utför genom att **Konfigurera webb tjänsten** kan vara tillräckligt för att förbereda ditt experiment som ska distribueras som en webb tjänst. Men du kanske vill göra ytterligare arbete som är speciellt för experimentet.

### <a name="adjust-input-and-output-modules"></a>Justera moduler för indata och utdata
I ditt utbildnings experiment har du använt en uppsättning tränings data och gjorde bearbetningen för att hämta data i ett formulär som Machine Learning-algoritmen behövde. Om de data som du förväntar dig via webb tjänsten inte behöver den här bearbetningen kan du kringgå det: Anslut utdata från **webb tjänstens inmatnings modul** till en annan modul i experimentet. Användarens data kommer nu att tas med i modellen på den här platsen.

Som standard lägger du till **webb** tjänstens **inmatnings** modul överst i ditt data flöde, som du ser i bilden ovan. Men vi kan manuellt placera **webb tjänstens indata** förbi modulerna för data bearbetning:

![Flytta webb tjänsten inmatade](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

De indata som tillhandahålls via webb tjänsten skickas nu direkt till modulen Poäng modell utan att Förbearbeta.

Som standard lägger **webb** tjänsten i webb tjänstens output-modul längst ned i ditt data flöde. I det här exemplet återgår webb tjänsten till användaren utdata från modulen [Poäng modell][score-model] , som innehåller den fullständiga inmatnings data vektorn plus resultat resultaten.
Men om du föredrar att returnera något annat kan du lägga till ytterligare moduler innan du skapar **webb tjänstens output-** modul. 

Om du till exempel bara vill returnera resultat resultaten och inte hela vektorn för indata lägger du till en [Välj kolumner i data uppsättnings][select-columns] modulen för att undanta alla kolumner förutom resultat resultaten. Flytta sedan **webb tjänstens output-** modul till utdata från modulen [Välj kolumner i data uppsättning][select-columns] . Experimentet ser ut så här:

![Flytta webb tjänstens utdata](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Lägg till eller ta bort ytterligare data bearbetnings moduler
Om det finns fler moduler i experimentet som du vet inte kommer att behövas under poängsättningen kan du ta bort dem. Till exempel, eftersom vi flyttade **webb tjänstens inmatnings** modul till en punkt efter data bearbetnings modulerna, kan vi ta bort modulen [Rensa data som saknas][clean-missing-data] från förutsägelse experimentet.

Vårt förutsägande experiment ser nu ut så här:

![Tar bort ytterligare modul](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Lägg till valfria webb tjänst parametrar
I vissa fall kanske du vill tillåta att användaren av webb tjänsten ändrar beteendet för moduler när tjänsten nås. Med *webb tjänst parametrar* kan du göra detta.

Ett vanligt exempel är att skapa en modul för att [Importera data][import-data] så att användaren av den distribuerade webb tjänsten kan ange en annan data källa när webb tjänsten nås. Eller konfigurera en modul för att [Exportera data][export-data] så att du kan ange ett annat mål.

Du kan definiera webb tjänst parametrar och associera dem med en eller flera parametrar för modulen, och du kan ange om de är obligatoriska eller valfria. Användaren av webb tjänsten tillhandahåller värden för dessa parametrar när tjänsten används och modulerna ändras därefter.

Mer information om vilka webb tjänst parametrar som är och hur de används finns i [använda Azure Machine Learning webb tjänst parametrar][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuera förutsägelseexperimentet som en webbtjänst
Nu när det förutsägande experimentet har varit tillräckligt för berett kan du distribuera det som en Azure-webbtjänst. Med hjälp av webb tjänsten kan användarna skicka data till din modell och modellen returnerar sina förutsägelser.

Mer information om den fullständiga distributions processen finns i [distribuera en Azure Machine Learning-webbtjänst][deploy]

[deploy]: publish-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
