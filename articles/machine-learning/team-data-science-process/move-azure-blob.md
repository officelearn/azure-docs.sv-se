---
title: Flytta Data till och från Azure Blob storage - Team Data Science Process
description: Flytta Data till och från Azure Blob storage
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a37c19ac0d3c053644b2f1f970ef9f84eac2f1df
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139865"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytta data till och från Azure Blob storage

Team Data Science Process kräver att data ska matas in eller läses in i en mängd olika lagringsmiljöer bearbetas eller analyseras i det lämpligaste sättet i varje steg i processen.

## <a name="different-technologies-for-moving-data"></a>Olika tekniker för att flytta data

I följande artiklar beskrivs hur du flyttar data till och från Azure Blob storage med hjälp av olika tekniker.

* [Azure Lagringsutforskaren](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Vilken metod som passar bäst för dig beror på ditt scenario. Den [scenarier för avancerade analyser i Azure Machine Learning](plan-sample-scenarios.md) artikeln hjälper dig att avgöra de resurser du behöver för en mängd olika data science arbetsflöden som används i processen för avancerad analys.

> [!NOTE]
> En fullständig introduktion till Azure blob storage finns [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Använda Azure Data Factory

Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) till: 

* Skapa och schemalägga en pipeline som hämtar data från Azure blob storage 
* Skicka den till en publicerad Azure Machine Learning-webbtjänst 
* ta emot resultaten förutsägelseanalys och 
* Överför resultatet till lagring. 

Mer information finns i [skapa förutsägande pipelines med Azure Data Factory och Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringskontots åtkomstnyckel för kontot. Innan du laddar upp/ned data, vet du ditt Azure storage-konto och kontonyckeln.

* Om du vill konfigurera en Azure-prenumeration, se [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* Mer information om hur du skapar ett lagringskonto och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

