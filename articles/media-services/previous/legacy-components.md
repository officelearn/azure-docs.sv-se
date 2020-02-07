---
title: Azure Media Services äldre komponenter | Microsoft Docs
description: I det här avsnittet beskrivs Azure Media Services äldre komponenter.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: bbf3ba17e7f91e88136f2dac50c908262100e77a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048780"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services äldre komponenter

Med tiden har du konstanta förbättringar och förbättringar av media service-komponenter. Därför har vissa äldre komponenter dragits tillbaka. Du hittar anvisningar om hur du migrerar ditt program från den äldre komponenten till en aktuell komponent i följande artiklar.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Indragnings planer för äldre komponenter och vägledning för migrering

Vi presenterar utfasningen av medie processorerna för *Windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (amn). Dessa processorer dras tillbaka den 31 mars 2020.

* [Migrera från Windows Azure Media Encoder till Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrera från Azure Media Encoder till Media Encoder Standard](migrate-azure-media-encoder.md)

Vi meddelar också att följande Medieanalys medie processorer tas ur bruk: 
 
|Namn på mediebearbetare|Datum för indragning|Ytterligare information|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 oktober 2020|Medie processorn kommer att ersättas av [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Mer information finns i [Migrera från Azure Media Indexer 2 till Azure Media Services video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 oktober 2020|Medie processorn kommer att ersättas av [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Mer information finns i [Migrera från Azure Media Indexer till Azure Media Services video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Nästa steg

[Vägledning för migrering för att flytta från Media Services v2 till v3](../latest/migrate-from-v2-to-v3.md)
