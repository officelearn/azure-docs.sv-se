---
title: Omtrimma en befintlig förutsägande webbtjänst | Microsoft Docs
description: Lär dig hur du tränar en modell och uppdatera webbtjänsten för att använda den nyligen tränade modellen i Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: b06e3d742a0bed778dc7671128980708ba379e39
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714901"
---
# <a name="retrain-an-existing-predictive-web-service"></a>Omtrimma en befintlig förutsägande webbtjänst
Det här dokumentet beskrivs hur omtränings följande scenario:

* Du har ett träningsexperiment och ett förutsägelseexperiment som du har distribuerat som en driftsatt webbtjänst.
* Du har nya data som du vill att din förutsägbar webbtjänst du använder för att utföra dess bedömning.

> [!NOTE]
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).

Från och med dina befintliga webbtjänsten och experiment, måste du följa dessa steg:

1. Uppdatera modellen.
   1. Ändra din träningsexperiment för web service indata och utdata.
   2. Distribuera träningsexperimentet som en omtränings webbtjänst.
   3. Använd den träningsexperiment BES Batch Execution Service () för att träna modellen.
2. Använda Azure Machine Learning PowerShell-cmdletar för att uppdatera förutsägbart experiment.
   1. Logga in på ditt Azure Resource Manager-konto.
   2. Hämta web service definition.
   3. Exportera web service definition som JSON.
   4. Uppdatera referensen till ilearner blob i JSON.
   5. Importera JSON till en web service definition.
   6. Uppdatera webbtjänsten med en ny web service definition.

## <a name="deploy-the-training-experiment"></a>Distribuera träningsexperimentet
Om du vill distribuera träningsexperiment som en omtränings webbtjänst, måste du lägga till web service indata och utdata modellen. Genom att ansluta en *Web Service utdata* modulen till arbetsytan för experimentet *[Träningsmodell][train-model]* modulen, som du aktiverar träningsexperiment till Skapa en ny tränad modell som du kan använda i din förutsägelseexperiment. Om du har en *utvärdera modell* modulen, du kan även ansluta web service-utdata för att hämta utvärderingsresultatet som utdata.

Så här uppdaterar du din träningsexperiment:

1. Ansluta en *Web Service inkommande* modul till dina data-indata (till exempel en *Rensa Data som saknas* modul). Normalt kan du se till att dina indata bearbetas på samma sätt som den ursprungliga informationen för utbildning.
2. Ansluta en *Web Service utdata* modulen till utdataporten för din *Träningsmodell* modulen.
3. Om du har en *utvärdera modell* modulen och du vill utdataresultat utvärdering, ansluta en *Web Service utdata* modulen till utdataporten för din *utvärdera modell* modulen.

Kör experimentet.

Därefter måste du distribuera träningsexperimentet som en webbtjänst som producerar en tränad modell och utvärderingsresultat av modellen.

Längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service**, och välj sedan **distribuera webbtjänsten [nyhet]**. Azure Machine Learning Web Services-portalen öppnas på den **distribuera webbtjänsten** sidan. Skriv ett namn för din webbtjänst, Välj en betalningsplan och klicka sedan på **distribuera**. Du kan bara använda Batch Execution-metoden för att skapa anpassade modeller.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Träna modellen med nya data med hjälp av BES
Det här exemplet använder vi C# för att skapa omtränings-program. Du kan också använda exempelkod för Python eller R för att åstadkomma detta.

Att anropa omtränings-API: erna:

1. Skapa ett C#-konsolprogram i Visual Studio: **New** > **projekt** > **Visual C#** > **Windows Classic Desktop** > **konsolprogram (.NET Framework)**.
2. Logga in på Machine Learning Web Services-portalen.
3. Klicka på den webbtjänst som du arbetar med.
4. Klicka på **förbrukar**.
5. Längst ned på den **förbruka** sidan den **exempelkoden** klickar du på **Batch**.
6. Kopiera C# exempelkoden för batchkörning och klistra in den i filen Program.cs. Se till att namnområdet förblir intakta.

