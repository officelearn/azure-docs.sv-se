---
title: ALM i Azure Machine Learning | Microsoft Docs
description: Använd Application Lifecycle Management bästa praxis i Azure Machine Learning Studio
keywords: Versionskontroll för ALM, AML, Azure ML, livscykel för programhantering
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.openlocfilehash: ff30afdcfebe51d914d2f7504ab3bf530309c222
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054589"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Livscykelhantering för program i Azure Machine Learning Studio
Azure Machine Learning Studio är ett verktyg för utveckling av machine learning-experiment som operationaliserat i Azure-molnplattformen. Det sammanfogas som Visual Studio IDE och skalbar molntjänst i en enda plattform. Du kan införliva standardmetoder Application Lifecycle Management (ALM) från versionshantering olika tillgångar för automatisk körning och distribution i Azure Machine Learning Studio. Den här artikeln beskrivs några av alternativen och metoder.

## <a name="versioning-experiment"></a>Versionshantering experiment
Det finns två rekommenderade sätt att version experiment. Du kan förlita dig på inbyggda körningshistoriken, eller så kan du exportera experiment i JSON-format för att hantera den externt. Varje metoden har sina för- och nackdelar.

### <a name="experiment-snapshots-using-run-history"></a>Experiment ögonblicksbilder med Körningshistorik
I körningsmodell för Azure Machine Learning Studio learning-experiment, skickas ett oföränderligt ögonblicksbild av experimentet till jobbschemat när du klickar på **kör** i Redigeraren för experimentet. Om du vill visa den här listan över ögonblicksbilder, klickar du på **Körningshistorik** i kommandofältet i vyn experiment redigeraren.

![Historik-knappen Kör](./media/version-control/runhistory.png)

Du kan sedan öppna ögonblicksbild i låst läge genom att klicka på namnet på experimentet när experimentet har skickats till körnings- och ögonblicksbilden togs. Observera att det första objektet i listan som representerar det aktuella experimentet, är i ett redigerbart tillstånd. Också inklusive klar (delvis kör), misslyckades, tillstånd, misslyckades (delvis kör), Lägg märke till att varje ögonblicksbild som kan finnas i olika Status eller utkast.

![Kör listan med Kommandohistorik](./media/version-control/runhistorylist.png)

När du har öppnat du spara ögonblicksbild experiment som ett nytt experiment och ändra den. Om experiment ögonblicksbilden innehåller material som inlärda modeller, transformeringar eller datauppsättningar som har uppdaterat versioner, behåller ögonblicksbilden referenser till den ursprungliga versionen när ögonblicksbilden togs. Om du sparar den låsta ögonblicksbilden som ett nytt experiment, Azure Machine Learning Studio identifierar förekomsten av en nyare version av dessa tillgångar och uppdaterar automatiskt i nya försöket.

Om du tar bort experimentet raderas alla ögonblicksbilder av försöket.

