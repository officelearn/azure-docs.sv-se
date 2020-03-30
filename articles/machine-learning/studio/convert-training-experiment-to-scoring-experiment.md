---
title: Förbered modell för distribution
titleSuffix: ML Studio (classic) - Azure
description: Så här förbereder du din tränade modell för distribution som en webbtjänst genom att konvertera ditt maskininlärningsstudio (klassiska) utbildningsexperiment till ett förutsägelseexperiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204519"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Så här förbereder du din modell för distribution i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassisk) ger dig de verktyg du behöver för att utveckla en modell för förutsägelseanalys och sedan operationalisera den genom att distribuera den som en Azure-webbtjänst.

För att göra detta använder du Studio (klassisk) för att skapa ett experiment - kallat *träningsexperiment* - där du tränar, poängar och redigerar din modell. När du är nöjd får du din modell redo att distribuera genom att konvertera träningsexperimentet till ett *förutsägelseexperiment* som är konfigurerat för att få användardata.

Du kan se ett exempel på den här processen i [handledning 1: Förutsäga kreditrisk](tutorial-part1-credit-risk.md).

Den här artikeln tar en djupdykning i detaljerna i hur ett träningsexperiment omvandlas till ett prediktivt experiment och hur det prediktiva experimentet distribueras. Genom att förstå dessa detaljer kan du lära dig hur du konfigurerar den distribuerade modellen så att den blir mer effektiv.



## <a name="overview"></a>Översikt 

Processen att konvertera ett utbildningsexperiment till ett prediktivt experiment omfattar tre steg:

1. Byt ut maskininlärningsalgoritmmodulerna mot din tränade modell.
2. Trimma experimentet till endast de moduler som behövs för bedömning. Ett utbildningsexperiment innehåller ett antal moduler som är nödvändiga för utbildning men som inte behövs när modellen har tränats.
3. Definiera hur din modell ska acceptera data från webbtjänstanvändaren och vilka data som ska returneras.

> [!TIP]
> I ditt träningsexperiment har du ägnat dig åt att träna och göra mål på din modell med dina egna data. Men när distribueras, kommer användarna att skicka nya data till din modell och det kommer att returnera förutsägelse resultat. Så när du konverterar ditt träningsexperiment till ett förutsägelseexperiment för att få det redo för distribution, tänk på hur modellen kommer att användas av andra.
> 
> 

## <a name="set-up-web-service-button"></a>Knappen Konfigurera webbtjänst
När du har kört experimentet (klicka på **KÖR** längst ned på experimentarbetsytan) klickar du på knappen **Konfigurera webbtjänst** (välj alternativet **Förutsägande webbtjänst).** **Konfigurera webbtjänst** utför för dig de tre stegen för att konvertera ditt träningsexperiment till ett förutsägelseexperiment:

1. Det sparar din tränade modell i avsnittet **Tränade modeller** i modulpaletten (till vänster om experimentduken). Den ersätter sedan maskininlärningsalgoritmen och [tågmodellmodulerna][train-model] med den sparade tränade modellen.
2. Den analyserar ditt experiment och tar bort moduler som tydligt användes endast för utbildning och inte längre behövs.
3. Den infogar in- och _utdatamoduler_ för _webbtjänster_ på standardplatser i experimentet (dessa moduler accepterar och returnerar användardata).

Följande experiment tränar till exempel en tvåklassbestufferad beslutsträdmodell med hjälp av exempelräkningsdata:

