---
Rubrik: Skapa flera modeller från en Studio-experiment-titleSuffix: Beskrivning av Azure Machine Learning Studio: Använda PowerShell för att skapa flera Machine Learning-modeller och webbtjänstslutpunkter med samma algoritm men olika utbildning datauppsättningar.
tjänster: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 04/04/2017
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>Använd PowerShell för att skapa Studio modeller och slutpunkter för webbtjänster från ett experiment

Här är ett vanligt problem för machine learning: Du vill skapa många modeller som har samma arbetsflöde för utbildning och använder samma algoritm. Men du vill att de har olika utbildning datauppsättningar som indata. Den här artikeln visar vi hur du gör detta i skala i Azure Machine Learning Studio med bara ett enda experiment.

Anta exempelvis att du äger en global cykel hyra franchise verksamhet. Du vill skapa en regressionsmodell för att förutsäga hyres-begäran baserat på historiska data. Du har 1 000 hyres-platser över hela världen och du har lagrat en datauppsättning för varje plats. De innehåller viktiga funktioner, till exempel datum, tid, väder och trafik som är specifika för varje plats.

Du kan träna din modell som en gång med hjälp av en sammanfogad version av alla datauppsättningar på alla platser. Men var och en av platserna har en unik-miljö. Så är en bättre metod att träna din regressionsmodell separat med hjälp av datauppsättningen för varje plats. På så sätt kan varje tränade modellen kan ta hänsyn till olika store-storlekar, volym, geografi, population, cykel-vänlig trafiken miljö med mera.

Som kan vara det bästa sättet, men du vill inte skapa 1 000 utbildning experiment i Azure Machine Learning med var och en representerar en unik plats. Förutom att en utmanande uppgiften också verkar ineffektiv eftersom varje experiment skulle ha samma komponenter förutom datauppsättning för träning.

