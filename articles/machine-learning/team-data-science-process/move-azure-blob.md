---
title: Flytta Data till och från Azure Blob storage - Team Data Science Process
description: Flytta data till och från Azure Blob Storage med hjälp av Azure Storage Explorer, AzCopy, python och SSIS.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717585"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytta data till och från Azure Blob storage

Team Data Science Process kräver att data ska matas in eller läses in i en mängd olika lagringsmiljöer bearbetas eller analyseras i det lämpligaste sättet i varje steg i processen.

## <a name="different-technologies-for-moving-data"></a>Olika tekniker för att flytta data

I följande artiklar beskrivs hur du flyttar data till och från Azure Blob storage med hjälp av olika tekniker.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Vilken metod som passar bäst för dig beror på ditt scenario. [Scenarierna för avancerad analys i Azure Machine Learning](plan-sample-scenarios.md) artikel hjälper dig att avgöra vilka resurser du behöver för en mängd olika data vetenskaps arbets flöden som används i avancerad analys process.

> [!NOTE]
> En fullständig introduktion till Azure Blob Storage finns i grunderna för [Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Använda Azure Data Factory

Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att: 

* Skapa och schemalägga en pipeline som hämtar data från Azure blob storage 
* Skicka den till en publicerad Azure Machine Learning-webbtjänst 
* ta emot resultaten förutsägelseanalys och 
* Överför resultatet till lagring. 

Mer information finns i [skapa förutsägande pipelines med hjälp av Azure Data Factory och Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringskontots åtkomstnyckel för kontot. Innan du laddar upp/laddar ned data måste du känna till Azure Storage kontots namn och konto nyckel.

* Information om hur du konfigurerar en Azure-prenumeration finns i [den kostnads fria utvärderings perioden på en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagrings konto och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

