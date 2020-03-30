---
title: Skapa flera slutpunkter för en modell
titleSuffix: ML Studio (classic) - Azure
description: Använd PowerShell för att skapa flera Machine Learning-modeller och slutpunkter för webbtjänster med samma algoritm men olika träningsdatauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218179"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Använda PowerShell för att skapa Studio-modeller (klassiska) modeller och slutpunkter för webbtjänster från ett experiment

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Här är ett vanligt maskininlärningsproblem: Du vill skapa många modeller som har samma träningsarbetsflöde och använder samma algoritm. Men du vill att de ska ha olika träningsdatauppsättningar som indata. Den här artikeln visar hur du gör detta i stor skala i Azure Machine Learning Studio (klassisk) med bara ett enda experiment.

Anta till exempel att du äger en global franchiseverksamhet för cykeluthyrning. Du vill skapa en regressionsmodell för att förutsäga hyresefterfrågan baserat på historiska data. Du har 1 000 uthyrningsplatser över hela världen och du har samlat in en datauppsättning för varje plats. De innehåller viktiga funktioner som datum, tid, väder och trafik som är specifika för varje plats.

Du kan träna din modell en gång med hjälp av en sammanslagen version av alla datauppsättningar på alla platser. Men var och en av dina platser har en unik miljö. Så en bättre metod skulle vara att träna din regressionsmodell separat med hjälp av datauppsättningen för varje plats. På så sätt kan varje utbildad modell ta hänsyn till de olika butiksstorlekarna, volym, geografi, befolkning, cykelvänlig trafikmiljö med mera.

Det kan vara den bästa metoden, men du vill inte skapa 1 000 utbildningsexperiment i Azure Machine Learning Studio (klassisk) där var och en representerar en unik plats. Förutom att vara en överväldigande uppgift, verkar det också ineffektivt eftersom varje experiment skulle ha alla samma komponenter utom för utbildning datauppsättning.

Lyckligtvis kan du åstadkomma detta genom att använda [Azure Machine Learning Studio (klassisk) omskolning API](/azure/machine-learning/studio/retrain-machine-learning-model) och automatisera uppgiften med Azure Machine Learning Studio [(klassisk) PowerShell](powershell-module.md).

