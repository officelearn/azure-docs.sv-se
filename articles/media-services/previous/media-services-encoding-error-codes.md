---
title: Felkoder för Azure Media Services -kodning | Microsoft-dokument
description: I det här avsnittet visas felkoder som kan returneras om ett fel påträffades under körningen av kodning av uppgifter..
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64709526"
---
# <a name="encoding-error-codes"></a>Felkoder vid kodning

I följande tabell visas felkoder som kunde returneras om ett fel påträffades under körningen av kodningsaktiviteten.  Om du vill hämta felinformation i .NET-koden använder du klassen [ErrorDetails.](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) Om du vill hämta felinformation i REST-koden använder du [REST-API:et för ErrorDetail.](https://msdn.microsoft.com/library/jj853026.aspx)

| ErrorDetail.Code | Möjliga orsaker till fel |
| --- | --- |
| Okänt |Okänt fel när aktiviteten kördes |
| FelLadda nerInputAssetMalformedContent |Kategori av fel som täcker fel i nedladdning av indata, till exempel felaktiga filnamn, nolllängdsfiler, felaktiga format och så vidare. |
| FelLadda nerInputAssetServiceFailure |Kategori av fel som täcker problem på tjänstsidan - till exempel nätverks- eller lagringsfel vid nedladdning. |
| ErrorParsingConfiguration |Kategori av fel \<där aktiviteten se cref="MediaTask.PrivateData"/> (konfiguration) är ogiltig, till exempel är konfigurationen inte en giltig systemförinställning eller den innehåller ogiltig XML. |
| FelUtföraTadMalformadinnehåll |Kategori av fel under körningen av uppgiften där problem i indatamediefilerna orsakar fel. |
| FelExecutingTaskUnsupportedFormat |Kategori av fel där medieprocessorn inte kan bearbeta filerna - medieformat stöds inte eller matchar inte konfigurationen. Om du till exempel försöker producera en ljudutdata från en tillgång som bara har |
| FelProcessingTask |Kategori av andra fel som medieprocessorn stöter på under bearbetningen av uppgiften som inte är relaterade till innehåll. |
| ErrorUploadingOutputAsset |Kategori av fel vid uppladdning av utdatatillgången |
| ErrorCancelingTask |Kategori av fel för att täcka fel vid försök att avbryta aktiviteten |
| Transienterror |Kategori av fel för att täcka tillfälliga frågor (t.ex. tillfälliga nätverksproblem med Azure Storage) |

Öppna en [supportbiljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill ha hjälp av **Media Services-teamet.**

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodningsuppgifter genom att anpassa standardförinställningar för mediekodare](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
