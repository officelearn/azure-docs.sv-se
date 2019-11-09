---
title: Distribution och användning
titleSuffix: ML Studio (classic) - Azure
description: Du kan använda Azure Machine Learning Studio (klassisk) för att distribuera Machine Learning-arbetsflöden och-modeller som webb tjänster. Dessa webb tjänster kan sedan användas för att anropa maskin inlärnings modeller från program via Internet för att göra förutsägelser i real tid eller i batchläge.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 560158dce3c70049ad3faa11a5ffb9a1858aa445
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837671"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klassiska) webb tjänster: distribution och användning

Du kan använda Azure Machine Learning Studio (klassisk) för att distribuera Machine Learning-arbetsflöden och-modeller som webb tjänster. Dessa webb tjänster kan sedan användas för att anropa maskin inlärnings modeller från program via Internet för att göra förutsägelser i real tid eller i batchläge. Eftersom webb tjänsterna är RESTful kan du anropa dem från olika programmeringsspråk och plattformar, till exempel .NET och Java, och från program som Excel.

I nästa avsnitt finns länkar till genom gångar, kod och dokumentation som hjälper dig att komma igång.

## <a name="deploy-a-web-service"></a>Distribuera en webbtjänst

### <a name="with-azure-machine-learning-studio-classic"></a>Med Azure Machine Learning Studio (klassisk)

Studio-portalen (klassisk) och Microsoft Azure Machine Learning Web Services-portalen hjälper dig att distribuera och hantera en webb tjänst utan att skriva kod.

Följande länkar ger allmän information om hur du distribuerar en ny webb tjänst:

* En översikt över hur du distribuerar en ny webb tjänst som är baserad på Azure Resource Manager finns i [distribuera en ny webb tjänst](deploy-a-machine-learning-web-service.md).
* En genom gång av hur du distribuerar en webb tjänst finns i [distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md).
* För en fullständig genom gång av hur du skapar och distribuerar en webb tjänst börjar du med [självstudie 1: Förutsäg kredit risk](tutorial-part1-credit-risk.md).
* För vissa exempel som distribuerar en webb tjänst, se:

  * [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md)
  * [Så här distribuerar du en webb tjänst till flera regioner](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Med API: er för webb tjänst resurs leverantör (Azure Resource Manager API: er)

Den klassiska versionen av Azure Machine Learning Studio Resource Provider för webb tjänster möjliggör distribution och hantering av webb tjänster genom att använda REST API samtal. Mer information finns i referens för [Machine Learning-webbtjänsten (rest)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Med PowerShell-cmdletar

Den klassiska versionen av Azure Machine Learning Studio Resource Provider för webb tjänster möjliggör distribution och hantering av webb tjänster med hjälp av PowerShell-cmdletar.

Om du vill använda cmdlets måste du först logga in på ditt Azure-konto från PowerShell-miljön med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Om du inte är bekant med hur du anropar PowerShell-kommandon som baseras på Resource Manager kan du läsa [använda Azure PowerShell med Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Använd [den här exempel koden](https://github.com/ritwik20/AzureML-WebServices)om du vill exportera förutsägande experiment. När du har skapat exe-filen från koden kan du skriva:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Om du kör programmet skapas en JSON-mall för webbtjänster. Om du vill använda mallen för att distribuera en webb tjänst måste du lägga till följande information:

* Lagrings konto namn och nyckel

    Du kan hämta lagrings kontots namn och nyckel från [Azure Portal](https://portal.azure.com/).
* ID för åtagande plan

    Du kan hämta plan-ID: t från [Azure Machine Learning Web Services-](https://services.azureml.net) portalen genom att logga in och klicka på ett plan namn.

Lägg till dem i JSON-mallen som underordnade till noden *Egenskaper* på samma nivå som *MachineLearningWorkspace* -noden.

Här är ett exempel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Mer information finns i följande artiklar och exempel kod:

* [Azure Machine Learning Studio (klassisk) cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) -referens på MSDN
* Exempel [på](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) GitHub

## <a name="consume-the-web-services"></a>Använda webb tjänsterna

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Från Azure Machine Learning Web Services UI (test)

Du kan testa webb tjänsten från Azure Machine Learning Web Services-portalen. Detta omfattar att testa BES-gränssnitten (Request-Response service) och batch execution service ().

* [Distribuera en ny webbtjänst](deploy-a-machine-learning-web-service.md)
* [Distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md)
* [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Från Excel

Du kan hämta en Excel-mall som förbrukar webbtjänsten:

* [Använda en Azure Machine Learning webb tjänst från Excel](consuming-from-excel.md)
* [Excel-tillägg för Azure Machine Learning webb tjänster](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Från en REST-baserad klient

Azure Machine Learning webb tjänster är RESTful-API: er. Du kan använda dessa API: er från olika plattformar, till exempel .NET, python, R, Java osv. **Förbruknings** sidan för din webb tjänst på [Microsoft Azure Machine Learning Web Services-portalen](https://services.azureml.net) innehåller exempel kod som hjälper dig att komma igång. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).
