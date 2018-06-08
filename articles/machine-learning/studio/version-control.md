---
title: ALM i Azure Machine Learning | Microsoft Docs
description: Tillämpa Metodtips för programhantering livscykel i Azure Machine Learning Studio
keywords: ALM AML, Azure ML, programhanteringen livscykel versionskontroll
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
ms.openlocfilehash: 49d1a228132cc220b30091481bb542623b1e222d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835873"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Livscykel för programhantering i Azure Machine Learning Studio
Azure Machine Learning Studio är ett verktyg för utveckling av machine learning-experiment som operationalized i plattformen Azure-molnet. Den kopplas som Visual Studio IDE och skalbar Molntjänsten till en enda plattform. Du kan införliva standard programmet livscykeln för hantering (ALM) praxis från versionshantering olika tillgångar för automatisk körning och distribution, i Azure Machine Learning Studio. Den här artikeln beskrivs några av alternativen och metoder.

## <a name="versioning-experiment"></a>Versionshantering experiment
Det finns två rekommenderade sätt att version experimenten. Du kan antingen förlitar sig på inbyggda körningshistoriken eller exportera experiment i JSON-format för att hantera den externt. Tillvägagångssätten levereras med dess- och nackdelar.

### <a name="experiment-snapshots-using-run-history"></a>Experiment ögonblicksbilder med Körningshistorik
I modellen körningen av den Azure Machine Learning Studio learning experiment, en oföränderlig ögonblicksbild av experimentet har skickats till jobbschemat när du klickar på **kör** i Redigeraren för experimentet. Om du vill visa den här listan över ögonblicksbilder klickar du på **Körningshistorik** i kommandofältet i vyn experiment editor.

![Kör tidigare](./media/version-control/runhistory.png)

Därefter kan du öppna ögonblicksbild i låst läge genom att klicka på namnet på experimentet när experimentet angavs för att köra och ögonblicksbilden togs. Observera att det första objektet i listan som representerar det aktuella experimentet, är i ett redigerbart tillstånd. Observera att varje ögonblicksbild kan ha olika Status inklusive klar (delvis kör), misslyckades, tillstånd, misslyckades (delvis kör), eller också utkast.

![Kör listan tidigare](./media/version-control/runhistorylist.png)

När det öppnas, kan du spara ögonblicksbild experiment som ett nytt experiment och sedan ändra den. Om din experiment ögonblicksbild innehåller tillgångar som tränade modeller, transformeringar eller datauppsättningar som har uppdaterade versioner, behåller ögonblicksbilden referenser till den ursprungliga versionen när ögonblicksbilden togs. Om du sparar den låsta ögonblicksbilden som ett nytt experiment Azure Machine Learning Studio identifierar förekomsten av en nyare version av dessa tillgångar och uppdateras automatiskt i nya försöket.

Om du tar bort experimentet bort alla ögonblicksbilder av försöket.

