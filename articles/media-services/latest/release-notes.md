---
title: Azure Media Services v3 viktig information | Microsoft Docs
description: Om du vill hålla dig uppdaterad med den senaste utvecklingen, ger dig med de senaste uppdateringarna på Azure Media Services v3 i den här artikeln.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788118"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 (förhandsgranskning) viktig information 

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner
* Planer för ändringar

## <a name="may-07-2018"></a>07 kan 2018

### <a name="net-sdk"></a>.NET SDK

Följande funktioner finns i .net SDK:

1. **Transformerar** och **jobb** koda eller analysera medieinnehåll. Exempel finns i [strömma filer](stream-files-tutorial-with-api.md) och [analysera](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** för att publicera och innehåll till slutanvändarens enheter för strömning
3. **StreamingPolicies** och **ContentKeyPolicies** konfigurera viktiga leverans och innehållsskydd (DRM) när leverera innehåll.
4. **LiveEvents** och **LiveOutputs** att konfigurera infognings- och arkivering av liveströmmat innehåll.
5. **Tillgångar** att lagra och publicera medieinnehåll i Azure Storage. 
6. **Strömningsslutpunkter** att konfigurera och skala dynamisk paketering, kryptering och strömning för både Direktmigrering och på begäran medieinnehåll.

### <a name="known-issues"></a>Kända problem

Kända problem:

När ett jobb med en HTTPS-URL (JobInputHttp) som pekar på källinnehållet, se till att HTTP-servern stöder 'HEAD-begäran. I annat fall avvisas jobbet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt](media-services-overview.md)
