---
title: Distribution och förbrukning
titleSuffix: ML Studio (classic) - Azure
description: Du kan använda Azure Machine Learning Studio (klassisk) för att distribuera arbetsflöden och modeller för maskininlärning som webbtjänster. Dessa webbtjänster kan sedan användas för att anropa maskininlärningsmodeller från program över Internet för att göra förutsägelser i realtid eller i batch-läge.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204400"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klassiska) Webbtjänster: Distribution och förbrukning

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Du kan använda Azure Machine Learning Studio (klassisk) för att distribuera arbetsflöden och modeller för maskininlärning som webbtjänster. Dessa webbtjänster kan sedan användas för att anropa maskininlärningsmodellerna från program via Internet för att göra förutsägelser i realtid eller i batch-läge. Eftersom webbtjänsterna är RESTful kan du anropa dem från olika programmeringsspråk och plattformar, till exempel .NET och Java, och från program, till exempel Excel.

I nästa avsnitt finns länkar till genomgångar, kod och dokumentation som hjälper dig att komma igång.

## <a name="deploy-a-web-service"></a>Distribuera en webbtjänst

### <a name="with-azure-machine-learning-studio-classic"></a>Med Azure Machine Learning Studio (klassisk)

Studio-portalen (klassisk) och Microsoft Azure Machine Learning Web Services-portalen hjälper dig att distribuera och hantera en webbtjänst utan att skriva kod.

Följande länkar innehåller allmän information om hur du distribuerar en ny webbtjänst:

* En översikt över hur du distribuerar en ny webbtjänst som baseras på Azure Resource Manager finns i [Distribuera en ny webbtjänst](deploy-a-machine-learning-web-service.md).
* En genomgång av hur du distribuerar en webbtjänst finns i [Distribuera en azure machine learning-webbtjänst](deploy-a-machine-learning-web-service.md).
* För en fullständig genomgång om hur du skapar och distribuerar en webbtjänst, börja med [handledning 1: Förutsäga kreditrisk](tutorial-part1-credit-risk.md).
* Specifika exempel som distribuerar en webbtjänst finns i:

  * [Handledning 3: Distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md)
  * [Distribuera en webbtjänst till flera regioner](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Med API:er för resursprovider för webbtjänster (Azure Resource Manager API:er)

Azure Machine Learning Studio (klassisk) resursleverantör för webbtjänster möjliggör distribution och hantering av webbtjänster med hjälp av REST API-anrop. Mer information finns i [REST-referensen (Machine Learning Web Service).](/rest/api/machinelearning/index)

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Med PowerShell-cmdlets

Azure Machine Learning Studio (klassisk) resursleverantör för webbtjänster möjliggör distribution och hantering av webbtjänster med hjälp av PowerShell-cmdlets.

Om du vill använda cmdlets måste du först logga in på ditt Azure-konto inifrån PowerShell-miljön med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Om du inte är bekant med hur du anropar PowerShell-kommandon som baseras på Resource Manager läser [du Använda Azure PowerShell med Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Om du vill exportera förutsägelseexperimentet använder du [den här exempelkoden](https://github.com/ritwik20/AzureML-WebServices). När du har skapat EXE-filen från koden kan du skriva:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Om du kör programmet skapas en JSON-mall för webbtjänsten. Om du vill använda mallen för att distribuera en webbtjänst måste du lägga till följande information:

* Namn och nyckel för lagringskonto

    Du kan hämta namnet och nyckeln för lagringskontot från [Azure-portalen](https://portal.azure.com/).
* Id för åtagandeplan

    Du kan hämta plan-ID:t från Azure [Machine Learning Web Services-portalen](https://services.azureml.net) genom att logga in och klicka på ett plannamn.

Lägg till dem i JSON-mallen som underordnade till *properties-noden* på samma nivå som *machinelearningworkspace-noden.*

Här är ett exempel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Mer information finns i följande artiklar och exempelkod:

* [Azure Machine Learning Studio (klassisk) Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) referens på MSDN
* Exempel på [genomgång](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) på GitHub

## <a name="consume-the-web-services"></a>Konsumera webbtjänster

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Från Azure Machine Learning Web Services UI (Testning)

Du kan testa webbtjänsten från Azure Machine Learning Web Services-portalen. Detta inkluderar testning av BES-gränssnitt (Request-Response Service) och BES-gränssnitt (Batch Execution Service).

* [Distribuera en ny webbtjänst](deploy-a-machine-learning-web-service.md)
* [Distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md)
* [Handledning 3: Distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Från Excel

Du kan hämta en Excel-mall som använder webbtjänsten:

* [Använda en azure machine learning-webbtjänst från Excel](consuming-from-excel.md)
* [Excel-tillägg för Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Från en REST-baserad klient

Azure Machine Learning Web Services är RESTful API:er. Du kan använda dessa API:er från olika plattformar, till exempel .NET, Python, R, Java, etc. Sidan **Förbruka** för din webbtjänst på [Portalen Microsoft Azure Machine Learning Web Services](https://services.azureml.net) har exempelkod som kan hjälpa dig att komma igång. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).
