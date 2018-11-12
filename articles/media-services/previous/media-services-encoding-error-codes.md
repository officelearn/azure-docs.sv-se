---
title: Azure Media Services encoding felkoder | Microsoft Docs
description: Det här avsnittet visar en lista över felkoder som kan returneras om ett fel uppstod under körning av kodning uppgift...
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 7e32d0826d36b0d6f68264ba8c74aec49574b0c2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254561"
---
# <a name="encoding-error-codes"></a>Kodning felkoder

I följande tabell visas felkoder som kan returneras om ett fel uppstod under aktivitetskörningen kodning.  För att få information om fel i din .NET-kod kan använda den [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) klass. För att få information om fel i koden REST kan använda den [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail.Code | Möjliga orsaker till felet |
| --- | --- |
| Okänt |Ett okänt fel när aktiviteten körs |
| ErrorDownloadingInputAssetMalformedContent |Kategori för fel som avser fel nedladdningen av indatatillgången, till exempel felaktigt filnamn, noll längd-filer, som felaktigt format och så vidare. |
| ErrorDownloadingInputAssetServiceFailure |Kategori för fel som beskriver problem på serversidan - exempel nätverks- eller fel vid nedladdning. |
| ErrorParsingConfiguration |Kategori för fel där uppgiften <see cref="MediaTask.PrivateData"/> (konfiguration) är inte giltigt, till exempel konfigurationen är inte ett giltigt system förinställda eller innehåller ogiltig XML. |
| ErrorExecutingTaskMalformedContent |Kategori för fel vid körning av aktiviteten där problemen i de inkommande mediefilerna leda till fel. |
| ErrorExecutingTaskUnsupportedFormat |Kategori för fel där mediebearbetare kan inte bearbeta de filer som tillhandahålls – medieformat inte stöds eller matchar inte konfigurationen. Exempel: försöker producera utdata enbart ljud från en tillgång som har endast video |
| ErrorProcessingTask |Kategori för andra fel som mediebearbetare som uppstår under bearbetningen av uppgiften som inte är relaterade till innehåll. |
| ErrorUploadingOutputAsset |Kategori av fel vid uppladdning av utdatatillgången |
| ErrorCancelingTask |Kategori för att täcka fel vid försök att avbryta aktiviteten |
| TransientError |Kategori för att täcka tillfälliga problem (t.ex.) tillfällig nätverksproblem har uppstått med Azure Storage) |

Att få hjälp från den **Media Services** -teamet öppen en [supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utför avancerad kodningsuppgifter genom att anpassa förinställningar för Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