### <a name="exportimport-experiment-in-json-format"></a>Export/import experiment i JSON-format
Kör rapporthistorik behålla en oföränderlig version av experimentet i Azure Machine Learning Studio varje gång det har skickats för att köra. Du kan också spara en lokal kopia av experimentet och checka in din favorit källkontrollsystem, till exempel Team Foundation Server och skapa senare på ett experiment från den lokala filen igen. Du kan använda den [Azure Machine Learning PowerShell](http://aka.ms/amlps) kommandon [ *Export AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) och [ *importera AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) att göra det.

JSON-filen är en textrepresentation av experimentet diagram, vilket kan innebära en referens till tillgångar på arbetsytan, till exempel ett dataset eller en tränad modell. Den innehåller inte en serialiserad version av tillgången. Om du försöker importera JSON-dokumentet tillbaka till arbetsytan måste refererade tillgångar finnas med samma tillgång ID: N som refereras i experimentet. Annars kan du komma åt importerade experimentet.

## <a name="versioning-trained-model"></a>Versionshantering tränade modellen
En tränad modell i Azure Machine Learning serialiseras till ett format som kallas en iLearner-fil (`.iLearner`), och lagras i Azure Blob storage-konto som är kopplade till arbetsytan. Ett sätt att hämta en kopia av den iLearner-fil är via omtränings-API: et. [Den här artikeln](retrain-models-programmatically.md) beskriver hur omtränings-API: et fungerar. Anvisningar:

1. Ställ in experimentet utbildning.
2. Lägger till en web service utgående port träningsmodellmodulen eller modulen som producerar tränad modell, till exempel finjustera modellen Hyperparameter eller skapa R-modell.
3. Kör experimentet utbildning och distribuera det som en webbtjänst för utbildning av modellen.
4. Anropa BES-slutpunkten för webbtjänsten utbildning och ange önskade iLearner-filnamnet och Blob lagringskontoplatsen där den lagras.
5. Inhämta producerade iLearner-fil när BES-anropet har slutförts.

Ett annat sätt att hämta den iLearner-fil är via PowerShell-kommandot [ *Download AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Det blir enklare om du vill hämta en kopia av den iLearner-fil utan att behöva träna om modellen programmässigt.

När du har den iLearner-fil som innehåller den tränade modellen, kan du använda en egen strategi för versionshantering. Strategin kan vara så enkelt som tillämpades före/username@Domain som en namngivningskonvention och bara om du lämnar den iLearner-fil i Blob storage eller kopiera/importerar den till din systemet för versionskontroll.

Den sparade iLearner-fil kan sedan användas för poäng genom distribuerade webbtjänster.

## <a name="versioning-web-service"></a>Webbtjänst för versionshantering
Du kan distribuera två typer av webbtjänster från ett Azure Machine Learning-experiment. Klassiska webbtjänsten är direkt kopplade till experimentet samt arbetsytan. Den nya webbtjänsten använder Azure Resource Manager-ramverk och det inte längre används tillsammans med det ursprungliga experimentet eller på arbetsytan.

### <a name="classic-web-service"></a>Klassiska webbtjänst
Du kan dra nytta av web service endpoint konstruktion till version klassiska webbtjänsten. Här är en typisk flöde:

1. Från din prediktivt experiment kan du distribuera en ny klassiska webbtjänst, som innehåller en standardslutpunkt.
2. Du kan skapa en ny slutpunkt med namnet ep2 som visar den aktuella versionen av modellen experiment/tränats.
3. Du kan gå tillbaka och uppdatera din prediktivt experiment och tränad modell.
4. Du distribuerar prediktivt experiment, som sedan uppdaterar standardslutpunkten. Men det kommer inte att ändra ep2.
5. Du kan skapa en ytterligare slutpunkt med namnet ep3 som visar den nya versionen av experimentet och tränad modell.
6. Gå tillbaka till steg 3 om det behövs.

Du kan ha många slutpunkter har skapats i samma webbtjänsten med tiden. Varje slutpunkt representerar en tidpunkt i kopia av experimentet som innehåller point-in-time-versionen av den tränade modellen. Du kan sedan använda externa logik för att avgöra vilken slutpunkt att anropa, vilket effektivt innebär att välja en version av den tränade modellen för bedömningsprofil kör.

Du kan också skapa slutpunkter för många identiska webbtjänster och korrigering olika versioner av den iLearner-fil till slutpunkten för att uppnå samma effekt. [Den här artikeln](create-models-and-endpoints-with-powershell.md) förklarar i detalj hur du utför som.

### <a name="new-web-service"></a>Ny webbtjänst
Om du skapar en ny Azure Resource Manager-baserad webbtjänst, endpoint-konstruktion inte längre tillgängligt. Du kan i stället generera web service definition (WSD)-filer i JSON-format från din prediktivt experiment med hjälp av den [Export AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell-kommandot eller genom att använda den [ *Export AzureRmMlWebservice* ](https://msdn.microsoft.com/library/azure/mt767935.aspx) PowerShell-kommandot från en distribuerad Resource Manager-baserad webbtjänst.

När du har den exporterade WSD filen och kontroll över den kan distribuera du också WSD som en ny webbtjänst i en annan web service-plan i en annan Azure-region. Kontrollera att du anger rätt konto lagringskonfigurationen samt nya web service-plan-ID. Om du vill korrigera i olika iLearner filer du ändra WSD-filen och uppdatera platsreferensen av den tränade modellen och distribuera det som en ny webbtjänst.

## <a name="automate-experiment-execution-and-deployment"></a>Automatisera distribution och körning av experimentet
En viktig del av ALM ska kunna automatisera körningen och processen för distribution av programmet. I Azure Machine Learning, du kan göra detta med hjälp av den [PowerShell-modulen](http://aka.ms/amlps). Här är ett exempel på en slutpunkt till slutpunkt steg som är relevanta för en standard ALM automatisk körning/distributionsprocessen genom att använda den [Azure Machine Learning Studio PowerShell-modulen](http://aka.ms/amlps). Varje steg är kopplad till en eller flera PowerShell-kommandon som du kan använda för att utföra steget.

1. [Överför en datamängd](https://github.com/hning86/azuremlps#upload-amldataset).
2. Kopiera en träningsexperiment till arbetsytan från en [arbetsytan](https://github.com/hning86/azuremlps#copy-amlexperiment) eller från [galleriet](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), eller [importera](https://github.com/hning86/azuremlps#import-amlexperimentgraph) en [exporteras](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment från lokal disk.
3. [Uppdatera datauppsättningen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i utbildning försöket.
4. [Kör experimentet utbildning](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Flytta upp den tränade modellen](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Kopiera en prediktivt experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) till arbetsytan.
7. [Uppdatera den tränade modellen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i prediktivt experiment.
8. [Kör prediktivt experiment](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Distribuera en webbtjänst](https://github.com/hning86/azuremlps#new-amlwebservice) från prediktivt experiment.
10. Testa webbtjänsten [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) eller [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) slutpunkt.

## <a name="next-steps"></a>Nästa steg
* Hämta den [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) modulen och starta för att automatisera ALM-uppgifter.
* Lär dig hur du [skapa och hantera stort antal ML modeller med bara ett enda experiment](create-models-and-endpoints-with-powershell.md) via PowerShell och omtränings-API.
* Lär dig mer om [distribuerar Azure Machine Learning-webbtjänster](publish-a-machine-learning-web-service.md).
