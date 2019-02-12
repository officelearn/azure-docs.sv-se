---
title: Förbereda modellen för distribution
titleSuffix: Azure Machine Learning Studio
description: Hur du förbereder din tränade modellen för distribution som en webbtjänst genom att konvertera din Machine Learning Studio-träningsexperiment till ett förutsägbart experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: 22cfdd22a8d2adacb5a5a5c817a628fe2c072755
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001705"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Hur du förbereder din modell för distribution i Azure Machine Learning Studio

Azure Machine Learning Studio ger dig de verktyg du behöver för att utveckla en förutsägande analysmodell och använda det genom att distribuera den som en Azure-webbtjänst.

Om du vill göra detta måste du använder Studio för att skapa ett experiment - kallas en *träningsexperiment* – där du träna, poäng och redigera din modell. När du är nöjd kan du få din modell redo att distribuera genom att konvertera din träningsexperiment till ett *förutsägelseexperiment* som har konfigurerats poäng användardata.

Du kan se ett exempel på hur det [ självstudie 1: Förutsäga kreditrisken](tutorial-part1-credit-risk.md).

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

1. Det sparar tränade modellen i den **tränade modeller** delen av modulpaletten (till vänster om arbetsytan för experimentet). Ersätter maskininlärningsalgoritmen och [Träningsmodell] [ train-model] moduler med den sparade tränade modellen.
2. Den analyserar experimentet och tar bort moduler som tydligt användes enbart för utbildning och inte längre behövs.
3. Den infogar _Web service indata_ och _utdata_ moduler till standardplatser i experimentet (dessa moduler godkänner och returnerar användardata).

Till exempel träna följande experiment finns en modell för trädet av tvåklassförhöjt beslutsträd med hjälp av exemplet insamlade data:

![Träningsexperiment][figure1]

Moduler i det här experimentet utför i princip fyra olika funktioner:

![Modulfunktioner][figure2]

När du konverterar den här träningsexperiment till ett förutsägbart experiment, några av dessa moduler längre behövs inte eller de nu har en annan syfte:

* **Data** -data i den här exempeldatauppsättningen används inte vid bedömning – användaren av webbtjänsten innehåller informationen som ska poängsättas. Dock används metadata från den här datauppsättningen, till exempel datatyper, av den tränade modellen. Så du måste behålla datauppsättningen i förutsägbart experiment så att det kan ge dessa metadata.

* **Förbered** – beroende på användarens data som ska skickas för bedömning, dessa moduler kan eller inte vara nödvändigt att ta emot inkommande data. Den **konfigurera Web Service** knappen inte touch dessa – måste du bestämma hur du vill hantera dem.
  
    Till exempel i det här exemplet exempeldatauppsättningen kan ha saknade värden, så en [Rensa Data som saknas] [ clean-missing-data] modulen kunde med för att hantera dem. Dessutom innehåller exempeldatauppsättningen kolumner som inte behövs för att träna modellen. Så en [Välj kolumner i datauppsättning] [ select-columns] modulen har inkluderats att undanta dessa extra kolumner från dataflödet. Om du vet att de data som skickas för bedömning via webbtjänsten har inte värden som saknas och sedan kan du ta bort den [Rensa Data som saknas] [ clean-missing-data] modulen. Eftersom den [Välj kolumner i datauppsättning] [ select-columns] modulen bidrar till att definiera kolumner med data som den tränade modellen förväntar sig, modulen måste vara.

* **Träna** -modulerna som används för att träna modellen. När du klickar på **konfigurera Web Service**, dessa moduler har ersatts med en enda modul som innehåller modellen du tränas. Den här nya modulen sparas i den **tränade modeller** delen av modulpaletten.

* **Poäng** – i det här exemplet på [dela Data] [ split] modulen används för att dela data i dataströmmen i testdata och träningsdata. I förutsägelseexperiment vi inte tränar längre, så [dela Data] [ split] kan tas bort. På samma sätt kan andra [Poängmodell] [ score-model] modulen och [utvärdera modell] [ evaluate-model] modulen används för att jämföra resultaten från testdata, så dessa moduler behövs inte i förutsägbart experiment. De återstående [Poängmodell] [ score-model] modulen, men behövs för att returnera ett poäng resultat via webbtjänsten.

