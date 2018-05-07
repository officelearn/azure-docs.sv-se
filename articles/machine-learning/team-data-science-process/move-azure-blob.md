---
title: Flytta Data till och från Azure Blob Storage | Microsoft Docs
description: Flytta data till och från Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 424216226ca4b92f17cec78dadbb0643defa8d0c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytta data till och från Azure Blob Storage
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Vilken metod som passar bäst för dig beror på ditt scenario. Den [scenarier för avancerade analyser i Azure Machine Learning](plan-sample-scenarios.md) artikeln hjälper dig att avgöra de resurser du behöver för en mängd olika datavetenskap arbetsflöden som används i avancerade analyser.

> [!NOTE]
> En fullständig introduktion till Azure blob storage finns i [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) till: 

* Skapa och schemalägga en pipeline som hämtar data från Azure blob storage. 
* Överför den till en publicerad Azure Machine Learning-webbtjänst 
* förutsägelseanalyser-resultat och 
* Överför resultat till lagring. 

Mer information finns i [skapa förutsägande pipelines med hjälp av Azure Data Factory och Azure Machine Learning](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringsnyckel för det kontot. Innan du laddar upp/hämtar data, måste du känna till din Azure storage-konto och nyckel.

* Om du vill konfigurera en Azure-prenumeration finns [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagringskonto och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