> [!NOTE]
> Om du vill att provet ska gå snabbare minskar du antalet platser från 1 000 till 10. Men samma principer och förfaranden gäller för 1000 platser. Men om du vill träna från 1 000 datauppsättningar kanske du vill köra följande PowerShell-skript parallellt. Hur man gör det är utanför ramen för den här artikeln, men du kan hitta exempel på PowerShell flertrådning på Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurera utbildningsexperimentet
Använd [exempelträningsexperimentet](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) som finns i [Cortana Intelligence Gallery](https://gallery.azure.ai). Öppna det här experimentet i din [Azure Machine Learning Studio -arbetsyta (klassisk).](https://studio.azureml.net)

> [!NOTE]
> Om du vill följa med i det här exemplet kanske du vill använda en standardarbetsyta i stället för en ledig arbetsyta. Du skapar en slutpunkt för varje kund - för totalt 10 slutpunkter - och det kräver en standardarbetsyta eftersom en ledig arbetsyta är begränsad till 3 slutpunkter.
> 
> 

Experimentet använder en **importdatamodul** för att importera utbildningsdatauppsättningen *customer001.csv* från ett Azure-lagringskonto. Låt oss anta att du har samlat in utbildningsdatauppsättningar från alla cykeluthyrningsplatser och lagrat dem på samma bloblagringsplats med filnamn som sträcker sig från *rentalloc001.csv* till *rentalloc10.csv*.

![Läsarmodul importerar data från en Azure-blob](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observera att en **webbtjänstutdatamodul** har lagts till i modulen **Tågmodell.**
När det här experimentet distribueras som en webbtjänst returnerar slutpunkten som är associerad med utdata den tränade modellen i formatet för en .ilearner-fil.

Observera också att du ställer in en webbtjänstparameter som definierar url:en som modulen **Importera data** använder. På så sätt kan du använda parametern för att ange individuella träningsdatauppsättningar för att träna modellen för varje plats.
Det finns andra sätt du kunde ha gjort detta. Du kan använda en SQL-fråga med en webbtjänstparameter för att hämta data från en SQL Azure-databas. Du kan också använda en **webbtjänstinmatningsmodul** för att skicka en datauppsättning till webbtjänsten.

![En tränad modellmodul matas ut till en utdatamodul för webbtjänsten](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu ska vi köra det här utbildningsexperimentet med standardvärdet *rental001.csv* som träningsdatauppsättning. Om du visar utdata från **modulen Utvärdera** (klicka på utdata och välj **Visualisera)** kan du se att du får en anständig prestanda *för AUC* = 0,91. Nu är du redo att distribuera en webbtjänst från det här utbildningsexperimentet.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuera webbtjänster för utbildning och poängsättning
Om du vill distribuera webbtjänsten för utbildning klickar du på knappen **Konfigurera webbtjänst** under arbetsytan och väljer **Distribuera webbtjänst**. Kalla denna webbtjänst "Bike Rental Training".

Nu måste du distribuera bedömningswebbtjänsten.
Det gör du genom att klicka på **Konfigurera webbtjänst** under arbetsytan och välja **Förutsägande webbtjänst**. Detta skapar ett bedömningsexperiment.
Du måste göra några mindre justeringar för att få det att fungera som en webbtjänst. Ta bort etikettkolumnen "cnt" från indata och begränsa utdata till endast instans-ID och motsvarande förväntade värde.

Om du vill spara det arbetet kan du öppna det [förutsägelseexperiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) i galleriet som redan har förberetts.

Om du vill distribuera webbtjänsten kör du förutsägelseexperimentet och klickar sedan på knappen **Distribuera webbtjänst** under arbetsytan. Namnge poängwebbtjänsten "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Skapa 10 identiska webbtjänstslutpunkter med PowerShell
Den här webbtjänsten levereras med en standardslutpunkt. Men du är inte lika intresserad av standardslutpunkten eftersom den inte kan uppdateras. Vad du behöver göra är att skapa ytterligare 10 slutpunkter, en för varje plats. Du kan göra detta med PowerShell.

Först konfigurerar du PowerShell-miljön:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Kör sedan följande PowerShell-kommando:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Nu har du skapat 10 slutpunkter och de innehåller alla samma utbildade modell som tränas på *customer001.csv*. Du kan visa dem i Azure-portalen.

![Visa listan över tränade modeller i portalen](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Uppdatera slutpunkterna för att använda separata träningsdatauppsättningar med PowerShell
Nästa steg är att uppdatera slutpunkterna med modeller som är unikt utbildade på varje kunds individuella data. Men först måste du producera dessa modeller från **Bike Rental Training** webbtjänst. Låt oss gå tillbaka till **Bike Rental Training** webbtjänst. Du måste anropa bes-slutpunkten 10 gånger med 10 olika träningsdatauppsättningar för att kunna producera 10 olika modeller. Använd **cmdleten InovkeAmlWebServiceBESEndpoint** PowerShell för att göra detta.

Du måste också ange autentiseringsuppgifter för `$configContent`ditt blob storage-konto i . Nämligen på `AccountName`fälten `AccountKey`, `RelativeLocation`, och . Det `AccountName` kan vara ett av dina kontonamn, vilket visas i **fliken Azure-portal** (*Lagring).* När du klickar på ett `AccountKey` lagringskonto, kan dess hittas genom att trycka på **knappen Hantera åtkomsttangenter** längst ner och kopiera *den primära åtkomstnyckeln*. Det `RelativeLocation` är sökvägen i förhållande till din lagring där en ny modell ska lagras. Sökvägen `hai/retrain/bike_rental/` i följande skript pekar till exempel `hai`på `/retrain/bike_rental/` en behållare med namnet och är undermappar. För närvarande kan du inte skapa undermappar via portalgränssnittet, men det finns [flera Azure Storage Explorers](../../storage/common/storage-explorers.md) som gör att du kan göra det. Vi rekommenderar att du skapar en ny behållare i ditt lagringsutrymme för att lagra de nya tränade modellerna (.iLearner-filer) `retrain`på följande sätt: från lagringssidan klickar du på knappen Lägg **till** längst ned och namnger den . Sammanfattningsvis gäller de nödvändiga ändringarna av `AccountName` `AccountKey`följande `RelativeLocation` skript`"retrain/model' + $seq + '.ilearner"`, och (: ).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> BES-slutpunkten är det enda läge som stöds för den här åtgärden. RRS kan inte användas för att producera utbildade modeller.
> 
> 

Som du kan se ovan, istället för att konstruera 10 olika BES jobbkonfiguration json filer, du dynamiskt skapa config strängen istället. Mata sedan den till parametern *jobConfigString* i cmdleten **InvokeAmlWebServceBESEndpoint.** Det finns egentligen ingen anledning att ha en kopia på disken.

Om allt går bra, efter ett tag bör du se 10 .iLearner filer, från *model001.ilearner* till *model010.ilearner*, i ditt Azure-lagringskonto. Nu är du redo att uppdatera slutpunkterna för 10 bedömningswebbtjänsten med dessa modeller med hjälp av **Cmdleten Patch-AmlWebServiceEndpoint** PowerShell. Kom ihåg igen att du bara kan korrigera de slutpunkter som inte är standard som du skapade programmässigt tidigare.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Detta bör köra ganska snabbt. När körningen är klar har du skapat 10 prediktiva webbtjänstslutpunkter. Var och en kommer att innehålla en tränad modell unikt tränad på datauppsättningen som är specifik för en uthyrningsplats, allt från ett enda utbildningsexperiment. För att kontrollera detta kan du prova att anropa dessa slutpunkter med hjälp av **cmdleten InvokeAmlWebServiceRRSEndpoint** och ge dem samma indata. Du bör förvänta dig att se olika förutsägelse resultat eftersom modellerna är utbildade med olika träningsuppsättningar.

## <a name="full-powershell-script"></a>Fullständigt PowerShell-skript
Här är listan över den fullständiga källkoden:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
