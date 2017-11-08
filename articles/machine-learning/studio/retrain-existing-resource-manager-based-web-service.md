---
title: "Träna om en befintlig förutsägande webbtjänst | Microsoft Docs"
description: "Lär dig mer om att träna om en modell och uppdatera webbtjänsten för att använda den nya tränade modellen i Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: v-donglo
ms.openlocfilehash: 40079da0e04520477771a11a1e4111768cf18280
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="retrain-an-existing-predictive-web-service"></a>Träna om en befintlig förutsägande webbtjänst
Det här dokumentet beskriver omtränings för följande scenario:

* Du har en träningsexperiment och en prediktivt experiment som du har distribuerat som en operationalized webbtjänst.
* Du har nya data som du vill förutsägande webbtjänsten att utföra den bedömningen.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

Från och med din befintliga webbtjänsten och experiment, måste du följa dessa steg:

1. Uppdatera modellen.
   1. Ändra experimentet utbildning för tjänsten indata och utdata.
   2. Distribuera utbildning experiment som en omtränings-webbtjänst.
   3. Använd den träningsexperiment Batch Execution Service BES-för att träna om modellen.
2. Använda Azure Machine Learning PowerShell-cmdlets för att uppdatera prediktivt experiment.
   1. Logga in på ditt konto i Azure Resource Manager.
   2. Hämta web service definition.
   3. Exportera web service definition som JSON.
   4. Uppdatera referens till ilearner-blob i JSON.
   5. Importera JSON till en web service definition.
   6. Uppdatera webbtjänsten med en ny web service definition.

## <a name="deploy-the-training-experiment"></a>Distribuera utbildning experimentet
Om du vill distribuera träningsexperiment som en omtränings webbtjänst, måste du lägga till web service in- och utdataenheter modellen. Genom att ansluta en *Web Service utdata* modulen till arbetsytan för experimentet  *[Träningsmodell] [ train-model]*  modulen, aktivera utbildning experimentet Skapa en ny tränad modell som du kan använda i experimentet förutsägbara. Om du har en *utvärdera modell* modulen, du kan även bifoga web service-utdata för att få utvärderingsresultaten som utdata.

Så här uppdaterar experimentet utbildning:

1. Ansluta en *Web Service inkommande* modulen till data-indata (till exempel en *Rensa Data som saknas* module). Vanligtvis vill du se till att dina indata behandlas på samma sätt som den ursprungliga informationen för utbildning.
2. Ansluta en *Web Service utdata* modulen till utdataporten för din *Träningsmodell* modul.
3. Om du har en *utvärdera modell* modul och du vill att utdata utvärderingsresultaten, ansluta en *Web Service utdata* modulen till utdataporten för din *utvärdera modell* modul.

Kör experimentet.

Därefter måste du distribuera utbildning experiment som en webbtjänst som producerar en tränad modell och utvärderingsresultat av modellen.  

Längst ned i arbetsytan för experimentet klickar du på **konfigurera Web Service**, och välj sedan **distribuera webbtjänsten [ny]**. Azure Machine Learning-webbtjänster portal öppnar den **distribuera webbtjänsten** sidan. Ange ett namn för webbtjänsten, väljer en betalningsplan och klicka sedan på **distribuera**. Du kan bara använda Batch Execution-metoden för att skapa tränade modeller.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Träna om modellen med nya data med hjälp av BES
I det här exemplet använder vi C# skapa omtränings programmet. Du kan också använda Python eller R exempelkod för att utföra den här uppgiften.

Att anropa omtränings-API: erna:

1. Skapa ett C#-konsolprogram i Visual Studio: **ny** > **projekt** > **Visual C#** > **Windows Klassiska Desktop** > **konsolen App (.NET Framework)**.
2. Logga in på portalen Machine Learning-webbtjänster.
3. Klicka på den webbtjänst som du arbetar med.
4. Klicka på **använda**.
5. Längst ned i den **förbruka** sidan den **exempelkod** klickar du på **Batch**.
6. Kopiera C# exempelkod för batchkörning och klistra in den i filen Program.cs. Kontrollera att namnområdet intakt.

