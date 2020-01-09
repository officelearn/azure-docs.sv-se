---
title: Förbered modell för distribution
titleSuffix: ML Studio (classic) - Azure
description: Förbereda din tränade modell för distribution som en webb tjänst genom att konvertera din Machine Learning Studio (klassiska) utbildnings experiment till ett förutsägelse experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: e397cfaa9ce521ebe3c2f46ef6cc015bff3112f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454829"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Förbereda din modell för distribution i Azure Machine Learning Studio (klassisk)

Azure Machine Learning Studio (klassisk) ger dig de verktyg du behöver för att utveckla en förutsägelse analys modell och sedan operationalisera den genom att distribuera den som en Azure-webbtjänst.

Det gör du genom att använda Studio (klassisk) för att skapa ett experiment som kallas ett *utbildnings experiment* – där du tränar, visar poäng och redigerar din modell. När du är nöjd kan du få din modell redo att distribuera genom att konvertera din träningsexperiment till ett *förutsägelseexperiment* som har konfigurerats poäng användardata.

Du kan se ett exempel på den här processen i [självstudie 1: förutsägelse kredit risk](tutorial-part1-credit-risk.md).

Den här artikeln tar en djupdykning i hur ett träningsexperiment konverteras till ett förutsägelseexperiment och hur den förutsägelseexperiment distribueras. Genom att förstå dessa uppgifter kan du lära dig hur du konfigurerar din distribuerade modell för att göra det mer effektivt.



## <a name="overview"></a>Översikt 

Att konvertera ett träningsexperiment till ett förutsägbart experiment omfattar tre steg:

1. Ersätt de machine learning-algoritm moduler med den tränade modellen.
2. Trimma experimentet till de moduler som krävs för bedömning. Ett träningsexperiment innehåller ett antal moduler som krävs för utbildning omfattas dock inte när modellen tränas.
3. Definiera hur din modell ska ta emot data från web service-användare och vilka data som ska returneras.

> [!TIP]
> I experimentet utbildning har du jobbat berörda med träning och bedömning av din modell med dina egna data. Men när distribuerats användare skickar nya data i din modell och förutsagda resultaten som returneras. Därför som du konverterar din träningsexperiment till ett förutsägbart experiment att förbereda för distribution, Tänk på hur modellen kommer att användas av andra.
> 
> 

## <a name="set-up-web-service-button"></a>Konfigurera Web Service-knappen
När du har kört experimentet (klicka på **kör** längst ned på arbetsytan för experimentet), klickar du på den **konfigurera Web Service** knappen (Välj den **förutsägande webbtjänst** alternativet). **Konfigurera Web Service** utför du de tre stegen för att konvertera din träningsexperiment till ett förutsägbart experiment:

1. Det sparar tränade modellen i den **tränade modeller** delen av modulpaletten (till vänster om arbetsytan för experimentet). Den ersätter sedan Machine Learning-algoritmen och [tränar modell][train-model] moduler med den sparade tränade modellen.
2. Den analyserar experimentet och tar bort moduler som tydligt användes enbart för utbildning och inte längre behövs.
3. Den infogar _Web service indata_ och _utdata_ moduler till standardplatser i experimentet (dessa moduler godkänner och returnerar användardata).

Till exempel träna följande experiment finns en modell för trädet av tvåklassförhöjt beslutsträd med hjälp av exemplet insamlade data:

![Träningsexperiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduler i det här experimentet utför i princip fyra olika funktioner:

![Modulfunktioner](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

När du konverterar den här träningsexperiment till ett förutsägbart experiment, några av dessa moduler längre behövs inte eller de nu har en annan syfte:

* **Data** -data i den här exempeldatauppsättningen används inte vid bedömning – användaren av webbtjänsten innehåller informationen som ska poängsättas. Dock används metadata från den här datauppsättningen, till exempel datatyper, av den tränade modellen. Så du måste behålla datauppsättningen i förutsägbart experiment så att det kan ge dessa metadata.

* **Förbered** – beroende på användarens data som ska skickas för bedömning, dessa moduler kan eller inte vara nödvändigt att ta emot inkommande data. Den **konfigurera Web Service** knappen inte touch dessa – måste du bestämma hur du vill hantera dem.
  
    I det här exemplet kan exempel data uppsättningen ha saknade värden, så en [rensad][clean-missing-data] datamodul som saknas inkluderades för att hantera dem. Dessutom innehåller exempeldatauppsättningen kolumner som inte behövs för att träna modellen. Därför inkluderades en [Välj kolumner i data uppsättnings][select-columns] modulen för att utesluta de extra kolumnerna från data flödet. Om du vet att de data som ska skickas för poängsättning via webb tjänsten saknar värden som saknas kan du ta bort modulen [Rensa data som saknas][clean-missing-data] . Eftersom modulen [Välj kolumner i data uppsättning][select-columns] hjälper till att definiera kolumner med data som den tränade modellen förväntar sig, måste modulen vara kvar.

* **Träna** -modulerna som används för att träna modellen. När du klickar på **konfigurera Web Service**, dessa moduler har ersatts med en enda modul som innehåller modellen du tränas. Den här nya modulen sparas i den **tränade modeller** delen av modulpaletten.

* **Score** – i det här exemplet används modulen [dela data][split] för att dela upp data strömmen i test data och tränings data. I det förutsägande experimentet har vi inte längre utbildning, så [delade data][split] kan tas bort. På samma sätt används den andra [Poäng modell][score-model] modulen och modulen [utvärdera modell][evaluate-model] för att jämföra resultat från test data, så dessa moduler behövs inte i förutsägelse experimentet. Den återstående [poängen modell][score-model] -modulen behövs dock för att returnera ett resultat resultat via webb tjänsten.

Här är hur vårt exempel ser ut när du klickar på **konfigurera Web Service**:

![Konverterade förutsägbart experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Arbetet som utförs av **konfigurera Web Service** kan vara tillräckliga för att förbereda ditt experiment som kan distribueras som en webbtjänst. Dock kan du utföra vissa ytterligare arbete som är specifika för ditt experiment.

### <a name="adjust-input-and-output-modules"></a>Justera inkommande och utgående moduler
I din träningsexperiment används en uppsättning träningsdata och har viss bearbetning för att hämta data i ett formulär som behövs för machine learning-algoritm. Om de data du förväntar dig att ta emot via webbtjänsten inte behöver denna bearbetning, kan du kringgå det: Anslut utdataporten för den **Web service indata-modulen** till en annan modul i experimentet. Användarens data kommer nu i modellen på den här platsen.

Som standard **konfigurera Web Service** placerar den **Web service indata** modulen överst i ditt dataflöde, som visas i bilden ovan. Men vi kan placera manuellt de **Web service indata** tidigare databearbetning-moduler:

![Flytta webbtjänstindata](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Indata via webbtjänsten nu skickas direkt till modulen poängsätta modell utan någon Förbearbeta.

På samma sätt som standard **konfigurera Web Service** placerar Web service utdata-modulen längst ned på ditt dataflöde. I det här exemplet återgår webb tjänsten till användaren utdata från modulen [Poäng modell][score-model] , som innehåller den fullständiga inmatnings data vektorn plus resultat resultaten.
Men om du föredrar att returnera något annat sedan du kan lägga till ytterligare moduler innan den **Web service utdata** modulen. 

Om du till exempel bara vill returnera resultat resultaten och inte hela vektorn för indata lägger du till en [Välj kolumner i data uppsättnings][select-columns] modulen för att undanta alla kolumner förutom resultat resultaten. Flytta sedan **webb tjänstens output-** modul till utdata från modulen [Välj kolumner i data uppsättning][select-columns] . Experimentet ser ut så här:

![Flytta web service-utdata](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Lägga till eller ta bort ytterligare bearbetning-moduler
Om det finns flera moduler i ditt experiment som du vet inte behövs under bedömning, kan de tas bort. Till exempel, eftersom vi flyttade **webb tjänstens inmatnings** modul till en punkt efter data bearbetnings modulerna, kan vi ta bort modulen [Rensa data som saknas][clean-missing-data] från förutsägelse experimentet.

Vår förutsägelseexperiment ser nu ut så här:

![Ta bort ytterligare modulen](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Lägga till valfria Webbtjänstparametrar
I vissa fall kanske du vill tillåta att användaren av webbtjänsten kan ändra beteendet för moduler när tjänsten används. *Web tjänstparametrar* gör att du kan göra detta.

Ett vanligt exempel är att skapa en modul för att [Importera data][import-data] så att användaren av den distribuerade webb tjänsten kan ange en annan data källa när webb tjänsten nås. Eller konfigurera en modul för att [Exportera data][export-data] så att du kan ange ett annat mål.

Du kan definiera Webbtjänstparametrar och koppla dem till en eller flera parametrar, och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten ger värden för dessa parametrar när tjänsten används, och åtgärderna som modulen ändras.

Mer information om vilka webb tjänst parametrar som är och hur de används finns i [använda Azure Machine Learning webb tjänst parametrar][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuera förutsägelseexperimentet som en webbtjänst
Nu när förutsägbart experiment har förberetts tillräckligt, kan du distribuera den som en Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och modellen returnerar dess förutsägelser.

Mer information om den fullständiga distributions processen finns i [distribuera en Azure Machine Learning-webbtjänst][deploy]

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
