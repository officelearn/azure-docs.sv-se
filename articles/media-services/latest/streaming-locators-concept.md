---
title: Strömning positionerare i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad positionerare för direktuppspelning är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120352"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

För att skapa videor i utdatatillgångar som klienter ska kunna spela upp måste du skapa en [positionerare för direktuppspelning](https://docs.microsoft.com/rest/api/media/streaminglocators) och sedan skapa direktuppspelnings-URL:er. .NET-exempel finns i [Hämta en positionerare för direktuppspelning](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **Strömningspositionerare**, måste du ange en **tillgången** namn och en **Streaming princip** namn. Mer information finns i följande avsnitt:

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Principer för innehållsnycklar](content-key-policy-concept.md)

> [!IMPORTANT]
> * Egenskaper för **positionerare för direktuppspelning** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="associate-filters-with-streaming-locators"></a>Associera filter med positionerare för direktuppspelning

Se [filter: Koppla med strömning positionerare](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrera, ordning, sidan Strömningspositionerare entiteter

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

[Självstudie: Ladda upp, koda och strömma videor med hjälp av .NET](stream-files-tutorial-with-api.md)
