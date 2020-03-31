---
title: Omskola en webbtjänst
titleSuffix: ML Studio (classic) - Azure
description: Lär dig hur du uppdaterar en webbtjänst för att använda en nyligen tränad maskininlärningsmodell i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 218c1c98a2ed775ae86c1657156991879708cc7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217940"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Omskola och distribuera en maskininlärningsmodell

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Omskolning är ett sätt att se till att maskininlärningsmodeller förblir korrekta och baseras på de mest relevanta tillgängliga uppgifterna. Den här artikeln visar hur du omskola och distribuera en maskininlärningsmodell som en ny webbtjänst i Studio (klassisk). Om du vill träna om en klassisk webbtjänst [kan du visa den här artikeln.](retrain-classic-web-service.md)

Den här artikeln förutsätter att du redan har en prediktiv webbtjänst distribuerad. Om du inte redan har en prediktiv webbtjänst [kan du läsa om hur du distribuerar en studiowebbtjänst (klassisk) här.](deploy-a-machine-learning-web-service.md)

Du följer dessa steg för att omskola och distribuera en ny webbtjänst för maskininlärning:

1. Distribuera en **omskolningswebbtjänst**
1. Träna en ny modell med hjälp av din **omskolningswebbtjänst**
1. Uppdatera ditt befintliga **förutsägelseexperiment** för att använda den nya modellen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Distribuera webbtjänsten omskolning

Med en omskolningswebbtjänst kan du omskola din modell med en ny uppsättning parametrar, till exempel nya data, och spara den till senare. När du ansluter en **webbtjänstutdata** till en **tågmodell**matar träningsexperimentet ut en ny modell som du kan använda.

Gör så här för att distribuera en omskolningswebbtjänst:

