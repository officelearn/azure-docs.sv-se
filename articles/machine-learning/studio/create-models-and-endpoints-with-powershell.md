---
title: "Skapa flera modeller från ett experiment | Microsoft Docs"
description: "Använda PowerShell för att skapa flera Machine Learning-modeller och web service-slutpunkter med samma algoritm men olika utbildning datauppsättningar."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: cc938fdaa6843f7c9e974d9b88a9b682b4678493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Skapa många Machine Learning-modeller och webbtjänstslutpunkter från ett experiment med PowerShell
Här är ett vanligt problem i machine learning: du vill skapa många modeller som har samma utbildning arbetsflöde och använda samma algoritm, men olika utbildning datauppsättningar som indata. Den här artikeln visar hur du gör detta på skalan i Azure Machine Learning Studio med bara ett enda experiment.

Anta exempelvis att du äger ett globala cykel uthyrning franchise företag. Du vill skapa en regressionsmodell för att förutsäga uthyrning-begäran som bygger på historiska data. Du har 1 000 uthyrning platser över hela världen och du har lagrat en datauppsättning för varje plats som innehåller viktiga funktioner, till exempel datum, tid, väder och trafik som är specifika för varje plats.

Du kan lära din modell som en gång med hjälp av en sammanfogad version av alla datauppsättningar på alla platser. Men eftersom var och en av dina platser har en unik miljö, en bättre metod är att träna din regressionsmodell separat med datauppsättningen för varje plats. På så sätt kan varje tränad modell kan ta hänsyn till olika store storlekar, volym, geografi, ifyllning, cykel eget trafik miljö, *etc.*.

Som kan vara det bästa sättet, men du vill inte skapa 1 000 utbildning experiment i Azure Machine Learning med var och en representerar en unik plats. Förutom att det är en överväldigande aktivitet, är det också verkar vara ganska ineffektiv eftersom varje experiment skulle ha samma komponenter förutom utbildning dataset.

