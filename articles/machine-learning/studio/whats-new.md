---
title: Vad är nytt i Azure Machine Learning | Microsoft Docs
description: Nya funktioner som är tillgängliga i Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: e0bf53670895c81379dcc2cd58542a8e3b3bc585
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Vad är nytt i Azure Machine Learning?

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>Mars 2017-versionen av Microsoft Azure Machine Learning uppdateringar innehåller följande funktioner:



* Dedikerade kapacitet för Azure Machine Learning BES-jobb

    Datorn Learning Batch-Pool bearbetning använder den [Azure Batch](../../batch/batch-technical-overview.md) ska hanteras av kunden skalan för Azure Machine Learning Batch Execution Service. Poolen batchbearbetning kan du skapa Azure Batch-pooler som du kan skicka batchjobb och ha dem köra förutsägbart.

    Mer information finns i [Azure Batch-tjänsten för Machine Learning jobb](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Augusti 2016-versionen av Microsoft Azure Machine Learning uppdateringar innehåller följande funktioner:
* Klassiska webbtjänster kan hanteras i den nya [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/) portal som ger dig en plats för att hantera alla aspekter av webbtjänsten.    
  * Som innehåller webbtjänsten [användningsstatistik](manage-new-webservice.md).
  * Förenklar testning av Azure Machine Learning fjärr-begäran-anrop med exempeldata.
  * Ger en ny Batch Execution Service testsida exempel data och jobbet skicka historik.
  * Tillhandahåller enklare hantering av slutpunkten.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Juli 2016-versionen av Microsoft Azure Machine Learning uppdateringar innehåller följande funktioner:
* Webbtjänster som nu hanteras som Azure-resurser som hanteras via [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) gränssnitt, vilket ger följande förbättringar:
  * Det finns nya [REST API: er](https://msdn.microsoft.com/library/azure/Dn950030.aspx) att distribuera och hantera Resource Manager baserat webbtjänsterna.
  * Det finns en ny [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/) portal som ger dig en plats för att hantera alla aspekter av webbtjänsten.
* Innehåller en ny prenumeration-baserade, flera regioner web service distributionsmodell med Resource Manager baserade API: er som utnyttjar Resource Manager Resource Provider för webbtjänster.
* Inför nya [prissättningar](https://azure.microsoft.com/pricing/details/machine-learning/) och planera hanteringsmöjligheter med nya Resource Manager RP för fakturering.
  * Du kan nu [distribuera webbtjänsten till flera regioner](how-to-deploy-to-multiple-regions.md) utan att behöva skapa en prenumeration i varje region.
* Innehåller webbtjänsten [användningsstatistik](manage-new-webservice.md).
* Förenklar testning av Azure Machine Learning fjärr-begäran-anrop med exempeldata.
* Ger en ny Batch Execution Service testsida exempel data och jobbet skicka historik.

Dessutom har uppdaterats så att du kan distribuera till den nya Web service modellen Machine Learning Studio eller fortsätta att distribuera till den klassiska modellen för Web service. 

