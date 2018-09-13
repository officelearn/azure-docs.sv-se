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
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 854c671d4db6cdca2b019ed9adb0475e588281b1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35622559"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytta data till och från Azure Blob Storage
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Vilken metod som passar bäst för dig beror på ditt scenario. Den [scenarier för avancerade analyser i Azure Machine Learning](plan-sample-scenarios.md) artikeln hjälper dig att avgöra de resurser du behöver för en mängd olika data science arbetsflöden som används i processen för avancerad analys.

> [!NOTE]
> En fullständig introduktion till Azure blob storage finns [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) till: 

* Skapa och schemalägga en pipeline som hämtar data från Azure blob storage 
* Skicka den till en publicerad Azure Machine Learning-webbtjänst 
* ta emot resultaten förutsägelseanalys och 
* Överför resultatet till lagring. 

Mer information finns i [skapa förutsägande pipelines med Azure Data Factory och Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringskontots åtkomstnyckel för kontot. Innan du laddar upp/ned data, vet du ditt Azure storage-konto och kontonyckeln.

* Om du vill konfigurera en Azure-prenumeration, se [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* Mer information om hur du skapar ett lagringskonto och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