1. Anslut en **inmatningsmodul** för webbtjänsten till datainmatningen. Vanligtvis vill du se till att dina indata bearbetas på samma sätt som dina ursprungliga träningsdata.
1. Anslut en **webbtjänstutmatningsmodul** till utdata från **tågmodellen**.
1. Om du har en **utvärdera modellmodul** kan du ansluta en **webbtjänstutdatamodul** för att mata ut utvärderingsresultaten
1. Kör experimentet.

    När du har kört experimentet bör det resulterande arbetsflödet likna följande bild:

    ![Resulterande arbetsflöde](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nu distribuerar du utbildningsexperimentet som en omskolningswebbtjänst som matar ut en tränad modell och modellutvärderingsresultat.

1. Klicka på **Konfigurera webbtjänst** längst ned på experimentarbetsytan
1. Välj **Distribuera webbtjänst [Ny]**. Portalen Azure Machine Learning Web Services öppnas på sidan **Distribuera webbtjänst.**
1. Skriv ett namn på webbtjänsten och välj en betalningsplan.
1. Välj **Distribuera**.

## <a name="retrain-the-model"></a>Omskola modellen

I det här exemplet använder vi C# för att skapa omskolningsprogrammet. Du kan också använda Python- eller R-exempelkoden för att utföra den här uppgiften.

Följ följande steg för att anropa omskolnings-API:erna:

1. Skapa ett C#-konsolprogram i Visual Studio: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)**.
1. Logga in på machine learning-portalen för webbtjänster.
1. Klicka på webbtjänsten som du arbetar med.
1. Klicka på **Förbruka**.
1. Klicka på **Batch**i avsnittet **Exempelkod** längst ned på sidan **Använd** .
1. Kopiera exempelkoden C#för batchkörning och klistra in den i Program.cs filen. Kontrollera att namnområdet förblir intakt.

Lägg till NuGet-paketet Microsoft.AspNet.WebApi.Client, enligt vad som anges i kommentarerna. Om du vill lägga till referensen till Microsoft.WindowsAzure.Storage.dll kan du behöva installera [klientbiblioteket för Azure Storage-tjänster](https://www.nuget.org/packages/WindowsAzure.Storage).

Följande skärmbild visar sidan **Förbruka** i Azure Machine Learning Web Services-portalen.

![Använda sida](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Uppdatera apikey-deklarationen

Leta upp **apikey-deklarationen:**

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Leta reda på primärnyckeln i avsnittet **Grundläggande förbrukningsinformation** på sidan **Förbruka** och kopiera den till **apikey-deklarationen.**

### <a name="update-the-azure-storage-information"></a>Uppdatera Azure Storage-informationen

BES-exempelkoden överför en fil från en lokal enhet (till exempel "C:\temp\CensusInput.csv") till Azure Storage, bearbetar den och skriver tillbaka resultaten till Azure Storage.

1. Logga in på Azure-portalen
1. Klicka på **Fler tjänster**i kolumnen Vänster, sök efter **lagringskonton**och markera den.
1. I listan över lagringskonton väljer du en för att lagra den omskolade modellen.
1. Klicka på **Access-knappar**i den vänstra navigeringskolumnen .
1. Kopiera och spara primär **åtkomstnyckeln**.
1. Klicka på **Blobbar**i den vänstra navigeringskolumnen .
1. Markera en befintlig behållare eller skapa en ny och spara namnet.

Leta reda på deklarationerna *StorageAccountName,* *StorageAccountKey*och *StorageContainerName* och uppdatera de värden som du sparade från portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Du måste också se till att indatafilen är tillgänglig på den plats som du anger i koden.

### <a name="specify-the-output-location"></a>Ange utdataplats

När du anger utdataplatsen i begäran nyttolasten måste filtillägget som anges i `ilearner` *RelativeLocation* anges som .

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Här är ett exempel på omskolning utgång:

![Omskolningsutgång](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Utvärdera omskolningsresultaten

När du kör programmet innehåller utdata url-token för signaturer och signaturer för delad åtkomst som är nödvändiga för att komma åt utvärderingsresultaten.

Du kan se prestandaresultaten för den omskolade modellen genom att kombinera *BaseLocation*, *RelativeLocation*och *SasBlobToken* från utdataresultaten för *output2* och klistra in den fullständiga WEBBADRESSEN i webbläsarens adressfält.

Undersök resultaten för att avgöra om den nyutbildade modellen presterar bättre än den befintliga.

Spara *BaseLocation,* *RelativeLocation*och *SasBlobToken* från utdataresultaten.

## <a name="update-the-predictive-experiment"></a>Uppdatera det prediktiva experimentet

### <a name="sign-in-to-azure-resource-manager"></a>Logga in på Azure Resource Manager

Logga först in på ditt Azure-konto från PowerShell-miljön med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="get-the-web-service-definition-object"></a>Hämta webtjänstdefinitionsobjektet

Hämta sedan web servicedefinitionsobjektet genom att anropa cmdleten [Get-AzMlWebService.](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice)

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Om du vill ta reda på resursgruppsnamnet för en befintlig webbtjänst kör du cmdleten Get-AzMlWebService utan några parametrar för att visa webbtjänsterna i prenumerationen. Leta reda på webbtjänsten och titta sedan på dess webbtjänst-ID. Namnet på resursgruppen är det fjärde elementet i ID:t, strax efter elementet *resourceGroups.* I följande exempel är resursgruppnamnet Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Om du vill bestämma resursgruppnamnet för en befintlig webbtjänst loggar du också in på Azure Machine Learning Web Services-portalen. Välj webbtjänsten. Resursgruppsnamnet är det femte elementet i webbtjänstens URL, strax efter elementet *resourceGroups.* I följande exempel är resursgruppnamnet Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Exportera webtjänstdefinitionsobjektet som JSON

Om du vill ändra definitionen av den tränade modellen så att den nyutbildade modellen används måste du först använda cmdleten [Exportera-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) för att exportera den till en JSON-formatfil.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Uppdatera referensen till ilearner blob

Leta reda på [tränad modell] i tillgångarna och uppdatera *uri-värdet* på platsenInfo-noden med ilearnerblobens ilearner.In the assets, locate the [trained model], update the uri value in the *locationInfo* node with the URI of the ilearner blob. URI genereras genom att kombinera *BaseLocation* och *RelativeLocation* från utdata från BES omskolningsanrop.

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importera JSON till ett definitionsobjekt för webbtjänst

Använd cmdleten [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) för att konvertera tillbaka den ändrade JSON-filen till ett Web Service Definition-objekt som du kan använda för att uppdatera det predikativa experimentet.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Uppdatera webbtjänsten

Slutligen använder du cmdleten [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) för att uppdatera förutsägelseexperimentet.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar webbtjänster eller håller reda på flera experimentkörningar finns i följande artiklar:

* [Utforska webbtjänstportalen](manage-new-webservice.md)
* [Hantera experimentupprepningar](manage-experiment-iterations.md)
