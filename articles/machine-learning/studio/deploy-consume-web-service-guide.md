---
title: 'Azure Machine Learning-webbtjänster: Distribution och användning | Microsoft Docs'
description: Resurser för att distribuera och använda webbtjänster.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 7c84956f9a22d8924fef53af8f27b730ee24017e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823596"
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning-webbtjänster: Distribution och användning
Du kan använda Azure Machine Learning för att distribuera machine learning arbetsflöden och modeller som webbtjänster. Dessa webbtjänster kan sedan användas för att anropa machine learning-modeller från program via Internet för att göra förutsägelser i realtid eller i batchläge. Eftersom webbtjänsterna är RESTful, kan du anropa dem från olika programmeringsspråk och plattformar, till exempel .NET och Java och program, till exempel Excel.

I nästa avsnitt innehåller länkar till genomgångar, kod och dokumentation som hjälper dig att komma igång.

## <a name="deploy-a-web-service"></a>Distribuera en webbtjänst

### <a name="with-azure-machine-learning-studio"></a>Med Azure Machine Learning Studio
Machine Learning Studio och Microsoft Azure Machine Learning Web Services-portalen hjälper dig att distribuera och hantera en webbtjänst utan att skriva kod.

Följande länkar ger allmän Information om hur du distribuerar en ny webbtjänst:

* En översikt om hur du distribuerar en ny webbtjänst som baseras på Azure Resource Manager finns i [distribuera en ny webbtjänst](publish-a-machine-learning-web-service.md).
* En genomgång om hur du distribuerar en webbtjänst finns i [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).
* En fullständig genomgång om hur du skapar och distribuerar en webbtjänst finns i [genomgång steg 1: skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md).
* Specifika exempel som distribuerar en webbtjänst finns:

  * [Genomgång steg 5: Distribuera Azure Machine Learning-webbtjänst](walkthrough-5-publish-web-service.md)
  * [Hur du distribuerar en webbtjänst till flera regioner](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Med web services-resursprovidern API: er (Azure Resource Manager API: er)
Azure Machine Learning resource provider för webbtjänster möjliggör distribution och hantering av webbtjänster med REST API-anrop. Mer information finns i den [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) referens.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Med PowerShell-cmdletar
Azure Machine Learning resource provider för webbtjänster möjliggör distribution och hantering av webbtjänster med PowerShell-cmdletar.

Om du vill använda cmdletarna som du måste först logga in på ditt Azure-konto från PowerShell-miljö med hjälp av den [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet. Om du inte känner till hur du anropar PowerShell-kommandon som är baserade på resurshanteraren, se [med hjälp av Azure PowerShell med Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

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

* [Azure Machine Learning-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.machinelearning) reference på MSDN
* Exemplet [genomgången](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) på GitHub

## <a name="consume-the-web-services"></a>Använda webbtjänsterna
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Från Azure Machine Learning Web Services Användargränssnittet (test)
Du kan testa din webbtjänst från Azure Machine Learning Web Services-portalen. Detta inkluderar testning Request-Response service (RR) och BES-tjänsten i Batch Execution-gränssnitt.

* [Distribuera en ny webbtjänst](publish-a-machine-learning-web-service.md)
* [Distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md)
* [Genomgång steg 5: Distribuera Azure Machine Learning-webbtjänst](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Från Excel
Du kan hämta en Excel-mall som förbrukar webbtjänsten:

* [Använda en Azure Machine Learning-webbtjänst från Excel](consuming-from-excel.md)
* [Excel-tillägget för Azure Machine Learning-webbtjänster](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Från en REST-baserad klient
Azure Machine Learning-webbtjänster är RESTful API: er. Du kan använda dessa API: er från olika plattformar, till exempel .NET, Python, R, Java, osv. Den **förbruka** sidan för webbtjänsten på den [Microsoft Azure Machine Learning Web Services-portalen](https://services.azureml.net) har exempelkod som hjälper dig att komma igång. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).
