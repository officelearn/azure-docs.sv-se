---
title: Träna och distribuera en webbtjänst
titleSuffix: Azure Machine Learning Studio
description: Lär dig hur du uppdaterar en webbtjänst för att använda en nyligen tränade machine learning-modell i Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: b57dd40c8610953563a3d5b8861e144d775b4eb7
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330519"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Träna och distribuera en modell för maskininlärning

Träna är ett sätt så att machine learning-modeller hålla korrekta och baserat på de mest relevanta data som är tillgängliga. Den här artikeln visar hur du tränar om och distribuera en machine learning-modell som en ny webbtjänst i Studio. Om du vill omtrimma en klassisk webbtjänst [visa i den här artikeln.](retrain-classic-web-service.md)

Den här artikeln förutsätter att du redan har en förutsägbar webbtjänst som har distribuerats. Om du inte redan har en förutsägbar webbtjänst [Lär dig hur du distribuerar en Studio-webbtjänst här.](publish-a-machine-learning-web-service.md)

Du ska följa stegen nedan för att träna och distribuera en machine learning ny webbtjänst:

1. Distribuera en **omtränings-webbtjänst**
1. Skapa en ny modell med din **omtränings-webbtjänst**
1. Uppdatera dina befintliga **förutsägelseexperiment** att använda den nya modellen

## <a name="deploy-the-retraining-web-service"></a>Distribuera omtränings webbtjänsten

En omtränings webbtjänst kan du träna din modell med en ny uppsättning parametrar som nya data, och spara den till senare. När du ansluter en **Web Service utdata** till en **Träningsmodell**, träningsexperimentet matar ut en ny modell som du kan använda.

Använd följande steg för att distribuera en omtränings webbtjänst:

