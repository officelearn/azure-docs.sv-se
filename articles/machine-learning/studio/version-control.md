---
title: Application Lifecycle Management
titleSuffix: ML Studio (classic) - Azure
description: Använd metodtips för hantering av programlivscykeln i Azure Machine Learning Studio (klassisk)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 3f22ce3b1fb750e33e35d35ee1fe5ad1893abcfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204144"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Livscykelhantering för program i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassisk) är ett verktyg för att utveckla maskininlärningsexperiment som är funktionsdugliga i Azure-molnplattformen. Det är som visual studio IDE och skalbar molntjänst samman till en enda plattform. Du kan infoga standardpraxis för ALM -standardprogramlivscykelhantering (ALM) från att ta bort olika resurser till automatisk körning och distribution i Azure Machine Learning Studio (klassisk). I den här artikeln beskrivs några av alternativen och tillvägagångssätten.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experiment för versionshantering
Det finns två rekommenderade sätt att göra en version av experimenten. Du kan antingen förlita dig på den inbyggda körningshistoriken eller exportera experimentet i ett JSON-format för att hantera det externt. Varje tillvägagångssätt kommer med sina för-och nackdelar.

### <a name="experiment-snapshots-using-run-history"></a>Experimentera ögonblicksbilder med körhistorik
I körningsmodellen för det klassiska inlärningsexpertrationen för Azure Machine Learning Studio (klassisk) skickas en oföränderlig ögonblicksbild av experimentet till jobbschemaläggaren när du klickar på **Kör** i experimentredigeraren. Om du vill visa den här listan med ögonblicksbilder klickar du på **Kör historik** i kommandofältet i experimentredigerarvyn.

![Knappen Kör historik](./media/version-control/runhistory.png)

Du kan sedan öppna ögonblicksbilden i låst läge genom att klicka på namnet på experimentet när experimentet skickades för att köras och ögonblicksbilden togs. Observera att endast det första objektet i listan, som representerar det aktuella experimentet, är i redigerbart tillstånd. Observera också att varje ögonblicksbild kan vara i olika statuslägen också, inklusive Färdig (partiell körning), Misslyckades, Misslyckades (partiell körning) eller Utkast.

![Kör historiklista](./media/version-control/runhistorylist.png)

När det har öppnats kan du spara ögonblicksbildexperimentet som ett nytt experiment och sedan ändra det. Om experimentögonblicksbilden innehåller resurser som tränade modeller, transformeringar eller datauppsättningar som har uppdaterade versioner, behåller ögonblicksbilden referenserna till den ursprungliga versionen när ögonblicksbilden togs. Om du sparar den låsta ögonblicksbilden som ett nytt experiment identifierar Azure Machine Learning Studio (klassisk) förekomsten av en nyare version av dessa tillgångar och uppdaterar dem automatiskt i det nya experimentet.

Om du tar bort experimentet tas alla ögonblicksbilder av experimentet bort.

### <a name="exportimport-experiment-in-json-format"></a>Export-/importexperiment i JSON-format
Ögonblicksbilderna av körningshistoriken behåller en oföränderlig version av experimentet i Azure Machine Learning Studio (klassisk) varje gång den skickas för att köras. Du kan också spara en lokal kopia av experimentet och checka in det i ditt favoritstyrningssystem, till exempel Team Foundation Server, och senare återskapa ett experiment från den lokala filen. Du kan använda [Azure Machine Learning PowerShell-kommandona](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) och [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) för att åstadkomma detta.

JSON-filen är en textrepresentation av experimentdiagrammet, som kan innehålla en referens till tillgångar på arbetsytan, till exempel en datauppsättning eller en tränad modell. Den innehåller inte en serialiserad version av tillgången. Om du försöker importera JSON-dokumentet tillbaka till arbetsytan måste de refererade tillgångarna redan finnas med samma tillgångs-ID som refereras i experimentet. Annars kan du inte komma åt det importerade experimentet.

## <a name="versioning-trained-model"></a>Versionsutbildad modell
En tränad modell i Azure Machine Learning Studio (klassisk) serialiseras till`.iLearner`ett format som kallas en iLearner-fil ( ) och lagras i Azure Blob-lagringskontot som är associerat med arbetsytan. Ett sätt att få en kopia av iLearner filen är genom omskolning API. [I](/azure/machine-learning/studio/retrain-machine-learning-model) den här artikeln beskrivs hur omskolnings-API:et fungerar. Stegen på hög nivå:

1. Ställ in ditt träningsexperiment.
2. Lägg till en webbtjänstutdataport i modulen Tågmodell eller modulen som producerar den tränade modellen, till exempel Tune Model Hyperparameter eller Create R Model.
3. Kör ditt träningsexperiment och distribuera det sedan som en webbtjänst för modellutbildning.
4. Anropa BES-slutpunkten för utbildningswebbtjänsten och ange önskat iLearner-filnamn och Blob-lagringskontoplats där den ska lagras.
5. Skörda den producerade iLearner-filen när BES-anropet är klart.