Lägg till NuGet-paketet system.NET.http.Formatting, enligt vad som anges i kommentarerna. Om du vill lägga till referensen till Microsoft.WindowsAzure.Storage.dll, måste du kanske först installera den [-klientbiblioteket för Azure Storage-tjänsterna](https://www.nuget.org/packages/WindowsAzure.Storage).

Följande skärmbild visar de **förbruka** i Azure Machine Learning Web Services-portalen.

![Använda sidan][1]

### <a name="update-the-apikey-declaration"></a>Uppdatera apikey deklaration
Leta upp den **apikey** deklarationen:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

I den **grundläggande förbrukning info** delen av den **förbruka** sidan letar du upp den primära nyckeln och kopiera den till den **apikey** deklaration.

### <a name="update-the-azure-storage-information"></a>Uppdatera informationen om Azure Storage
Exempelkoden BES laddar upp en fil från en lokal enhet (till exempel ”C:\temp\CensusIpnput.csv”) till Azure Storage, bearbetar dessa och skriver resultatet tillbaka till Azure Storage.

När du har kört experimentet resulterande arbetsflödet ska se ut ungefär så här:

![Resulterande arbetsflöde efter att köra][4]

1. Logga in på Azure Portal.
2. I den vänstra kolumnen klickar du på **fler tjänster**, Sök efter **lagringskonton**, och markera den.
3. Välj en för att lagra retrained modellen från listan över lagringskonton.
4. I den vänstra kolumnen klickar du på **åtkomstnycklar**.
5. Kopiera och spara den **primära åtkomstnyckel**.
6. I den vänstra kolumnen klickar du på **behållare**.
7. Välj en befintlig behållare eller skapa en ny och spara namnet.

Leta upp den *StorageAccountName*, *StorageAccountKey*, och *StorageContainerName* deklarationer, och uppdatera de värden som du sparade från portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Du måste också kontrollera att indatafilen är tillgänglig på den plats som du anger i koden.

### <a name="specify-the-output-location"></a>Ange platsen för utdata
När du anger platsen i begäran-nyttolasten, tillägg till den fil som har angetts i *RelativeLocation* måste anges som `ilearner`. Se följande exempel:

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
När du kör programmet innehåller utdata URL: en och signaturtoken för delad åtkomst som krävs för att komma åt utvärderingar.

Du kan se prestandaresultat retrained modellen genom att kombinera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från utdataresultat som för *output2* (som visas i den föregående bilden för omtränings utdata) och klistra in den fullständiga URL: en i webbläsarens adressfält.

Granska resultaten för att avgöra om den nyligen tränade modellen utför tillräckligt bra för att ersätta den befintliga.

Kopiera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från utdataresultat som.

## <a name="retrain-the-web-service"></a>Träna om webbtjänsten
När du tränar en ny webbtjänst kan du uppdatera förutsägande web service definition för att referera till den nya tränade modellen. Web service definition är en intern representation av den tränade modellen webbtjänsten och kan inte direkt ändras. Kontrollera att du hämtar web service definition för din förutsägelseexperiment och inte experimentet utbildning.

## <a name="sign-in-to-azure-resource-manager"></a>Logga in till Azure Resource Manager
Du måste först logga in på ditt Azure-konto från PowerShell-miljö med hjälp av den [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet.

## <a name="get-the-web-service-definition-object"></a>Hämta objektet för Web Service Definition
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

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportera Web Service Definition-objektet som JSON
Om du vill ändra definitionen av den tränade modellen att använda den nyligen tränade modellen, måste du först använda den [Export AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) cmdlet för att exportera den till en JSON-fil.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Uppdatera referensen till ilearner-blob
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importera JSON till ett objekt för Web Service Definition
Du måste använda den [Import AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) cmdlet för att omvandla den ändrade JSON-filen tillbaka till ett Web Service Definition-objekt som du kan använda för att uppdatera predicative experimentet.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Uppdatera webbtjänsten
Använd slutligen den [uppdatering AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) cmdlet för att uppdatera förutsägbart experiment.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