### <a name="exportimport-experiment-in-json-format"></a>Exportera/importera experiment i JSON-format
Körningshistorik ögonblicksbilder behålla en oföränderligt version av experiment i Azure Machine Learning Studio varje gång det har skickats för att köra. Du kan också spara en lokal kopia av experimentet och checka in den i din favorit källkontrollsystem, till exempel Team Foundation Server och skapa senare på ett experiment från den lokala filen igen. Du kan använda den [Azure Machine Learning PowerShell](http://aka.ms/amlps) commandlets [ *Export AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) och [  *Importera AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) att göra det.

JSON-filen är en textrepresentation av experimentdiagram, vilket kan innebära en referens till tillgångar på arbetsytan, till exempel en datauppsättning eller en tränad modell. Den innehåller inte en serialiserade version av tillgången. Om du försöker importera JSON-dokumentet tillbaka till arbetsytan måste refererade tillgångar redan finnas med samma tillgång ID: N som refereras till i experimentet. Annars kan du komma åt importerade experimentet.

## <a name="versioning-trained-model"></a>Versionshantering tränade modellen
En tränad modell i Azure Machine Learning är serialiserat till ett format som kallas en iLearner-fil (`.iLearner`), och lagras i Azure Blob storage-konto som är associerade med arbetsytan. Ett sätt att hämta en kopia av den iLearner-fil är via omtränings-API. [Den här artikeln](retrain-models-programmatically.md) förklarar hur du omtränings-API: et. De övergripande stegen:

1. Ställ in din träningsexperiment.
2. Lägg till en web service-utdataporten träningsmodellmodulen eller den modul som ger den tränade modellen, till exempel finjustera modell finjustering eller skapa R-modellen.
3. Kör experimentet utbildning och sedan distribuera den som en webbtjänst för modellen utbildning.
4. Anropa BES-slutpunkten för webbtjänsten utbildning och ange önskade iLearner filnamnet och blobblagringskontot där den kommer att lagras.
5. Inhämta producerade iLearner-fil när BES-anropet har slutförts.

Ett annat sätt att hämta den iLearner-fil är via PowerShell-kommandot [ *Download AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Det blir enklare om du bara vill hämta en kopia av den iLearner-fil utan att du behöver för att träna modellen programmässigt.

När du har den iLearner-fil som innehåller den tränade modellen kan kan du använda din egen strategi för versionshantering. Strategin kan vara lika enkelt som att tillämpa en i förväg/postfix som en namngivningskonvention och lämnar bara den iLearner-fil i Blob storage eller kopiera/importerar dem till din versionskontrollsystem.

Den sparade iLearner-fil kan sedan användas för bedömning via distribuerade webbtjänster.

## <a name="versioning-web-service"></a>Webbtjänst för versionshantering
Du kan distribuera två typer av webbtjänster från ett Azure Machine Learning-experiment. Klassisk webbtjänst är direkt kopplade till experimentet samt arbetsytan. Den nya webbtjänsten använder Azure Resource Manager-ramverk och det inte längre används tillsammans med det ursprungliga experimentet eller på arbetsytan.

### <a name="classic-web-service"></a>Klassisk webbtjänst
Du kan dra nytta av web service-slutpunkt-konstruktion till version en klassisk webbtjänst. Här är ett typiskt flöde:

1. Från din förutsägbart experiment kan du distribuera en ny klassiska webbtjänst, som innehåller en standardslutpunkt.
2. Du skapar en ny slutpunkt med namnet ep2 som visar den aktuella versionen av modellen experiment/tränas.
3. Du kan gå tillbaka och uppdatera din förutsägelseexperiment och tränade modellen.
4. Du distribuerar om förutsägelseexperiment, som sedan uppdaterar standardslutpunkten. Men detta påverkar inte ep2.
5. Du skapar en ytterligare slutpunkt med namnet ep3 som visar den nya versionen av experimentet och tränade modellen.
6. Gå tillbaka till steg 3 om det behövs.

Du kan ha många slutpunkter som skapats i samma webbtjänsten med tiden. Varje slutpunkt representerar en point-in-time-kopia av experimentet som innehåller den point-in-time-versionen av den tränade modellen. Du kan sedan använda externa logik för att avgöra vilken slutpunkt för att anropa, vilket effektivt innebär att välja en version av den tränade modellen för den bedömnings körningen.

Du kan också skapa slutpunkter för många identiska webbtjänster och korrigera olika versioner av den iLearner-fil till slutpunkten för att uppnå liknande effekt. [Den här artikeln](create-models-and-endpoints-with-powershell.md) förklarar i detalj hur du utför som.

### <a name="new-web-service"></a>Ny webbtjänst
Om du skapar en ny Azure Resource Manager-baserad webbtjänst, är endpoint-konstruktion inte längre tillgängliga. I stället du kan generera web service definition (WSD)-filer, i JSON-format från ditt förutsägbart experiment med hjälp av den [Export AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell-kommandot, eller genom att använda den [ *Export AzureRmMlWebservice* ](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice?view=azurermps-6.6.0) PowerShell-kommandot från en distribuerad Resource Manager-baserad webbtjänst.

När du har exporterat WSD fil- och kontroll över den version distribuera du även WSD som en ny webbtjänst i en annan web service-plan i en annan Azure-region. Se bara till att du anger rätt konto lagringskonfigurationen samt nya web service-plan-ID. Om du vill korrigera i olika iLearner filer du ändrar WSD-filen och uppdatera platsreferensen av den tränade modellen och distribuera den som en ny webbtjänst.

## <a name="automate-experiment-execution-and-deployment"></a>Körning av experiment och distribuera automatiskt
Det är en viktig aspekt av ALM ska kunna automatisera körning och processen för distribution av programmet. I Azure Machine Learning kan du göra detta med hjälp av den [PowerShell-modulen](http://aka.ms/amlps). Här är ett exempel på åtgärder för slutpunkt till slutpunkt som är relevanta för en standard ALM automatisk körning/distributionsprocessen genom att använda den [Azure Machine Learning Studio PowerShell-modulen](http://aka.ms/amlps). Varje steg är länkad till en eller flera PowerShell-kommandon som du kan använda för att utföra steget.

1. [Överföra en datauppsättning](https://github.com/hning86/azuremlps#upload-amldataset).
2. Kopiera ett träningsexperiment till arbetsytan från en [arbetsytan](https://github.com/hning86/azuremlps#copy-amlexperiment) eller från [galleriet](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), eller [importera](https://github.com/hning86/azuremlps#import-amlexperimentgraph) en [exporteras](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment från lokal disk.
3. [Uppdatera datauppsättningen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i träningsexperimentet.
4. [Kör träningsexperimentet](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Flytta upp den tränade modellen](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Kopiera ett förutsägelseexperiment](https://github.com/hning86/azuremlps#copy-amlexperiment) till arbetsytan.
7. [Uppdatera den tränade modellen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i förutsägbart experiment.
8. [Kör förutsägelseexperiment](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Distribuera en webbtjänst](https://github.com/hning86/azuremlps#new-amlwebservice) från förutsägbart experiment.
10. Testa webbtjänsten [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) eller [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) slutpunkt.

## <a name="next-steps"></a>Nästa steg
* Ladda ned den [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) modulen och börja automatisera dina ALM-hanteringsuppgifter.
* Lär dig hur du [skapa och hantera stora antal ML-modeller med bara ett enda experiment](create-models-and-endpoints-with-powershell.md) via PowerShell och träna API.
* Läs mer om [distribuera Azure Machine Learning-webbtjänster](publish-a-machine-learning-web-service.md).
