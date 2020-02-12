---
title: Skapa flera slut punkter för en modell
titleSuffix: ML Studio (classic) - Azure
description: Använda PowerShell för att skapa flera Machine Learning-modeller och webbtjänstslutpunkter med samma algoritm men olika utbildning datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 9c1557efd87bf75ec59c9b65112b2bb3d0c678db
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153629"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Använd PowerShell för att skapa Studio-modeller (klassiska) och webb tjänst slut punkter från ett experiment

Här är ett vanligt problem för machine learning: du vill skapa många modeller som har samma arbetsflöde för utbildning och använder samma algoritm. Men du vill att de har olika utbildning datauppsättningar som indata. Den här artikeln visar hur du gör detta i skala i Azure Machine Learning Studio (klassisk) med bara ett enda experiment.

Anta exempelvis att du äger en global cykel hyra franchise verksamhet. Du vill skapa en regressionsmodell för att förutsäga hyres-begäran baserat på historiska data. Du har 1 000 hyres-platser över hela världen och du har lagrat en datauppsättning för varje plats. De innehåller viktiga funktioner, till exempel datum, tid, väder och trafik som är specifika för varje plats.

Du kan träna din modell som en gång med hjälp av en sammanfogad version av alla datauppsättningar på alla platser. Men var och en av platserna har en unik-miljö. Så är en bättre metod att träna din regressionsmodell separat med hjälp av datauppsättningen för varje plats. På så sätt kan varje tränade modellen kan ta hänsyn till olika store-storlekar, volym, geografi, population, cykel-vänlig trafiken miljö med mera.

Det kan vara den bästa metoden, men du vill inte skapa 1 000-tränings experiment i Azure Machine Learning Studio (klassisk) med var och en representerar en unik plats. Förutom att en utmanande uppgiften också verkar ineffektiv eftersom varje experiment skulle ha samma komponenter förutom datauppsättning för träning.

Lyckligt vis kan du göra detta med hjälp av [API: et för Azure Machine Learning Studio (klassisk)](/azure/machine-learning/studio/retrain-machine-learning-model) och automatisera uppgiften med [Azure Machine Learning Studio (klassisk) PowerShell](powershell-module.md).

