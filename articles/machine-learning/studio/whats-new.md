---
title: Vad är nytt i Azure Machine Learning Studio | Microsoft Docs
description: Nya funktioner som är tillgängliga i Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: 894aa55f2101c728fb39bbe6d84875dec9843ab9
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823069"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Vad är nytt i Azure Machine Learning Studio

## <a name="october-2018"></a>Oktober 2018

Motor för R-språket i den [kör R-skript](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) modulen har lagt till en ny version av R runtime version--Microsoft R Open (MRO) 3.4.4. MRO 3.4.4 bygger på öppen källkod R CRAN 3.4.4 och därför är kompatibel med paket som fungerar med den versionen av R.  Mer information om de R-paket som stöds i artikeln ”[R-paket som stöds av Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)”.

## <a name="march-2017"></a>Mars 2017 
Den här versionen av Microsoft Azure Machine Learning uppdateringar innehåller följande funktioner:

* Dedikerad kapacitet för Azure Machine Learning BES-jobb

    Machine Learning Batch-Pool bearbetning använder den [Azure Batch](../../batch/batch-technical-overview.md) tjänster för att ge kundhanterad scale för Azure Machine Learning Batch Execution Service. Batchbearbetning för IP-Pool kan du skapa Azure Batch-pooler som du kan skicka batchjobb och få dem att köra i ett förutsägbart sätt.

    Mer information finns i [Azure Batch-tjänsten för Machine Learning-jobb](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>Augusti 2016 
Den här versionen av Microsoft Azure Machine Learning uppdateringar innehåller följande funktioner:
* Klassiska webbtjänster kan nu hanteras i den nya [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) portal som tillhandahåller ett och samma ställe för att hantera alla aspekter av din webbtjänst.    
  * Som innehåller webbtjänsten [användningsstatistik](manage-new-webservice.md).
  * Förenklar testning av Azure Machine Learning fjärr-begäran-anrop med exempeldata.
  * Ger en ny Batch Execution Service testsida sample data och jobbet skickas historik.
  * Tillhandahåller enklare hantering av slutpunkten.

## <a name="july-2016"></a>Juli 2016 
Den här versionen av Microsoft Azure Machine Learning uppdateringar innehåller följande funktioner:
* Webbtjänster hanteras nu som hanteras via Azure-resurser [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) gränssnitt, vilket ger följande förbättringar:
  * Det finns nya [REST API: er](https://msdn.microsoft.com/library/azure/Dn950030.aspx) baserat att distribuera och hantera Resource Manager-webbtjänster.
  * Det finns en ny [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) portal som tillhandahåller ett och samma ställe för att hantera alla aspekter av din webbtjänst.
* Innehåller en ny prenumerationsbaserade, flera regioner web service-distributionsmodellen med Resource Manager-baserade API: er som utnyttjar Resource Manager Resource Provider för Web Services.
* Inför nya [prissättningsplaner](https://azure.microsoft.com/pricing/details/machine-learning/) och planera hanteringsfunktioner med hjälp av de nya Resource Manager-RP för fakturering.
  * Du kan nu [distribuera webbtjänsten till flera regioner](how-to-deploy-to-multiple-regions.md) utan att behöva skapa en prenumeration i varje region.
* Innehåller webbtjänsten [användningsstatistik](manage-new-webservice.md).
* Förenklar testning av Azure Machine Learning fjärr-begäran-anrop med exempeldata.
* Ger en ny Batch Execution Service testsida sample data och jobbet skickas historik.

Dessutom Machine Learning Studio har uppdaterats så att du kan distribuera till den nya Web service modellen eller fortsätta att distribuera till den klassiska modellen för Web service. 

