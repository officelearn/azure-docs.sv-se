---
title: "Hur du förbereder din modell för distribution i Azure Machine Learning Studio | Microsoft Docs"
description: "Hur du förbereder din tränade modellen för distribution som en webbtjänst genom att konvertera experimentet Machine Learning Studio utbildning till prediktivt experiment."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: garye
ms.openlocfilehash: 96434d6c5a2cf73e425c02d35d546ab33b7ac13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Hur du förbereder din modell för distribution i Azure Machine Learning Studio

Azure Machine Learning Studio ger dig de verktyg du behöver för att utveckla en förutsägelseanalysmodell och sedan använda det genom att distribuera den som Azure-webbtjänst.

Om du vill göra detta måste du använda Studio för att skapa ett experiment - kallas en *träningsexperiment* – där du träna poängsätta och redigera din modell. När du är nöjd du hämta din modell redo att distribuera genom att konvertera experimentet utbildning till en *prediktivt experiment* som är konfigurerad för att poängsätta användardata.

Du kan se ett exempel på hur [genomgång: utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md).

Den här artikeln tar en djupdykning i information om hur en träningsexperiment hämtar konverteras till en prediktivt experiment och hur det prediktivt experimentet distribueras. Förstå dessa uppgifter du lära dig hur du konfigurerar din distribuerade modell för att göra det mer effektivt.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Översikt 

Att konvertera en träningsexperiment till en prediktivt experiment omfattar tre steg:

1. Ersätt maskininlärning algoritmen moduler med din tränad modell.
2. Trim experimentet dessa moduler som krävs för resultatfunktioner. En träningsexperiment innehåller ett antal moduler som krävs för träning, men behövs inte när modellen har tränats.
3. Definiera hur din modell tar emot data från web service-användare och vilka data som ska returneras.

> [!TIP]
> Du har varit berörda med utbildning och bedömningen modellen med dina egna data i experimentet utbildning. Men distribution kan användare skicka nya data till din modell och förutsägelse resultat returneras. Så som du konverterar experimentet utbildning till prediktivt experiment att förbereda för distribution, Tänk på hur modellen kommer att användas av andra.
> 
> 

## <a name="set-up-web-service-button"></a>Konfigurera Web Service-knappen
När du har kört experimentet (klicka på **kör** längst ned i arbetsytan för experimentet), klickar du på den **konfigurera Web Service** knappen (Välj den **förutsägande webbtjänsten** alternativet). **Konfigurera Web Service** utför du de tre stegen för konvertering av experimentet utbildning till prediktivt experiment:

1. Sparar den tränade modellen i den **tränade modeller** avsnitt i modulpaletten (till vänster om arbetsytan för experimentet). Ersätter den maskininlärningsalgoritmen och [Träningsmodell] [ train-model] moduler med den sparade tränade modellen.
2. Analyseras experimentet och tar bort moduler som tydligt används endast för träning och inte längre behövs.
3. Infogas _Web service indata_ och _utdata_ moduler till standardplatser i experimentet (dessa moduler acceptera och returnera användardata).

Till exempel tränar följande experimentet en två-klass ökat beslut trädet modell med exempeldata inventering:

![Träningsexperiment][figure1]

Modulerna i experimentet utför i princip fyra olika funktioner:

![Modulfunktioner][figure2]

När du konverterar den här träningsexperiment till prediktivt experiment vissa av dessa moduler längre behövs inte eller de nu har olika syften:

* **Data** -data i det här exemplet dataset används inte vid bedömningen - användaren av webbtjänsten tillhandahåller data som ska mätas. Dock används metadata från den här datauppsättningen, till exempel datatyper, av den tränade modellen. Så du måste behålla datauppsättningen i prediktivt experiment så att den kan ge dessa metadata.

* **Förbered** – beroende på informationen som skickas för poäng, dessa moduler kan eller inte nödvändigt att bearbeta inkommande data. Den **konfigurera Web Service** knappen inte touch dessa – måste du bestämma hur du vill hantera dem.
  
    Till exempel i det här exemplet exempel dataset kan ha värden som saknas, så en [Rensa Data som saknas] [ clean-missing-data] modulen kunde med för att hantera dem. Dessutom innehåller exempel datauppsättningen kolumner som inte behövs för att träna modellen. Därför en [Välj kolumner i datauppsättning] [ select-columns] modulen ingick vill utesluta dataflödet de extra kolumnerna. Om du vet att de data som skickas för resultatfunktioner via webbtjänsten har inte värden som saknas och sedan kan du ta bort den [Rensa Data som saknas] [ clean-missing-data] modul. Eftersom den [Välj kolumner i datauppsättning] [ select-columns] modulen bidrar till att definiera kolumner med data som förväntas av den tränade modellen, att modulen måste vara.

