---
title: Azure Media Services encoding felkoder | Microsoft Docs
description: "Det här avsnittet beskrivs felkoder som kan returneras om ett fel uppstod under aktivitetskörningen kodning..."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: f4fd2212d19f89148dde08c75c5a48cdd322d029
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="encoding-error-codes"></a>Kodning felkoder

I följande tabell visas felkoder som kan returneras om ett fel uppstod under aktivitetskörningen kodning.  För att få information om fel i .NET-kod, använder den [felinformation](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) klass. För att få information om felet i REST-kod, använder den [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail.Code | Möjliga orsaker till felet |
| --- | --- |
| Okänd |Ett okänt fel när aktiviteten körs |
| ErrorDownloadingInputAssetMalformedContent |Kategori för fel som täcker fel i hämtar inkommande tillgång till exempel felaktiga filnamn, noll längd filer, felaktig formaterar och så vidare. |
| ErrorDownloadingInputAssetServiceFailure |Kategori för fel som beskriver problem på tjänstsidan - exempel nätverks- eller fel vid hämtning av. |
| ErrorParsingConfiguration |Kategori av fel där uppgift <see cref="MediaTask.PrivateData"/> (konfiguration) är inte giltig, till exempel konfigurationen är inte ett giltigt system förinställningen eller innehåller ogiltig XML. |
| ErrorExecutingTaskMalformedContent |Fel under körningen av aktiviteten där problem i de inkommande mediefilerna orsaka fel kategori. |
| ErrorExecutingTaskUnsupportedFormat |Kategori av fel där media kunde inte bearbeta filer som tillhandahålls - medieformat inte stöds eller matchar inte konfigurationen. Till exempel skulle producera ett ljuddata utdata från en tillgång som har endast video |
| ErrorProcessingTask |Kategori för andra fel som processorn media uppstår under bearbetningen av uppgiften som inte är relaterade till innehåll. |
| ErrorUploadingOutputAsset |Fel vid överföring av utdatatillgången kategori |
| ErrorCancelingTask |Kategori för att täcka fel vid försök att avbryta aktiviteten |
| TransientError |Kategori för att täcka tillfälliga problem (t.ex. tillfällig nätverksproblem med Azure Storage) |

Få hjälp från den **Media Services** team, öppna en [stöder biljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodning uppgifter genom att anpassa Media Encoder Standard förinställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
