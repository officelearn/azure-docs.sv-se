---
title: "Azure Machine Learning-webbtjänster: Distribution och användning | Microsoft Docs"
description: "Resurser för att distribuera och använda webbtjänster."
services: machine-learning
documentationcenter: 
author: aashishb
manager: hjerez
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: aashishb
ms.openlocfilehash: 7eecfe4a80499fbba0533f0d410dd920db61e62c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning-webbtjänster: Distribution och användning
Du kan använda Azure Machine Learning för att distribuera machine learning arbetsflöden och modeller som webbtjänster. Dessa webbtjänster kan sedan användas för att anropa machine learning-modeller från program via Internet för att göra förutsägelser i realtid eller i batchläge. Eftersom webbtjänsterna RESTful kan anropa du dem från olika programmeringsspråk språk och plattformar, till exempel .NET och Java, och program som Excel.

Nästa avsnitt innehåller länkar till genomgång, kod och dokumentation för att komma igång.

## <a name="deploy-a-web-service"></a>Distribuera en webbtjänst

### <a name="with-azure-machine-learning-studio"></a>Med Azure Machine Learning Studio
Machine Learning Studio och Microsoft Azure Machine Learning-webbtjänster portalen kan du distribuera och hantera en webbtjänst utan att skriva kod.

Följande länkar ger allmän Information om hur du distribuerar en ny webbtjänst:

* Översiktligt om hur du distribuerar en ny webbtjänst som baseras på Azure Resource Manager finns [distribuera en ny webbtjänst](publish-a-machine-learning-web-service.md).
* En genomgång om hur du distribuerar en webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).
* En fullständig genomgång om hur du skapar och distribuerar en webbtjänst finns [genomgången steg 1: skapa en arbetsyta i Machine Learning](walkthrough-1-create-ml-workspace.md).
* Vissa exempel som distribuerar en webbtjänst finns:

  * [Genomgång steg 5: Distribuera Azure Machine Learning-webbtjänst](walkthrough-5-publish-web-service.md)
  * [Distribuera en webbtjänst till flera regioner](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Med resursprovidern för web services API: er (Azure Resource Manager API: er)
Azure Machine Learning resource provider för webbtjänster kan distribution och hantering av webbtjänster med hjälp av REST API-anrop. Mer information finns i [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) referens.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Med PowerShell-cmdlets
Azure Machine Learning resource provider för webbtjänster kan distribution och hantering av webbtjänster med PowerShell-cmdlets.

Om du vill använda cmdlets måste du först logga in på ditt Azure-konto från PowerShell-miljö med hjälp av den [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet. Om du inte känner till hur du anropar PowerShell-kommandon som är baserade på hanteraren för filserverresurser, se [med hjälp av Azure PowerShell med Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

Om du vill exportera dina prediktivt experiment, Använd [den här exempelkoden](https://github.com/ritwik20/AzureML-WebServices). När du skapar .exe-fil från koden, skriver du:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Kör programmet skapar en mall för JSON web. Om du vill använda mallen för att distribuera en webbtjänst, måste du lägga till följande information:

* Lagringskontonamn och nyckel

    Du kan hämta namnet på lagringskontot och nyckeln från den [Azure-portalen](https://portal.azure.com/).
* Åtagande plan-ID

    Du kan hämta plan-ID från den [Azure Machine Learning-webbtjänster](https://services.azureml.net) portal genom att logga in och klicka på ett namn för energischema.

Lägg till dem i JSON-mall som underordnade till den *egenskaper* nod på samma nivå som den *MachineLearningWorkspace* nod.

Här är ett exempel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Se följande artiklar och exempelkod för ytterligare information:

* [Azure Machine Learning-Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) reference på MSDN
* Exempel [genomgången](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) på GitHub

## <a name="consume-the-web-services"></a>Konsumera webbtjänster
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Från Azure Machine Learning-webbtjänster Användargränssnittet (test)
Du kan testa webbtjänsten från portalen Azure Machine Learning-webbtjänster. Detta inkluderar testar tjänsten begäran och svar (RR) och Batch Execution service (BES)-gränssnitt.

* [Distribuera en ny webbtjänst](publish-a-machine-learning-web-service.md)
* [Distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md)
* [Genomgång steg 5: Distribuera Azure Machine Learning-webbtjänst](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Från Excel
Du kan hämta en Excel-mall som förbrukar webbtjänsten:

* [Använda en Azure Machine Learning-webbtjänst från Excel](consuming-from-excel.md)
* [Excel-tillägg för Azure Machine Learning-webbtjänster](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>En REST-baserad klient
Azure Machine Learning-webbtjänster är RESTful-API: er. Du kan använda dessa API: er från olika plattformar, till exempel .NET, Python, R, Java och så vidare. Den **förbruka** sidan för webbtjänsten på den [Microsoft Azure Machine Learning-webbtjänster portal](https://services.azureml.net) har exempelkod som kan hjälpa dig att komma igång. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).