* **Träna** -modulerna som används för att träna modellen. När du klickar på **konfigurera Web Service**, dessa moduler ersätts med en enda modul som innehåller modellen du tränats. Den här nya modulen sparas i den **tränade modeller** avsnitt i modulpaletten.

* **Poäng** – i det här exemplet i [dela Data] [ split] modulen används för att dela dataströmmen i testdata och utbildningsdata. I prediktivt experiment vi inte tränar längre så [dela Data] [ split] kan tas bort. På liknande sätt kan andra [Poängmodell] [ score-model] modulen och [utvärdera modell] [ evaluate-model] modulen används för att jämföra resultatet från testdata, så dessa moduler inte behövs i prediktivt experiment. De återstående [Poängmodell] [ score-model] modulen, men behövs för att returnera ett poäng resultat via webbtjänsten.

Här är hur vårt exempel ser ut när du klickar på **konfigurera Web Service**:

![Konvertera prediktivt experiment][figure3]

Arbete som utförs av **konfigurera Web Service** kan vara tillräcklig för att förbereda experimentet distribueras som en webbtjänst. Du kanske vill vissa ytterligare arbete som är specifika för experimentet.

### <a name="adjust-input-and-output-modules"></a>Justera inkommande och utgående moduler
I experimentet utbildning används en uppsättning träningsdata och har vissa bearbetning för att hämta data i ett formulär som behövs för machine learning-algoritmen. Om de data som du förväntar dig att få via webbtjänsten inte behöver denna bearbetning, du kan kringgå: ansluta utdata från den **inkommande Web service-modulen** till en annan modul i experimentet. Användarens data kommer nu i modellen på den här platsen.

Som standard **konfigurera Web Service** placerar den **Web service indata** modulen överst i dataflöde, som visas i bilden ovan. Men vi kan placera manuellt de **Web service indata** tidigare databearbetning moduler:

![Flytta web service-indata][figure4]

Indata som tillhandahålls via webbtjänsten skickas nu direkt i modulen poängsätta modell utan någon bearbetning i förväg.

På samma sätt som standard **konfigurera Web Service** placerar modulen Web service längst ned i din dataflöde. I det här exemplet webbtjänsten kommer tillbaka till användaren utdata från den [Poängmodell] [ score-model] modulen som innehåller fullständiga indata-vektorn plus bedömningsprofil resultaten.
Om du föredrar att returnera något annat sedan du kan dock lägga till ytterligare moduler innan den **Web service utdata** modul. 

Lägg exempelvis för att returnera endast bedömningsprofil resultat och inte hela vektorn som indata till en [Välj kolumner i datauppsättning] [ select-columns] modulen för att utesluta alla kolumner utom bedömningsprofil resultatet. Flytta sedan de **Web service utdata** modulen till utdataporten för den [Välj kolumner i datauppsättning] [ select-columns] modulen. Experimentet ser ut så här:

![Flytta web service-utdata][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Lägg till eller ta bort ytterligare rutiner för databehandling moduler
Om det finns flera moduler i experimentet som du vet inte behövs under bedömningen, kan dessa tas bort. Till exempel eftersom vi har flyttat den **Web service indata** modul till en punkt efter databearbetning moduler som vi kan ta bort den [Rensa Data som saknas] [ clean-missing-data] modul från prediktivt experiment.

Vår prediktivt experiment nu ser ut så här:

![Tar bort ytterligare modul][figure6]


### <a name="add-optional-web-service-parameters"></a>Lägga till valfria parametrar finns Web Service
I vissa fall kanske du vill tillåta användaren av webbtjänsten för att ändra funktionssättet för moduler när tjänsten används. *Webbtjänstparametrar* kan du göra detta.

Ett vanligt exempel är att lägga in en [importera Data] [ import-data] modulen så att användare av den distribuerade webbtjänsten kan ange en annan datakälla när webbtjänsten används. Eller konfigurera ett [exportera Data] [ export-data] modulen så att du kan ange ett annat mål.

Du kan definiera Webbtjänstparametrar och koppla dem till en eller flera parametrar, och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten tillhandahåller värdena för dessa parametrar när tjänsten används och åtgärderna som modulen ändras.

Mer information om vilka Webbtjänstparametrar är och hur de används finns [med Webbtjänstparametrar för Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuera prediktivt experiment som en webbtjänst
Nu när prediktivt experiment tillräckligt har förberetts, kan du distribuera den som en Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och dess förutsägelser returneras i modellen.

Mer information om fullständig distributionsprocessen finns [distribuera en Azure Machine Learning-webbtjänst][deploy]

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