Som tur är kan du kan göra detta med hjälp av den [Azure Machine Learning retraining API](retrain-models-programmatically.md) och automatisera uppgiften med [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Minska antalet platser från 1 000 till 10 för att göra ditt exempel snabbare. Men samma principer och procedurer som gäller för 1 000 platser. Men om du vill att träna från 1 000 datauppsättningar kan du köra följande PowerShell-skript parallellt. Hur du gör är utanför omfattningen för den här artikeln, men du kan hitta exempel på PowerShell flertrådsteknik på Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurera träningsexperimentet
Använd exempel [träningsexperiment](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) som finns i den [Cortana Intelligence-galleriet](http://gallery.cortanaintelligence.com). Öppna det här experimentet i din [Azure Machine Learning Studio](https://studio.azureml.net) arbetsyta.

> [!NOTE]
> För att kunna följa med i det här exemplet, kan du använda en standardarbetsytan i stället för en kostnadsfri arbetsyta. Du skapar en slutpunkt för varje kund - totalt 10 slutpunkter - och som kräver en standardarbetsytan eftersom en kostnadsfri arbetsyta är begränsat till 3 slutpunkter. Om du bara har en kostnadsfri arbetsyta bara ändra skripten så att endast th platser.
> 
> 

Experimentet använder en **importera Data** modul för att importera en datauppsättning för träning *customer001.csv* från ett Azure storage-konto. Vi antar att du har samlat in datauppsättningar för utbildning från alla cykel hyra platser och lagrat dem på samma plats för blob storage med filnamn som sträcker sig från *rentalloc001.csv* till *rentalloc10.csv*.

![image](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observera att en **Web Service utdata** modulen har lagts till i **Träningsmodell** modulen.
När det här experimentet har distribuerats som en webbtjänst slutpunkten som är associerade med att utdata returnerar den tränade modellen i formatet för en .ilearner-fil.

Observera också att du ställer in en web service-parameter som definierar URL som den **importdata** modul använder. På så sätt kan du använda parametern för att ange enskilda utbildning datauppsättningar för att träna modellen för varje plats.
Det finns andra sätt du kunnat göra detta. Du kan använda en SQL-fråga med en web service-parameter för att hämta data från en SQL Azure-databas. Du kan också använda en **Webbtjänstindata** modulen att i en datauppsättning till webbtjänsten.

![image](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu ska vi köra den här träningsexperiment med hjälp av standardvärdet *rental001.csv* som datamängd för träning. Om du visar utdata från den **Evaluate** modulen (klicka på utdata och välj **visualisera**), du kan se du få en vettigt prestanda av *AUC* = 0.91. Nu är du redo att distribuera en webbtjänst utanför den här träningsexperiment.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuera träningen och bedömning webbtjänster
Om du vill distribuera webbtjänsten utbildning, klickar du på den **konfigurera Web Service** under experimentets arbetsyta och välj **distribuera webbtjänsten**. Anropa den här webbtjänsten ”cykel hyra utbildning”.

Nu ska du distribuera bedömning av webbtjänsten.
Gör detta genom att klicka på **konfigurera Web Service** under arbetsytan och välj **förutsägande webbtjänsten**. Detta skapar en arbetsflödesbaserad experiment.
Du måste göra några mindre justeringar gör att den fungerar som en webbtjänst. Ta bort etikettkolumnen ”cnt” från indata och begränsa utdata till endast instans-id och motsvarande förutsägelsevärdet.

Du kan spara arbetet, kan du öppna den [förutsägelseexperiment](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) i galleriet som redan har förberetts.

Om du vill distribuera webbtjänsten kör förutsägbart experiment, och klicka sedan på den **distribuera webbtjänsten** knappen under arbetsytan. Namnge tjänsten bedömnings web ”cykel hyra bedömning”.

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

Nu du skapade 10 slutpunkter och alla innehåller samma tränas modellen tränats på *customer001.csv*. Du kan visa dem i Azure-portalen.

![image](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Uppdatera slutpunkterna för att använda separata tränings datauppsättningar med hjälp av PowerShell
Nästa steg är att uppdatera slutpunkterna med modeller som unikt tränats på varje kunds enskilda data. Men först måste du skapa dessa modeller från den **cykel hyra utbildning** webbtjänsten. Vi går tillbaka till den **cykel hyra utbildning** webbtjänsten. Du måste anropa dess slutpunkt för BES 10 gånger med 10 olika utbildning datauppsättningar för att skapa 10 olika modeller. Använd den **InovkeAmlWebServiceBESEndpoint** PowerShell-cmdlet för att göra detta.

Du måste också att ange autentiseringsuppgifter för blob storage-kontot till `$configContent`. Nämligen på fälten `AccountName`, `AccountKey`, och `RelativeLocation`. Den `AccountName` kan vara något av ditt kontonamn, som visas i den **Azure-portalen** (*Storage* fliken). När du klickar på ett lagringskonto, dess `AccountKey` kan hittas genom att trycka på den **hantera åtkomstnycklar** längst ned och kopiera den *primära åtkomstnyckel*. Den `RelativeLocation` är sökväg i förhållande till din lagring där en ny modell kommer att lagras. Exempelvis sökvägen `hai/retrain/bike_rental/` i nedanstående skript till en behållare med namnet `hai`, och `/retrain/bike_rental/` finns det undermappar. För närvarande kan du inte skapa undermappar via portalens användargränssnitt, men det finns [flera Azure Storage-Utforskare](../../storage/common/storage-explorers.md) som gör det möjligt att göra detta. Vi rekommenderar att du skapar en ny behållare i ditt storage för att lagra nya tränade modeller (.iLearner-filer) på följande sätt: ditt storage-sidan klickar du på den **Lägg till** knappen längst ned på sidan och ge den namnet `retrain`. Sammanfattningsvis nödvändiga ändringar till följande skript som hör till `AccountName`, `AccountKey`, och `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Som du ser ovan, i stället för att konstruera 10 olika BES jobbet json konfigurationsfilerna kan skapa du dynamiskt config-strängen i stället. Sedan skicka den till den *jobConfigString* -parametern för den **InvokeAmlWebServceBESEndpoint** cmdlet. Det är lätt behöver du behålla en kopia på disken.

Om allt går bra efter en stund bör du se 10 .iLearner filer från *model001.ilearner* till *model010.ilearner*, i ditt Azure storage-konto. Nu är du redo att uppdatera de 10 bedömning slutpunkter för webbtjänster med dessa modeller med hjälp av den **Patch AmlWebServiceEndpoint** PowerShell-cmdlet. Kom ihåg igen att du bara kan korrigera icke-standard-slutpunkter som du programmässigt skapat tidigare.

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

Detta bör köra ganska snabbt. När körningen är klar kommer skapades 10 förutsägelsewebbtjänster Tjänsteslutpunkter. Var och en innehåller en tränad modell unikt har tränats på datauppsättningen som är specifika för en Hyresplats, allt från ett enda träningsexperiment. Om du vill kontrollera detta, kan du anropa dessa slutpunkter med hjälp av den **InvokeAmlWebServiceRRSEndpoint** cmdlet, vilket ger dem med samma indata. Du kan förvänta att se olika förutsägelser eftersom modeller tränas med olika utbildning uppsättningar.

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
