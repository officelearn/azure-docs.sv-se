---
title: Distribution och användning
titleSuffix: ML Studio (classic) - Azure
description: Du kan använda Azure Machine Learning Studio (klassisk) för att distribuera Machine Learning-arbetsflöden och-modeller som webb tjänster. Dessa webbtjänster kan sedan användas för att anropa maskininlärningsmodeller från program via internet för att göra förutsägelser i realtid eller i batchläge.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 7216d2f97a52798d2609073761eb8f4a2ce9024d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976120"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klassiska) webb tjänster: distribution och användning

Du kan använda Azure Machine Learning Studio (klassisk) för att distribuera Machine Learning-arbetsflöden och-modeller som webb tjänster. Dessa webbtjänster kan sedan användas för att anropa maskininlärningsmodeller från program via Internet för att göra förutsägelser i realtid eller i batchläge. Eftersom webbtjänsterna är RESTful, kan du anropa dem från olika programmeringsspråk och plattformar, till exempel .NET och Java och program, till exempel Excel.

I nästa avsnitt innehåller länkar till genomgångar, kod och dokumentation som hjälper dig att komma igång.

## <a name="deploy-a-web-service"></a>Distribuera en webbtjänst

### <a name="with-azure-machine-learning-studio-classic"></a>Med Azure Machine Learning Studio (klassisk)

Studio-portalen (klassisk) och Microsoft Azure Machine Learning Web Services-portalen hjälper dig att distribuera och hantera en webb tjänst utan att skriva kod.

Följande länkar ger allmän Information om hur du distribuerar en ny webbtjänst:

* En översikt om hur du distribuerar en ny webbtjänst som baseras på Azure Resource Manager finns i [distribuera en ny webbtjänst](deploy-a-machine-learning-web-service.md).
* En genomgång om hur du distribuerar en webbtjänst finns i [distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md).
* För en fullständig genom gång av hur du skapar och distribuerar en webb tjänst börjar du med [självstudie 1: Förutsäg kredit risk](tutorial-part1-credit-risk.md).
* Specifika exempel som distribuerar en webbtjänst finns:

  * [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md)
  * [Hur du distribuerar en webbtjänst till flera regioner](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Med web services-resursprovidern API: er (Azure Resource Manager API: er)

Den Azure Machine Learning Studio (klassiska) resurs leverantören för webb tjänster möjliggör distribution och hantering av webb tjänster genom att använda REST API-anrop. Mer information finns i referens för [Machine Learning-webbtjänsten (rest)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Med PowerShell-cmdletar

Den Azure Machine Learning Studio (klassiska) resurs leverantören för webb tjänster möjliggör distribution och hantering av webb tjänster med hjälp av PowerShell-cmdletar.

Om du vill använda cmdlets måste du först logga in på ditt Azure-konto från PowerShell-miljön med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Om du inte känner till hur du anropar PowerShell-kommandon som är baserade på resurshanteraren, se [med hjälp av Azure PowerShell med Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Om du vill exportera dina förutsägelseexperiment använder [den här exempelkoden](https://github.com/ritwik20/AzureML-WebServices). När du har skapat .exe-fil från kod som kan du skriva:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Kör programmet skapar en mall för JSON web. Om du vill använda mallen för att distribuera en webbtjänst, måste du lägga till följande information:

* Lagringskontonamn och nyckel

    Du kan hämta lagringskontonamnet och nyckeln från den [Azure-portalen](https://portal.azure.com/).
* Åtagande plans-ID:

    Du kan hämta plan-ID: T från den [Azure Machine Learning Web Services](https://services.azureml.net) portalen genom att logga in och klicka på namn.

Lägga till dem i JSON-mallen som underordnade till den *egenskaper* nod på samma nivå som den *MachineLearningWorkspace* noden.

Här är ett exempel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Se följande artiklar och exempelkod för ytterligare information:

* [Azure Machine Learning Studio (klassisk) cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) -referens på MSDN
* Exemplet [genomgången](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) på GitHub

## <a name="consume-the-web-services"></a>Använda webbtjänsterna

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Från Azure Machine Learning Web Services Användargränssnittet (test)

Du kan testa din webbtjänst från Azure Machine Learning Web Services-portalen. Detta inkluderar testning Request-Response service (RR) och BES-tjänsten i Batch Execution-gränssnitt.

* [Distribuera en ny webbtjänst](deploy-a-machine-learning-web-service.md)
* [Distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md)
* [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Från Excel

Du kan hämta en Excel-mall som förbrukar webbtjänsten:

* [Använda en Azure Machine Learning-webbtjänst från Excel](consuming-from-excel.md)
* [Excel-tillägget för Azure Machine Learning-webbtjänster](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Från en REST-baserad klient

Azure Machine Learning-webbtjänster är RESTful API: er. Du kan använda dessa API: er från olika plattformar, till exempel .NET, python, R, Java osv. **Förbruknings** sidan för din webb tjänst på [Microsoft Azure Machine Learning Web Services-portalen](https://services.azureml.net) innehåller exempel kod som hjälper dig att komma igång. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).
