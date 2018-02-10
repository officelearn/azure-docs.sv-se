---
title: "Träna om en ny Azure Machine Learning-webbtjänst med PowerShell | Microsoft Docs"
description: "Lär dig mer om att genom programmering träna om en modell och uppdatera webbtjänsten för att använda den nya tränade modellen i Azure Machine Learning med Machine Learning Management PowerShell-cmdlets."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: raymondl
ms.openlocfilehash: 2a9302502594fa23d7bde1a71e7b096bc4152e59
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Träna om en ny Resource Manager-baserat webbtjänst med hjälp av Machine Learning Management PowerShell-cmdlets
När du träna om en ny webbtjänst uppdatera förutsägande web service definition för att referera till den nya tränade modellen.  

## <a name="prerequisites"></a>Förutsättningar
Du måste skapa ett experiment utbildning och prediktivt experiment som visas i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Prediktivt experiment måste distribueras som en Azure Resource Manager (nya) baserad machine learning-webbtjänst. Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

Ytterligare information om distribuera webbtjänster finns [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

Den här processen kräver att du har installerat Azure Machine Learning-Cmdlets. Information om installation av Machine Learning-cmdlets finns i [Azure Machine Learning-Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) reference på MSDN.

Kopiera följande information från omtränings utdata:

* BaseLocation
* RelativeLocation

Du vidta åtgärder är:

1. Logga in på ditt konto i Azure Resource Manager.
2. Hämta web service definition
3. Exportera Web Service Definition som JSON
4. Uppdatera referens till ilearner-blob i JSON.
5. Importera JSON till en Web Service Definition
6. Uppdatera webbtjänsten med nya Web Service Definition

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Logga in på Azure Resource Manager-konto
Du måste först logga in på ditt Azure-konto från i PowerShell-miljö med den [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

## <a name="get-the-web-service-definition"></a>Hämta Web Service Definition
Hämta sedan webbtjänsten genom att anropa den [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet. Web Service Definition är en intern representation av den tränade modellen för webbtjänsten och kan inte ändras direkt. Kontrollera att du hämtar Web Service Definition för experimentet förutsägbara och inte experimentet utbildning.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

För att fastställa resursgruppens namn för en befintlig webbtjänst, kör du cmdleten Get-AzureRmMlWebService utan några parametrar för att visa webbtjänsterna i din prenumeration. Leta upp webbtjänsten och titta på dess web service-ID. Namnet på resursgruppen är det fjärde elementet med ID precis efter den *resursgrupper* element. I följande exempel är resursgruppens namn standard-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Du kan också ta reda på resursgruppens namn för en befintlig webbtjänst genom loggar du in på Microsoft Azure Machine Learning-webbtjänster portal. Välj webbtjänsten. Resursgruppens namn är den femte del av URL för webbtjänsten precis efter den *resursgrupper* element. I följande exempel är resursgruppens namn standard-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportera Web Service Definition som JSON
Ändra definitionen för den tränade modellen att använda den nya utbildade modellen måste du först använda den [Export AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) för att exportera den till en fil i JSON-format.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Uppdatera referens till ilearner-blob i JSON.
Leta upp [tränad modell] i tillgångarna, uppdatera den *uri* värde i den *locationInfo* nod med ilearner-blob-URI. URI: N skapas genom att kombinera den *BaseLocation* och *RelativeLocation* från utdata från BES omtränings anrop. Sökvägen för att referera till den nya tränade modellen uppdateras.

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
Du måste använda den [importera AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) för att konvertera den ändrade JSON-filen till en Web Service Definition som du kan använda för att uppdatera Web Service Definition.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Uppdatera webbtjänsten med nya Web Service Definition
Slutligen kan du använda [uppdatering AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) att uppdatera Web Service Definition.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Sammanfattning
Med Machine Learning PowerShell management-cmdlets kan uppdatera du den tränade modellen av en förutsägbar webbtjänsten för att aktivera scenarier som:

* Periodiska modell via programmering med nya data.
* Distribution av en modell för kunder med målet att låta dem träna om modellen med sina egna data.

