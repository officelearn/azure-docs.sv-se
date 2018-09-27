---
title: Azure Media Services v3 viktig information | Microsoft Docs
description: Om du vill hålla dig uppdaterad med den senaste utvecklingen, ger dig med de senaste uppdateringarna på Azure Media Services v3 i den här artikeln.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219337"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 (förhandsversion) viktig information 

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* Den senaste versionen
* Kända problem
* Felkorrigeringar
* Inaktuell funktion
* Planer för ändringar

## <a name="may-07-2018"></a>07 maj 2018

### <a name="net-sdk"></a>.NET SDK

Följande funktioner finns i .net SDK:

1. **Omvandlar** och **jobb** att koda eller analysera medieinnehåll. Exempel finns i [Stream filer](stream-files-tutorial-with-api.md) och [analysera](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** för att publicera och strömning av innehåll till slutanvändarens enheter
3. **StreamingPolicies** och **ContentKeyPolicies** konfigurera viktiga leverans- och content protection (DRM) när du levererar innehåll.
4. **LiveEvents** och **LiveOutputs** att konfigurera för inmatning och arkivering av liveuppspelningsinnehåll.
5. **Tillgångar** att lagra och publicera medieinnehåll i Azure Storage. 
6. **Strömningsslutpunkter** du konfigurerar och skalar dynamisk paketering, kryptering och strömning för både live och på begäran medieinnehåll.

### <a name="known-issues"></a>Kända problem

* När du skickar ett jobb kan du ange för att mata in källvideon med hjälp av HTTPS-adresser, SAS URL: er eller sökvägar till filer i Azure Blob storage. AMS v3 stöder för närvarande inte Chunked-kodning över HTTPS-adresser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt](media-services-overview.md)