Lägg till NuGet-paketet Microsoft.AspNet.WebApi.Client, som anges i kommentarerna. Om du vill lägga till en referens till Microsoft.WindowsAzure.Storage.dll måste du kanske måste först installera den [klientbiblioteket för Azure Storage-tjänster](https://www.nuget.org/packages/WindowsAzure.Storage).

I följande skärmbild visas den **förbruka** sida i Azure Machine Learning-webbtjänster-portalen.

![Använda sidan][1]

### <a name="update-the-apikey-declaration"></a>Uppdatera apikey deklarationen
Leta upp den **apikey** deklaration:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

I den **grundläggande förbrukning info** avsnitt i den **förbruka** , leta upp den primära nyckeln och kopiera den till den **apikey** deklaration.

### <a name="update-the-azure-storage-information"></a>Uppdatera Azure Storage-informationen
Exempelkoden BES överför en fil från en lokal enhet (till exempel ”C:\temp\CensusIpnput.csv”) till Azure Storage, bearbetar den och skriver resultatet till Azure Storage.  

Om du vill uppdatera Azure Storage-informationen måste du hämta den lagringskontonamn och nyckel behållaren information för ditt lagringskonto från den klassiska Azure-portalen och uppdatera sedan motsvarande värden i koden.
När du har kört experimentet bör resulterande arbetsflödet likna följande:

![Resulterande arbetsflödet när kör][4]

1. Logga in på den klassiska Azure-portalen.
2. Klicka på den vänstra navigeringsfönstret i kolumnen **lagring**.
3. Välj en för att lagra retrained modellen från listan över storage-konton.
4. Längst ned på sidan klickar du på **hantera åtkomstnycklar**.
5. Kopiera och spara den **primära åtkomstnyckeln** och Stäng dialogrutan.
6. Överst på sidan, klickar du på **behållare**.
7. Välj en befintlig behållare eller skapa en ny och spara namnet.

Leta upp den *StorageAccountName*, *StorageAccountKey*, och *StorageContainerName* deklarationer, och uppdatera värden som du sparade från den klassiska portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Du måste också kontrollera att filen finns på den plats som du anger i koden.

### <a name="specify-the-output-location"></a>Ange platsen för utdata
När du anger platsen i begäran nyttolasten tillägget på den fil som har angetts i *RelativeLocation* måste anges som `ilearner`. Se följande exempel:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Följande är ett exempel på omtränings utdata:

![Omtränings utdata][6]

## <a name="evaluate-the-retraining-results"></a>Utvärdera omtränings resultaten
När du kör programmet innehåller utdata URL och delade signaturer åtkomsttoken som är nödvändiga för att komma åt utvärderingsresultaten.

Du kan se prestandaresultat retrained modellen genom att kombinera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från resultatet för *output2* (som visas i föregående omtränings utdata avbildningen) och klistra in den fullständiga URL: en i webbläsarens adressfält.  

Granska resultaten för att avgöra om den nyligen tränade modellen utför bra för att ersätta den befintliga versionen.

Kopiera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från resultatet.

## <a name="retrain-the-web-service"></a>Träna om webbtjänsten
När du träna om en ny webbtjänst uppdatera förutsägande web service definition för att referera till den nya tränade modellen. Web service definition är en intern representation av den tränade modellen för webbtjänsten och kan inte ändras direkt. Kontrollera att du hämtar web service definition för experimentet förutsägbara och inte experimentet utbildning.

## <a name="sign-in-to-azure-resource-manager"></a>Logga in till Azure Resource Manager
Du måste först logga in på ditt Azure-konto från PowerShell-miljö med hjälp av den [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

## <a name="get-the-web-service-definition-object"></a>Hämta Web Service Definition objektet
Hämta sedan Web Service Definition objektet genom att anropa den [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

För att fastställa resursgruppens namn för en befintlig webbtjänst, kör du cmdleten Get-AzureRmMlWebService utan några parametrar för att visa webbtjänsterna i din prenumeration. Leta upp webbtjänsten och titta på dess web service-ID. Namnet på resursgruppen är det fjärde elementet med ID precis efter den *resursgrupper* element. I följande exempel är resursgruppens namn standard-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Du kan också för att fastställa resursgruppens namn för en befintlig webbtjänst inloggning på portalen Azure Machine Learning-webbtjänster. Välj webbtjänsten. Resursgruppens namn är den femte del av URL för webbtjänsten precis efter den *resursgrupper* element. I följande exempel är resursgruppens namn standard-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportera Web Service Definition-objektet som JSON
Om du vill ändra definitionen av den tränade modellen att använda den nya tränade modellen måste du först använda den [Export AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) för att exportera den till en JSON-fil.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Uppdatera referens till ilearner-blob
Leta upp [tränad modell] i tillgångarna, uppdatera den *uri* värde i den *locationInfo* nod med ilearner-blob-URI. URI: N skapas genom att kombinera den *BaseLocation* och *RelativeLocation* från utdata från BES omtränings anrop.

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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importera JSON till ett objekt för Web Service Definition
Du måste använda den [importera AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) för att omvandla den ändrade JSON-filen tillbaka till Web Service Definition-objekt som du kan använda för att uppdatera predicative experimentet.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Uppdatera webbtjänsten
Använd slutligen den [uppdatering AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) att uppdatera prediktivt experiment.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
