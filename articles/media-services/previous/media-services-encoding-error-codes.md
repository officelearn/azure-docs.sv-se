---
title: Fel koder för Azure Media Services kodning | Microsoft Docs
description: Det här avsnittet innehåller felkoder som kan returneras om ett fel påträffades under kodnings körningen..
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "64709526"
---
# <a name="encoding-error-codes"></a>Felkoder vid kodning

I följande tabell visas felkoder som kan returneras om ett fel påträffades under kodnings körningen.  Om du vill ha fel information i .NET-koden använder du klassen [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Använd [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) -REST API för att få fel information i din rest-kod.

| ErrorDetail. Code | Möjliga orsaker till felet |
| --- | --- |
| Okänt |Ett okänt fel uppstod när uppgiften kördes |
| ErrorDownloadingInputAssetMalformedContent |Kategori av fel som täcker fel vid hämtning av indata till gångar, t. ex. Felaktiga fil namn, filer med nollängd, felaktiga format och så vidare. |
| ErrorDownloadingInputAssetServiceFailure |Kategori av fel som täcker problem på tjänst sidan, t. ex. nätverks-eller lagrings fel vid hämtning. |
| ErrorParsingConfiguration |Kategori av fel där uppgiften \<se cref = "MediaTask. PrivateData"/> (konfiguration) är ogiltig, till exempel om konfigurationen inte är en giltig systeminställning eller om den innehåller ogiltig XML. |
| ErrorExecutingTaskMalformedContent |Fel kategori under körningen av aktiviteten där problem i indatafiler orsakar fel. |
| ErrorExecutingTaskUnsupportedFormat |Kategori av fel där medie processorn inte kan bearbeta de angivna filerna, medie formatet stöds inte eller matchar inte konfigurationen. Du kan till exempel försöka skapa en ljud signal från en till gång som bara har video |
| ErrorProcessingTask |Kategori av andra fel som medie processorn påträffar under bearbetningen av uppgiften som inte är relaterade till innehåll. |
| ErrorUploadingOutputAsset |Kategori av fel vid uppladdning av utgående till gång |
| ErrorCancelingTask |Kategori av fel som ska gälla fel vid försök att avbryta uppgiften |
| TransientError |Kategori av fel som ska gälla för tillfälliga problem (t. ex. tillfälligt nätverks problem med Azure Storage) |

Öppna ett [support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för att få hjälp från **Media Servicess** teamet.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodnings uppgifter genom att anpassa Media Encoder Standard för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