Lyckligtvis vi kan göra detta med hjälp av den [Azure Machine Learning API omtränings](retrain-models-programmatically.md) och automatisera aktiviteten med [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Vi ska minska antalet platser från 1 000 till 10 för att göra exemplet körs snabbare. Men samma principer och procedurer som gäller för 1 000 platser. Den enda skillnaden är att du förmodligen vill tror följande PowerShell-skript som körs parallellt om du vill att träna från 1 000 datauppsättningar. Hur du gör är utanför omfattningen för den här artikeln, men du kan hitta exempel på PowerShell flertrådsteknik på Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Ställ in utbildning experimentet
Vi använder ett exempel [träningsexperiment](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) som vi redan har skapat i den [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Öppna experimentet i din [Azure Machine Learning Studio](https://studio.azureml.net) arbetsytan.

> [!NOTE]
> För att kunna följa det här exemplet kan du vill använda en standard arbetsyta i stället för en kostnadsfri arbetsyta. Vi ska skapa en slutpunkt för varje kund - 10-slutpunkter – totalt och som kräver en standard arbetsyta eftersom en kostnadsfri arbetsyta är begränsat till 3 slutpunkter. Om du bara har en kostnadsfri arbetsyta bara ändra skripten nedan så att bara 3 platser.
> 
> 

Experimentet använder en **importera Data** modulen att importera utbildning datauppsättningen *customer001.csv* från ett Azure storage-konto. Antar vi att vi har samlat in utbildning datauppsättningar från alla cykel uthyrning platser och lagrat dem på samma plats för blob storage med filnamn som sträcker sig från *rentalloc001.csv* till *rentalloc10.csv*.

![Bild](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observera att en **Web Service utdata** modulen har lagts till i **Träningsmodell** modul.
När experimentet distribueras som en webbtjänst slutpunkten som är kopplade till att utdata ska returnera den tränade modellen i formatet av en .ilearner-fil.

Även Observera att vi ställa in en web service-parameter för URL-Adressen som den **importera Data** modulen används. Detta gör att vi kan använda parametern för att ange enskilda utbildning datauppsättningar för att träna modellen för varje plats.
Det finns andra sätt som vi gick har gjort det, till exempel använder en SQL-fråga med en parameter för web service för att hämta data från en SQL Azure-databas, eller bara använda en **webbtjänst** modulen att skicka in en datamängd till webbtjänsten.

![Bild](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu ska vi kör utbildning experimentet använder standardvärdet *rental001.csv* som utbildning dataset. Om du visar utdata från den **utvärdera** modulen (klicka på utdata och välj **visualisera**), du kan se vi hämta ett hyfsad prestanda för *AUC* = 0.91. Vi är nu redo att distribuera en webbtjänst utanför utbildning experimentet.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuera utbildning och bedömningen webbtjänster
Om du vill distribuera webbtjänsten utbildning klickar du på den **konfigurera Web Service** under arbetsytan för experimentet och välj **distribuera webbtjänsten**. Anropa den här webbtjänsten ”” cykel uthyrning utbildning ”.

Nu ska vi distribuera bedömningsprofil webbtjänsten.
Detta gör vi klickar på **konfigurera Web Service** under arbetsytan och välj **förutsägande webbtjänsten**. Detta skapar ett bedömningsprofil experiment.
Vi behöver göra några mindre justeringar så att den fungerar som en webbtjänst som att ta bort etikett kolumnen ”cnt” från indata och utdata till endast instans-id och motsvarande begränsa förväntad värde.

Du kan spara arbetet, kan du bara öppna den [prediktivt experiment](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) i galleriet som redan förberetts.

Om du vill distribuera webbtjänsten kör prediktivt experiment och klicka sedan på den **distribuera webbtjänsten** knapp under arbetsytan. Bedömningsprofil webbtjänsten ”cykel uthyrning bedömningen” namn ”.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Skapa 10 slutpunkter för identiska webbtjänster med PowerShell
Den här webbtjänsten levereras med en standardslutpunkt. Men vi har inte intresserad standardslutpunkten eftersom den inte uppdateras. Vi behöver göra är att skapa 10 ytterligare slutpunkter, ett för varje plats. Vi ska göra detta med PowerShell.

Först måste konfigurerar vi våra PowerShell-miljö:

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

Nu har vi skapat 10 slutpunkter och alla innehålla samma tränad modell tränas på *customer001.csv*. Du kan visa dem i Azure-hanteringsportalen.

![Bild](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Uppdatera slutpunkterna för att använda separata tränings datauppsättningar med hjälp av PowerShell
Nästa steg är att uppdatera slutpunkterna med modeller tränas unikt på varje enskild kunddata. Men vi måste först skapa dessa modeller från den **cykel uthyrning utbildning** webbtjänsten. Vi går tillbaka till den **cykel uthyrning utbildning** webbtjänsten. Vi behöver anropa sin BES slutpunkt 10 gånger med 10 olika utbildning datauppsättningar för att producera 10 olika modeller. Vi använder den **InovkeAmlWebServiceBESEndpoint** PowerShell-cmdlet för att göra detta.

Du måste också ange autentiseringsuppgifter för blob storage-konto i `$configContent`, nämligen på fälten `AccountName`, `AccountKey` och `RelativeLocation`. Den `AccountName` kan vara något av ditt kontonamn som visas i den **klassiska Azure-hanteringsportalen** (*lagring* fliken). När du klickar på ett lagringskonto dess `AccountKey` hittar du genom att trycka på **hantera åtkomstnycklar** längst ned och kopiera den *primära åtkomstnyckeln*. Den `RelativeLocation` är sökväg i förhållande till din lagring där en ny modell ska lagras. Till exempel sökvägen `hai/retrain/bike_rental/` i skriptet nedan pekar på en behållare med namnet `hai`, och `/retrain/bike_rental/` är undermappar. För närvarande kan du skapa undermappar via portalen UI, men det finns [flera Azure-lagringsutforskare](../../storage/common/storage-explorers.md) som gör det möjligt att göra detta. Vi rekommenderar att du skapar en ny behållare i lagring att lagra nya tränade modeller (.ilearner-filer) på följande sätt: från lagringssidan klickar du på den **Lägg till** längst ned och ger den namnet `retrain`. Sammanfattningsvis nödvändiga ändringar i skriptet nedan berör `AccountName`, `AccountKey` och `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> BES-slutpunkten är det enda läget som stöds för den här åtgärden. RR kan inte användas för att framställa tränade modeller.
> 
> 

Som du ser ovan, i stället för att konstruera 10 olika BES jobbet json konfigurationsfiler, vi skapa config-sträng i stället och dynamiskt feed det till den *jobConfigString* parameter för den  **InvokeAmlWebServceBESEndpoint** cmdlet, eftersom det inte finns egentligen inget behov av att behålla en kopia på disken.

Om allt går bra efter en stund bör du se 10 .ilearner filer från *model001.ilearner* till *model010.ilearner*, i ditt Azure storage-konto. Nu vi är redo att uppdatera våra 10 bedömningen slutpunkter för webbtjänster med dessa modeller med hjälp av den **korrigering AmlWebServiceEndpoint** PowerShell-cmdlet. Kom ihåg igen att vi kan bara korrigering av icke-standard-slutpunkter som vi programmässigt skapade tidigare.

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

Detta bör köra ganska snabbt. När körningen har slutförts ska vi har skapat 10 förutsägande slutpunkter för webbtjänster, som innehåller en tränad modell tränas unikt på specifika dataset till en plats för uthyrning, allt från en enda träningsexperiment. Om du vill kontrollera detta, kan du anropa dessa slutpunkter som använder den **InvokeAmlWebServiceRRSEndpoint** cmdlet, vilket ger dem med samma indata och du bör se olika förutsägelse resultat eftersom modeller tränas med olika utbildning mängder.

## <a name="full-powershell-script"></a>Fullständig PowerShell-skript
Här är lista med fullständig källkoden:

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