![Experiment för utbildning](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Modulerna i detta experiment utför i princip fyra olika funktioner:

![Modulfunktioner](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

När du konverterar det här träningsexperimentet till ett förutsägelseexperiment behövs inte längre några av dessa moduler, eller så tjänar de nu ett annat syfte:

* **Data** - Data i den här exempeldatauppsättningen används inte under bedömning - webbtjänstens användare kommer att tillhandahålla de data som ska poängsättas. Metadata från den här datauppsättningen, till exempel datatyper, används dock av den tränade modellen. Så du måste behålla datauppsättningen i förutsägelseexperimentet så att den kan tillhandahålla dessa metadata.

* **Prep** - Beroende på vilka användardata som ska skickas för bedömning kan dessa moduler vara nödvändiga för att bearbeta inkommande data. Knappen **Konfigurera webbtjänst** vidrör inte dessa – du måste bestämma hur du vill hantera dem.
  
    I det här exemplet kan exempeldatauppsättningen till exempel ha värden som saknas, så en clean [missing data-modul][clean-missing-data] inkluderades för att hantera dem. Exempeldatauppsättningen innehåller också kolumner som inte behövs för att träna modellen. Så en [Select Columns in Dataset-modul inkluderades][select-columns] för att utesluta dessa extra kolumner från dataflödet. Om du vet att de data som ska skickas för bedömning via webbtjänsten inte kommer att saknas värden, kan du ta bort modulen [Rensa data som saknas.][clean-missing-data] Eftersom modulen [Välj kolumner i datauppsättningen][select-columns] hjälper till att definiera kolumnerna med data som den tränade modellen förväntar sig, måste modulen finnas kvar.

* **Tåg** - Dessa moduler används för att träna modellen. När du klickar på **Konfigurera webbtjänst**ersätts dessa moduler med en enda modul som innehåller modellen du har tränat. Den här nya modulen sparas i avsnittet **Tränade modeller** i modulpaletten.

* **Poäng** - I det här exemplet används modulen [Dela data][split] för att dela upp dataströmmen i testdata och träningsdata. I förutsägelseexperimentet tränar vi inte längre, så [dela data][split] kan tas bort. På samma sätt används den andra [poängmodellmodulen][score-model] och modulen [Utvärdera modell][evaluate-model] för att jämföra resultat från testdata, så dessa moduler behövs inte i det prediktiva experimentet. Den återstående [poängmodellmodulen][score-model] behövs dock för att returnera ett poängresultat via webbtjänsten.

Så här ser vårt exempel ut när du har klickat på **Konfigurera webbtjänst:**

![Konverterade prediktiva experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Det arbete som utförs av **Konfigurera webbtjänst** kan vara tillräckligt för att förbereda experimentet så att det distribueras som en webbtjänst. Du kanske vill göra ytterligare arbete som är specifikt för experimentet.

### <a name="adjust-input-and-output-modules"></a>Justera in- och utdatamoduler
I ditt träningsexperiment använde du en uppsättning träningsdata och gjorde sedan en del bearbetning för att hämta data i ett formulär som maskininlärningsalgoritmen behövde. Om de data som du förväntar dig att ta emot via webbtjänsten inte behöver den här bearbetningen kan du kringgå den: anslut utdata från **webbtjänstens indatamodul** till en annan modul i experimentet. Användarens data kommer nu att anlända i modellen på den här platsen.

Som standard **placerar du webbtjänst** som standard **webbtjänsts indatamodulen** högst upp i dataflödet, vilket visas i bilden ovan. Men vi kan manuellt placera **webbtjänsten indata** förbi modulerna för databehandling:

![Flytta webbtjänstens indata](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Indata som tillhandahålls via webbtjänsten kommer nu att gå direkt till poängmodellmodulen utan förbehandling.

På samma sätt placerar som standard **Konfigurera webbtjänst** webbtjänst webbtjänstutdatamodulen längst ned i dataflödet. I det här exemplet returnerar webbtjänsten till användaren utdata från modulen [Poängmodell,][score-model] som innehåller hela indatavektorn plus poängresultaten.
Men om du föredrar att returnera något annat, kan du lägga till ytterligare moduler innan **webbtjänstens utdatamodul.** 

Om du till exempel bara vill returnera poängresultaten och inte hela vektorn för indata lägger du till en [Select Columns i Dataset-modulen][select-columns] för att utesluta alla kolumner utom poängresultaten. Flytta sedan **utdatamodulen** för webbtjänsten till utdata för modulen [Välj kolumner i datauppsättning.][select-columns] Experimentet ser ut så här:

![Flytta webbtjänstutdata](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Lägga till eller ta bort ytterligare databehandlingsmoduler
Om det finns fler moduler i experimentet som du vet inte kommer att behövas under bedömning, kan dessa tas bort. Eftersom vi till exempel flyttade **indatamodulen** för webbtjänsten till en punkt efter databehandlingsmodulerna kan vi ta bort modulen [Rensa saknade data][clean-missing-data] från det prediktiva experimentet.

Vårt prediktiva experiment ser nu ut så här:

![Ta bort ytterligare modul](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Lägga till valfria webbtjänstparametrar
I vissa fall kanske du vill tillåta att användaren av webbtjänsten ändrar beteendet för moduler när tjänsten används. *Med webbtjänstparametrar* kan du göra detta.

Ett vanligt exempel är att konfigurera en [importdatamodul][import-data] så att användaren av den distribuerade webbtjänsten kan ange en annan datakälla när webbtjänsten används. Eller konfigurera en [exportdatamodul][export-data] så att ett annat mål kan anges.

Du kan definiera webbtjänstparametrar och associera dem med en eller flera modulparametrar, och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten tillhandahåller värden för dessa parametrar när tjänsten används och modulåtgärderna ändras i enlighet med detta.

Mer information om vad parametrar för webbtjänst och hur du använder dem finns i [Använda Azure Machine Learning Web Service Parametrar][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuera förutsägelseexperimentet som en webbtjänst
Nu när förutsägelseexperimentet har förberetts tillräckligt kan du distribuera det som en Azure-webbtjänst. Med hjälp av webbtjänsten kan användare skicka data till din modell och modellen returnerar sina förutsägelser.

Mer information om hela distributionsprocessen finns i [Distribuera en Azure Machine Learning-webbtjänst][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
