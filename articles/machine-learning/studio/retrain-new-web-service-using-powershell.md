---
title: Omtrimma en ny Azure Machine Learning-webbtjänst med PowerShell | Microsoft Docs
description: Lär dig hur du programmässigt tränar en modell och uppdatera webbtjänsten för att använda den nyligen tränade modellen i Azure Machine Learning med Machine Learning Management PowerShell-cmdletar.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 069a3022cf9b6423b95e8f9f35686965d2654be7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631086"
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Omtrimma en ny Resource Manager-baserade webbtjänst med hjälp av Machine Learning Management PowerShell-cmdletar
När du tränar en ny webbtjänst kan du uppdatera förutsägande web service definition för att referera till den nya tränade modellen.

## <a name="prerequisites"></a>Förutsättningar
Du måste ställa in ett träningsexperiment och ett förutsägelseexperiment som visas i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).

> [!IMPORTANT]
> Förutsägbart experiment måste distribueras som en Azure Resource Manager-baserade (ny) machine learning-webbtjänst.
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).

Ytterligare information om distribuera webbtjänster finns i [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

Den här processen kräver att du har installerat Azure Machine Learning-Cmdlets. Information som installerar Machine Learning-cmdletar finns i den [Cmdlets för Azure Machine Learning](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/) reference på MSDN.

Kopiera följande information från omtränings utdata:

* BaseLocation
* RelativeLocation

Vilka steg du utför är:

1. Logga in på ditt Azure Resource Manager-konto.
2. Hämta web service definition
3. Exportera Web Service Definition som JSON
4. Uppdatera referensen till ilearner blob i JSON.
5. Importera JSON till en Web Service Definition
6. Uppdatera webbtjänsten med nya Web Service Definition

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Logga in på Azure Resource Manager-konto
Du måste först logga in på Azure-kontot från i PowerShell-miljö med den [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet.

## <a name="get-the-web-service-definition"></a>Hämta Web Service Definition
Hämta sedan webbtjänsten genom att anropa den [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) cmdlet. Web Service Definition är en intern representation av den tränade modellen webbtjänsten och kan inte direkt ändras. Kontrollera att du hämtar Web Service Definition för din förutsägelseexperiment och inte experimentet utbildning.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Kör cmdleten Get-AzureRmMlWebService utan några parametrar för att visa webbtjänsterna i din prenumeration för att fastställa resursgruppens namn för en befintlig webbtjänst. Leta upp webbtjänsten och titta sedan på dess webbtjänst-ID. Namnet på resursgruppen är det fjärde elementet i ID, efter den *resourceGroups* element. I följande exempel är resursgruppens namn standard-MachineLearning-Usasödracentrala.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Du kan också för att fastställa resursgruppens namn för en befintlig webbtjänst, loggar du in på Microsoft Azure Machine Learning Web Services-portal. Välj webbtjänsten. Resursgruppens namn är det femte elementet i URL: en för webbtjänst, efter den *resourceGroups* element. I följande exempel är resursgruppens namn standard-MachineLearning-Usasödracentrala.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportera Web Service Definition som JSON
Att ändra definitionen för att den tränade modellen att använda den nyligen Tränad modell, måste du först använda den [Export AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) cmdlet för att exportera den till en fil i JSON-format.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Uppdatera referensen till ilearner blob i JSON.
Leta upp den [tränade modellen] i resurser, uppdatera den *uri* värde i den *locationInfo* nod med URI: N för den ilearner-blobben. URI: N genereras genom att kombinera den *BaseLocation* och *RelativeLocation* från utdata för BES träna anrop. Detta uppdaterar sökvägen för att referera till den nya tränade modellen.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importera JSON till en Web Service Definition
Du måste använda den [Import AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) cmdlet för att omvandla den ändrade JSON-filen tillbaka till en Web Service Definition som du kan använda för att uppdatera Web Service Definition.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Uppdatera webbtjänsten med nya Web Service Definition
Slutligen kan du använda [uppdatering AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) cmdlet för att uppdatera Web Service Definition.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Sammanfattning
Med de Machine Learning PowerShell cmdletarna kan uppdatera du den tränade modellen av en förutsägbar webbtjänst att aktivera scenarier som:

* Periodiska modell träna med nya data.
* Distribution av en modell för kunder med målet att så att de kan träna modellen med sina egna data.

