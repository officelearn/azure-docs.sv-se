---
title: Flytta data till och från Azure Blob storage - Team Data Science Process
description: Flytta data till och från Azure Blob-lagring med Azure Storage Explorer, AzCopy, Python och SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717585"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytta data till och från Azure Blob-lagring

Team Data Science Process kräver att data intas eller laddas i en mängd olika lagringsmiljöer som ska bearbetas eller analyseras på lämpligaste sätt i varje steg i processen.

## <a name="different-technologies-for-moving-data"></a>Olika tekniker för att flytta data

I följande artiklar beskrivs hur du flyttar data till och från Azure Blob-lagring med hjälp av olika tekniker.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzKopia](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Vilken metod som är bäst för dig beror på ditt scenario. [Scenarierna för avancerad analys i Azure Machine Learning-artikeln](plan-sample-scenarios.md) hjälper dig att fastställa vilka resurser du behöver för en mängd olika datavetenskapsarbetsflöden som används i den avancerade analysprocessen.

> [!NOTE]
> En fullständig introduktion till Azure blob-lagring finns i [Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Använda Azure Data Factory

Som ett alternativ kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att: 

* skapa och schemalägga en pipeline som hämtar data från Azure blob storage, 
* skicka den till en publicerad Azure Machine Learning-webbtjänst, 
* ta emot de prediktiva analysresultaten, och 
* överföra resultaten till lagring. 

Mer information finns i [Skapa förutsägande pipelines med Azure Data Factory och Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringsnyckel för det kontot. Innan du laddar upp/laddar ned data måste du känna till ditt Azure Storage-kontonamn och kontonyckel.

* Information om hur du konfigurerar en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)finns i Kostnadsfri en månads provperiod .
* Instruktioner om hur du skapar ett lagringskonto och för att hämta konto- och nyckelinformation finns i [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

