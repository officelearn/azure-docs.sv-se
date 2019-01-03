---
title: Översikt för Azure Media Services-entiteter – Azure | Microsoft Docs
description: Den här artikeln ger en översikt över Azure Media Services-entiteter.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1d309bb550282d5245a2fbf74f14a931cf5c2279
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53746260"
---
# <a name="azure-media-services-entities-overview"></a>Översikt över entiteter i Azure Media Services

Den här artikeln ger en kort översikt över Azure Media Services entiteter och punkter i en artikel för mer information om varje entitet. 

| Avsnitt | Beskrivning |
|---|---|
| [Kontofilter och tillgången filter](filters-dynamic-manifest-overview.md)|När du levererar ditt innehåll till kunder (streaming direktsändningar eller Video på begäran) kanske klienten behöver mer flexibilitet än vad som beskrivs i standard-tillgången manifestfil. Azure Media Services kan du definiera [kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) och [tillgången filter](https://docs.microsoft.com/rest/api/media/assetfilters). Använd sedan **dynamiska manifest** baserat på fördefinierade filter. |
| [Tillgångar](assets-concept.md)|En [tillgången](https://docs.microsoft.com/rest/api/media/assets) entitet innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När de digitala filerna överförs till en tillgång, kan de användas i Media Services encoding, strömning, analysera innehållet arbetsflöden.|
| [Viktiga Innehållsprinciper](content-key-policy-concept.md)|Du kan använda Media Services för att skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Med medietjänster kan leverera du live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter.|
| [LiveEvents och LiveOutputs](live-events-outputs-concept.md)|Media Services kan du leverera händelser till dina kunder på Azure-molnet. Om du vill konfigurera din strömmade direktsändningar av evenemang i Media Services v3, du behöver lära dig om [Live händelser](https://docs.microsoft.com/rest/api/media/liveevents) och [Live utdata](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)|En [Strömningsslutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints) entitet representerar en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN Content Delivery Network () för vidare distribution. Den utgående dataströmmen från en slutpunkt för direktuppspelning-tjänst kan vara en direktsänd dataström eller en video på begäran-tillgång i Media Services-kontot. När du skapar ett Media Services-konto, en **standard** slutpunkt för direktuppspelning skapas åt dig i ett stoppat tillstånd. Du kan inte ta bort den **standard** slutpunkt för direktuppspelning. Du kan skapa ytterligare slutpunkter för direktuppspelning under kontot. Om du vill starta direktuppspelning av videor, måste du starta den Strömningsslutpunkt från vilken du vill atream videon. |
| [Positionerare för direktuppspelning](streaming-locators-concept.md)|Du måste ange dina klienter med en URL som de kan använda för att spela upp kodade video- eller ljudinnehåll filer, måste du skapa en [Strömningspositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators) och skapa strömmande URL: er.|
| [Principer för direktuppspelning](streaming-policy-concept.md)| [Principer för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingpolicies) gör det möjligt att definiera strömningsprotokoll och alternativ för kryptering för din StreamingLocators. Du kan ange namnet på en anpassad strömning princip som du har skapat, eller så kan du använda en av de fördefinierade Streaming principer som erbjuds av Media Services. <br/><br/>När du använder en anpassad princip för direktuppspelning, bör du utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ för kryptering och protokoll krävs. Du bör inte skapa en ny princip för strömning för varje Strömningspositionerare.|
| [Transformeringar och jobb](transforms-jobs-concept.md)|Använd [omvandlar](https://docs.microsoft.com/rest/api/media/transforms) att konfigurera vanliga uppgifter för kodning eller analysera videor. Varje **transformera** beskriver ett recept eller ett arbetsflöde med uppgifter för att bearbeta video- eller ljudinnehåll filer.<br/><br/>En [jobbet](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran till Azure Media Services att tillämpa den **omvandla** till en given video- eller ljudinnehåll datainnehållet. Den **jobbet** anger information som platsen för indatavideo och platsen för utdata. Du kan ange platsen för dina indata video med: HTTPS-adresser, SAS URL: er eller en tillgång.|

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)