1. Ansluta en **Web Service inkommande** modul till dina data-indata. Normalt kan du se till att dina indata bearbetas på samma sätt som den ursprungliga informationen för utbildning.
1. Ansluta en **Web Service utdata** modulen till utdataporten för din **Träningsmodell**.
1. Om du har en **utvärdera modell** modulen, som du kan ansluta en **Web Service utdata** modulen att mata ut utvärderingsresultaten
1. Kör experimentet.

    När du har kört experimentet bör resulterande arbetsflödet likna följande bild:

    ![Resulterande arbetsflöde](media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nu kan distribuera du träningsexperimentet som en omtränings webbtjänst som returnerar en tränad modell och utvärderingsresultat av modellen.

1. Längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service**
1. Välj **distribuera webbtjänst [nyhet]**. Azure Machine Learning Web Services-portalen öppnas på den **distribuera webbtjänsten** sidan.
1. Skriv ett namn för din webbtjänst och välj en betalningsplan.
1. Välj **Distribuera**.

## <a name="retrain-the-model"></a>Träna modellen

Det här exemplet använder vi C# för att skapa omtränings-program. Du kan också använda exempelkod för Python eller R för att åstadkomma detta.

Använd följande steg för att anropa omtränings-API: erna:

1. Skapa en C# konsolapp i Visual Studio: **Ny** > **projekt** > **Visual C#**   >  **Windows Classic Desktop**  >   **Konsolprogram (.NET Framework)**.
1. Logga in på Machine Learning Web Services-portalen.
1. Klicka på den webbtjänst som du arbetar med.
1. Klicka på **förbrukar**.
1. Längst ned på den **förbruka** sidan den **exempelkoden** klickar du på **Batch**.
1. Kopiera C# exempelkoden för batchkörning och klistra in den i filen Program.cs. Se till att namnområdet förblir intakta.

Lägg till NuGet-paketet system.NET.http.Formatting, enligt vad som anges i kommentarerna. Om du vill lägga till referensen till Microsoft.WindowsAzure.Storage.dll, kan du behöva installera den [-klientbiblioteket för Azure Storage-tjänsterna](https://www.nuget.org/packages/WindowsAzure.Storage).

Följande skärmbild visar de **förbruka** i Azure Machine Learning Web Services-portalen.

![Använda sidan](media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Uppdatera apikey deklaration

Leta upp den **apikey** deklarationen:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

I den **grundläggande förbrukning info** delen av den **förbruka** sidan, leta upp den primära nyckeln och kopiera den till den **apikey** deklaration.

### <a name="update-the-azure-storage-information"></a>Uppdatera informationen om Azure Storage

Exempelkoden BES laddar upp en fil från en lokal enhet (till exempel ”C:\temp\CensusInput.csv”) till Azure Storage, bearbetar dessa och skriver resultatet tillbaka till Azure Storage.

1. Logga in på Azure-portalen
1. I den vänstra kolumnen klickar du på **fler tjänster**, Sök efter **lagringskonton**, och markera den.
1. Välj en för att lagra retrained modellen från listan över lagringskonton.
1. I den vänstra kolumnen klickar du på **åtkomstnycklar**.
1. Kopiera och spara den **primära åtkomstnyckel**.
1. I den vänstra kolumnen klickar du på **behållare**.
1. Välj en befintlig behållare eller skapa en ny och spara namnet.

Leta upp den *StorageAccountName*, *StorageAccountKey*, och *StorageContainerName* deklarationer, och uppdatera de värden som du sparade från portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Du måste också kontrollera att indatafilen är tillgänglig på den plats som du anger i koden.

### <a name="specify-the-output-location"></a>Ange platsen för utdata

När du anger platsen i begäran-nyttolasten, tillägg till den fil som har angetts i *RelativeLocation* måste anges som `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Här är ett exempel på omtränings utdata:

![Omtränings utdata](media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Utvärdera omtränings resultaten

När du kör programmet innehåller utdata URL: en och signaturtoken för delad åtkomst som krävs för att komma åt utvärderingar.

Du kan se prestandaresultat retrained modellen genom att kombinera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från utdataresultat som för *output2* och klistra in den fullständiga URL: en i webbläsarens adressfält.

Granska resultaten för att avgöra om den nyligen tränade modellen presterar bättre än den befintliga påverkas.

Spara den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från utdataresultat som.

## <a name="update-the-predictive-experiment"></a>Uppdatera förutsägbart experiment

### <a name="sign-in-to-azure-resource-manager"></a>Logga in till Azure Resource Manager

Först måste logga in på ditt Azure-konto från PowerShell-miljö med hjälp av den [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet.

### <a name="get-the-web-service-definition-object"></a>Hämta objektet för Web Service Definition

Hämta sedan Web Service Definition objektet genom att anropa den [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) cmdlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Kör cmdleten Get-AzureRmMlWebService utan några parametrar för att visa webbtjänsterna i din prenumeration för att fastställa resursgruppens namn för en befintlig webbtjänst. Leta upp webbtjänsten och titta sedan på dess webbtjänst-ID. Namnet på resursgruppen är det fjärde elementet i ID, efter den *resourceGroups* element. I följande exempel är resursgruppens namn standard-MachineLearning-Usasödracentrala.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Du kan också för att fastställa resursgruppens namn för en befintlig webbtjänst, logga in på Azure Machine Learning Web Services-portalen. Välj webbtjänsten. Resursgruppens namn är det femte elementet i URL: en för webbtjänst, efter den *resourceGroups* element. I följande exempel är resursgruppens namn standard-MachineLearning-Usasödracentrala.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Exportera Web Service Definition-objektet som JSON

Om du vill ändra definitionen av den tränade modellen att använda den nyligen tränade modellen, måste du först använda den [Export AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) cmdlet för att exportera den till en JSON-fil.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Uppdatera referensen till ilearner-blob

Leta upp den [tränade modellen] i resurser, uppdatera den *uri* värde i den *locationInfo* nod med URI: N för den ilearner-blobben. URI: N genereras genom att kombinera den *BaseLocation* och *RelativeLocation* från utdata för BES träna anrop.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importera JSON till ett objekt för Web Service Definition

Använd den [Import AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) cmdlet för att omvandla den ändrade JSON-filen tillbaka till ett Web Service Definition-objekt som du kan använda för att uppdatera predicative experimentet.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Uppdatera webbtjänsten

Använd slutligen den [uppdatering AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) cmdlet för att uppdatera förutsägbart experiment.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du hanterar webbtjänster och hålla reda på flera experiment körs, finns i följande artiklar:

* [Utforska Web Services-portalen](manage-new-webservice.md)
* [Hantera iterationer av experiment](manage-experiment-iterations.md)