> [!NOTE]
> Minska antalet platser från 1 000 till 10 för att göra ditt exempel snabbare. Men samma principer och procedurer som gäller för 1 000 platser. Men om du vill att träna från 1 000 datauppsättningar kan du köra följande PowerShell-skript parallellt. Hur du gör är utanför omfattningen för den här artikeln, men du kan hitta exempel på PowerShell flertrådsteknik på Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurera träningsexperimentet
Använd exemplet på [inlärnings experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) som finns i [Cortana Intelligence Gallery](https://gallery.azure.ai). Öppna det här experimentet i arbets ytan [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) .

> [!NOTE]
> För att kunna följa med i det här exemplet, kan du använda en standardarbetsytan i stället för en kostnadsfri arbetsyta. Du skapar en slutpunkt för varje kund - totalt 10 slutpunkter - och som kräver en standardarbetsytan eftersom en kostnadsfri arbetsyta är begränsat till 3 slutpunkter.
> 
> 

Experimentet använder modulen **Importera data** för att importera data uppsättningen *customer001. csv* från ett Azure Storage-konto. Vi antar att du har samlat in inlärnings data uppsättningar från alla cykel uthyrnings platser och lagrat dem på samma Blob Storage-plats med fil namn som sträcker sig från *rentalloc001. csv* till *rentalloc10. csv*.

![Läsar modulen importerar data från en Azure-Blob](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observera att en **webb tjänst utmatnings** modul har lagts till modulen **träna modell** .
När det här experimentet har distribuerats som en webbtjänst slutpunkten som är associerade med att utdata returnerar den tränade modellen i formatet för en .ilearner-fil.

Observera också att du konfigurerar en webb tjänst parameter som definierar den URL som används av modulen **Importera data** . På så sätt kan du använda parametern för att ange enskilda utbildning datauppsättningar för att träna modellen för varje plats.
Det finns andra sätt du kunnat göra detta. Du kan använda en SQL-fråga med en web service-parameter för att hämta data från en SQL Azure-databas. Du kan också använda en modul för **indata för webb tjänst** för att skicka in en data uppsättning till webb tjänsten.

![En utbildad modell modul ger utdata till en webb tjänst utmatnings modul](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu ska vi köra det här övnings experimentet med hjälp av standardvärdet *rental001. csv* som inlärnings data uppsättning. Om du visar utdata från **evaluate** -modulen (klicka på utdata och väljer **visualisera**) kan du se att du får en vettigt prestanda på *AUC* = 0,91. Nu är du redo att distribuera en webbtjänst utanför den här träningsexperiment.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuera träningen och bedömning webbtjänster
Du distribuerar utbildnings webb tjänsten genom att klicka på knappen **Konfigurera webb tjänst** under experimentets arbets yta och välja **distribuera webb tjänst**. Anropa den här webbtjänsten ”cykel hyra utbildning”.

Nu ska du distribuera bedömning av webbtjänsten.
Det gör du genom att klicka på **Konfigurera webb tjänsten** under arbets ytan och välja **förutsägbar webb tjänst**. Detta skapar en arbetsflödesbaserad experiment.
Du måste göra några mindre justeringar gör att den fungerar som en webbtjänst. Ta bort etikettkolumnen ”cnt” från indata och begränsa utdata till endast instans-id och motsvarande förutsägelsevärdet.

För att du ska kunna spara pengar kan du öppna det [förutsägande experimentet](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) i galleriet som redan har förberetts.

Om du vill distribuera webb tjänsten kör du förutsägelse experimentet och klickar sedan på knappen **distribuera webb tjänst** under arbets ytan. Namnge tjänsten bedömnings web ”cykel hyra bedömning”.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Skapa slutpunkter för 10 identiska webbtjänster med PowerShell
Den här webbtjänsten levereras med en standardslutpunkt. Men du är inte intresserad standardslutpunkten eftersom det inte kan uppdateras. Vad du behöver göra är att skapa 10 ytterligare slutpunkter, en för varje plats. Du kan göra detta med PowerShell.

Först måste konfigurera du PowerShell-miljö:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Kör följande PowerShell-kommando:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Nu har du skapat 10 slut punkter och alla innehåller samma utbildade modell som har tränats på *customer001. csv*. Du kan visa dem i Azure-portalen.

![Visa listan över tränade modeller i portalen](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Uppdatera slutpunkterna för att använda separata tränings datauppsättningar med hjälp av PowerShell
Nästa steg är att uppdatera slutpunkterna med modeller som unikt tränats på varje kunds enskilda data. Men först måste du skapa dessa modeller från utbildnings webb tjänsten för **cykel uthyrning** . Nu ska vi gå tillbaka till webb tjänsten för **cykel uthyrnings utbildning** . Du måste anropa dess slutpunkt för BES 10 gånger med 10 olika utbildning datauppsättningar för att skapa 10 olika modeller. Använd **InovkeAmlWebServiceBESEndpoint** PowerShell-cmdleten för att göra detta.

Du måste också ange autentiseringsuppgifter för ditt Blob Storage-konto i `$configContent`. Dvs, i fälten `AccountName`, `AccountKey`och `RelativeLocation`. `AccountName` kan vara ett av dina konto namn, som du ser i **Azure Portal** (fliken*lagring* ). När du klickar på ett lagrings konto kan du hitta dess `AccountKey` genom att trycka på knappen **Hantera åtkomst nycklar** längst ned och kopiera den *primära åtkomst nyckeln*. `RelativeLocation` är sökvägen i förhållande till lagrings utrymmet där en ny modell kommer att lagras. Till exempel pekar sökvägen `hai/retrain/bike_rental/` i följande skript till en behållare med namnet `hai`och `/retrain/bike_rental/` är undermappar. För närvarande kan du inte skapa undermappar via portalens användar gränssnitt, men det finns [flera Azure Storage Explorer](../../storage/common/storage-explorers.md) som gör det möjligt att göra det. Vi rekommenderar att du skapar en ny behållare i lagringen för att lagra nya utbildade modeller (. iLearner-filer) på följande sätt: från din lagrings sida klickar du på knappen **Lägg till** längst ned och namnger den `retrain`. I sammanfattning är de nödvändiga ändringarna i följande skript kopplade till `AccountName`, `AccountKey`och `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> BES-slutpunkten är det enda läget som stöds för den här åtgärden. RRS kan inte användas för att producera anpassade modeller.
> 
> 

Som du ser ovan, i stället för att konstruera 10 olika BES jobbet json konfigurationsfilerna kan skapa du dynamiskt config-strängen i stället. Mata sedan in den till parametern *jobConfigString* för **InvokeAmlWebServceBESEndpoint** -cmdleten. Det är lätt behöver du behålla en kopia på disken.

Om allt går bra, efter ett tag kan du se 10. iLearner-filer, från *model001. iLearner* till *model010. iLearner*, i ditt Azure Storage-konto. Nu är du redo att uppdatera webb tjänst slut punkterna för 10 Poäng med dessa modeller med hjälp av PowerShell-cmdleten **patch-AmlWebServiceEndpoint** . Kom ihåg igen att du bara kan korrigera icke-standard-slutpunkter som du programmässigt skapat tidigare.

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

Detta bör köra ganska snabbt. När körningen är klar kommer skapades 10 förutsägelsewebbtjänster Tjänsteslutpunkter. Var och en innehåller en tränad modell unikt har tränats på datauppsättningen som är specifika för en Hyresplats, allt från ett enda träningsexperiment. Om du vill kontrol lera detta kan du prova att anropa dessa slut punkter med **InvokeAmlWebServiceRRSEndpoint** -cmdleten och ge dem samma indata. Du kan förvänta att se olika förutsägelser eftersom modeller tränas med olika utbildning uppsättningar.

## <a name="full-powershell-script"></a>Fullständig PowerShell-skript
Här är listan med den fullständiga källkoden:

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