Ett annat sätt att hämta iLearner-filen är via PowerShell-kommandolet [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Detta kan vara lättare om du bara vill få en kopia av iLearner filen utan att behöva omskola modellen programmässigt.

När du har iLearner-filen som innehåller den tränade modellen kan du sedan använda din egen versionsstrategi. Strategin kan vara så enkel som att tillämpa en pre / postfix som en namngivningskonvention och bara lämna iLearner filen i Blob lagring, eller kopiera / importera den till din version styrsystem.

Den sparade iLearner-filen kan sedan användas för bedömning via distribuerade webbtjänster.

## <a name="versioning-web-service"></a>Webbtjänst för versionshantering
Du kan distribuera två typer av webbtjänster från ett klassiskt experiment i Azure Machine Learning Studio. Den klassiska webbtjänsten är tätt kopplad till experimentet och arbetsytan. Den nya webbtjänsten använder Azure Resource Manager-ramverket och det är inte längre kopplat till det ursprungliga experimentet eller arbetsytan.

### <a name="classic-web-service"></a>Klassisk webbtjänst
Om du vill skapa en klassisk webbtjänst kan du dra nytta av slutpunktskonstruktionen för webbtjänsten. Här är ett typiskt flöde:

1. Från det förutsägelseexperiment som du använder distribuerar du en ny klassisk webbtjänst som innehåller en standardslutpunkt.
2. Du skapar en ny slutpunkt med namnet ep2, som exponerar den aktuella versionen av experiment/tränad modell.
3. Du går tillbaka och uppdaterar ditt förutsägelseexperiment och tränade modell.
4. Du distribuerar om förutsägelseexperimentet, som sedan uppdaterar standardslutpunkten. Men detta kommer inte att förändra ep2.
5. Du skapar ytterligare en slutpunkt med namnet ep3, som exponerar den nya versionen av experimentet och den tränade modellen.
6. Gå tillbaka till steg 3 om det behövs.

Med tiden kan du ha många slutpunkter som skapats i samma webbtjänst. Varje slutpunkt representerar en punkt-i-tid-kopia av experimentet som innehåller point-in-time-versionen av den tränade modellen. Du kan sedan använda extern logik för att avgöra vilken slutpunkt som ska anropas, vilket i praktiken innebär att välja en version av den tränade modellen för bedömningskörningen.

Du kan också skapa många identiska webbtjänstslutpunkter och sedan korrigera olika versioner av iLearner-filen till slutpunkten för att uppnå liknande effekt. [I den här artikeln](create-models-and-endpoints-with-powershell.md) förklaras mer i detalj hur du utför det.

### <a name="new-web-service"></a>Ny webbtjänst
Om du skapar en ny Azure Resource Manager-baserad webbtjänst är slutpunktskonstruktionen inte längre tillgänglig. I stället kan du generera WSD-filer (Web Service Definition) i JSON-format från ditt förutsägelseexperiment med hjälp av [kommandoleten Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell eller genom att använda kommandoleten [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell från en distribuerad Resource Manager-baserad webbtjänst.

När du har den exporterade WSD-filen och versionen kontrollera den kan du också distribuera WSD som en ny webbtjänst i en annan webbtjänstplan i en annan Azure-region. Se bara till att du anger rätt konfiguration av lagringskonto samt det nya webbtjänstplan-ID:et. Om du vill korrigera i olika iLearner-filer kan du ändra WSD-filen och uppdatera platsreferensen för den tränade modellen och distribuera den som en ny webbtjänst.

## <a name="automate-experiment-execution-and-deployment"></a>Automatisera körning och distribution av experiment
En viktig aspekt av ALM är att kunna automatisera körnings- och distributionsprocessen för programmet. I Azure Machine Learning Studio (klassisk) kan du åstadkomma detta med hjälp av [PowerShell-modulen](https://aka.ms/amlps). Här är ett exempel på steg från slutna till slutna steg som är relevanta för en vanlig ALM-automatiserad körning/distributionsprocess med hjälp av [Azure Machine Learning Studio (klassisk) PowerShell-modul](https://aka.ms/amlps). Varje steg är länkat till en eller flera PowerShell-kommando som du kan använda för att utföra det steget.

1. [Ladda upp en datauppsättning](https://github.com/hning86/azuremlps#upload-amldataset).
2. Kopiera ett utbildningsexperiment till arbetsytan från en [arbetsyta](https://github.com/hning86/azuremlps#copy-amlexperiment) eller från [Galleri](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)eller [importera](https://github.com/hning86/azuremlps#import-amlexperimentgraph) ett [exporterat](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment från lokal disk.
3. [Uppdatera datauppsättningen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i utbildningsexperimentet.
4. [Kör träningsexperimentet](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Främja den tränade modellen](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Kopiera ett förutsägelseexperiment](https://github.com/hning86/azuremlps#copy-amlexperiment) till arbetsytan.
7. [Uppdatera den tränade modellen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i det prediktiva experimentet.
8. [Kör det prediktiva experimentet](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Distribuera en webbtjänst](https://github.com/hning86/azuremlps#new-amlwebservice) från förutsägelseexperimentet.
10. Testa webbtjänsten [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) eller [BES-slutpunkten.](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)

## <a name="next-steps"></a>Nästa steg
* Ladda ned [Azure Machine Learning Studio (klassisk) PowerShell-modul](https://aka.ms/amlps) och börja automatisera dina ALM-uppgifter.
* Lär dig hur du [skapar och hanterar ett stort antal ML-modeller med bara ett enda experiment](create-models-and-endpoints-with-powershell.md) via PowerShell och omskolning API.
* Läs mer om [hur du distribuerar Azure Machine Learning-webbtjänster](deploy-a-machine-learning-web-service.md).