Här är hur vårt exempel ser ut när du klickar på **konfigurera Web Service**:

![Konverterade förutsägbart experiment][figure3]

Arbetet som utförs av **konfigurera Web Service** kan vara tillräckliga för att förbereda ditt experiment som kan distribueras som en webbtjänst. Dock kan du utföra vissa ytterligare arbete som är specifika för ditt experiment.

### <a name="adjust-input-and-output-modules"></a>Justera inkommande och utgående moduler
I din träningsexperiment används en uppsättning träningsdata och har viss bearbetning för att hämta data i ett formulär som behövs för machine learning-algoritm. Om de data du förväntar dig att ta emot via webbtjänsten inte behöver denna bearbetning, kan du kringgå det: Anslut utdataporten för den **Web service indata-modulen** till en annan modul i experimentet. Användarens data kommer nu i modellen på den här platsen.

Som standard **konfigurera Web Service** placerar den **Web service indata** modulen överst i ditt dataflöde, som visas i bilden ovan. Men vi kan placera manuellt de **Web service indata** tidigare databearbetning-moduler:

![Flytta webbtjänstindata][figure4]

Indata via webbtjänsten nu skickas direkt till modulen poängsätta modell utan någon Förbearbeta.

På samma sätt som standard **konfigurera Web Service** placerar Web service utdata-modulen längst ned på ditt dataflöde. I det här exemplet webbtjänsten returneras till användaren utdata från den [Poängmodell] [ score-model] modulen, som innehåller fullständiga indata-vektor plus bedömnings resultaten.
Men om du föredrar att returnera något annat sedan du kan lägga till ytterligare moduler innan den **Web service utdata** modulen. 

Exempelvis om du vill returnera endast de bedömnings resultaten och inte hela vektor indata, lägga till en [Välj kolumner i datauppsättning] [ select-columns] modul för att exkludera alla kolumner utom bedömnings resultaten. Flytta den **Web service utdata** modulen till utdataporten för den [Välj kolumner i datauppsättning] [ select-columns] modulen. Experimentet ser ut så här:

![Flytta web service-utdata][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Lägga till eller ta bort ytterligare bearbetning-moduler
Om det finns flera moduler i ditt experiment som du vet inte behövs under bedömning, kan de tas bort. Till exempel eftersom vi har flyttat den **Web service indata** modul till en tidpunkt efter bearbetning av moduler som vi kan ta bort den [Rensa Data som saknas] [ clean-missing-data] modul från den förutsägbart experiment.

Vår förutsägelseexperiment ser nu ut så här:

![Ta bort ytterligare modulen][figure6]


### <a name="add-optional-web-service-parameters"></a>Lägga till valfria Webbtjänstparametrar
I vissa fall kanske du vill tillåta att användaren av webbtjänsten kan ändra beteendet för moduler när tjänsten används. *Web tjänstparametrar* gör att du kan göra detta.

Ett vanligt exempel att konfigurera en [importdata] [ import-data] modulen så att användare av den distribuerade webbtjänsten kan ange en annan datakälla när webbtjänsten används. Eller konfigurera en [exportera Data] [ export-data] modulen så att du kan ange ett annat mål.

Du kan definiera Webbtjänstparametrar och koppla dem till en eller flera parametrar, och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten ger värden för dessa parametrar när tjänsten används, och åtgärderna som modulen ändras.

Mer information om vilka Webbtjänstparametrar är och hur de används finns i [med Webbtjänstparametrar för Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuera förutsägelseexperiment som en webbtjänst
Nu när förutsägbart experiment har förberetts tillräckligt, kan du distribuera den som en Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och modellen returnerar dess förutsägelser.

Läs mer om fullständig distributionsprocessen [distribuera en Azure Machine Learning-webbtjänst][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
