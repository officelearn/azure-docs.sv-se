---
title: Vanliga frågor och svar om Azure Media Services v3 | Microsoft Docs
description: Den här artikeln ger svar på Azure Media Services v3 vanliga frågor och svar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: e543f6e69632da67900e3b49a9c77e125df5f852
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316923"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Media Services v3

Den här artikeln ger svar på Azure Media Services (AMS) v3 vanliga frågor och svar.

## <a name="v3-apis"></a>v3-API: er

### <a name="how-do-i-configure-media-reserved-units"></a>Hur konfigurerar jag Mediereserverade enheter?

För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

Mer information finns i [skala mediebearbetning med CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Vad är den rekommenderade metoden för att bearbeta videor?

Använd [omvandlar](https://docs.microsoft.com/rest/api/media/transforms) att konfigurera vanliga uppgifter för kodning eller analysera videor. Varje **transformera** beskriver ett recept eller ett arbetsflöde med uppgifter för att bearbeta video- eller ljudinnehåll filer. En [jobbet](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran till Media Services att tillämpa den **omvandla** till en given video- eller ljudinnehåll datainnehållet. När vi har skapats, kan du skicka jobb med hjälp av API: er för Media Services eller någon av de publicerade SDK: er. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Hur fungerar sidbrytning?

När du använder sidbrytning, bör du alltid använda nästa länk att räkna upp samlingen och inte är beroende av en viss storlek. Mer information och exempel, i [filtrering, sortering, växling](entities-overview.md).

## <a name="live-streaming"></a>Liveuppspelning 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hur du infogar radbrytningar/videor och avbildning pekdatorer under live stream?

Media Services v3 direktsänd kodning stöder ännu inte att infoga video- och bildfiler pekdatorer under live stream. 

Du kan använda en [livekodaren lokala](recommended-on-premises-live-encoders.md) växla källvideo. Många appar erbjuder möjligheten att byta källor, till exempel Telestream Wirecast, Studio mellan aktiviteter (på iOS), OBS Studio (kostnadsfri app) och många fler.

## <a name="content-protection"></a>Innehållsskydd

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hur och var du vill hämta JWT-token innan det att begäran licens eller nyckel?

1. Du måste ha en säker Token tjänster (STS) (webbtjänst) som utfärdar JWT-token på en HTTPS-begäran för produktion. För testning kan du använda koden som visas i **GetTokenAsync** metod som definieras i [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player behöver du göra en begäran när en användare autentiseras till STS för sådana en token och tilldela den som värde för token. Du kan använda den [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Ett exempel för att köra STS, med symmetriska och asymmetrisk nyckel finns i [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Ett exempel på en spelare baserat på Azure Media Player med hjälp av sådana JWT-token finns i [ https://aka.ms/amtest ](https://aka.ms/amtest) (expandera ”player_settings”-länk om du vill se token indata).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hur du för att godkänna begäranden att strömma videor med AES-kryptering?

Det korrekta sättet är att använda STS (Secure Token Service):

I STS, beroende på användarprofil, lägger du till olika anspråk (till exempel ”Premium-användare”, ”grundläggande användare”, ”kostnadsfri utvärdering användare”). Användaren kan se olika innehållet med olika anspråk i en JWT. Naturligtvis för annat innehåll/tillgången har ContentKeyPolicyRestriction motsvarande RequiredClaims.

Använd Azure Media Services API: er för att konfigurera/licensnyckel leverans och krypterar dina tillgångar (enligt [i det här exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Mer information finns i:

- [Content protection-översikt](content-protection-overview.md)
- [Designen av ett system med multi-DRM innehållsskydd med åtkomstkontroll](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure-portalen för att hantera v3-resurser?

För närvarande kan använda du inte Azure-portalen för att hantera v3-resurser. Använd den [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), eller någon av stöds [SDK: er](developers-guide.md).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Finns det ett AssetFile begrepp i v3?

AssetFiles togs bort från AMS-API för att separera medietjänster från Storage SDK beroende. Lagring, inte Media Services, behåller nu den information som tillhör i lagring. 

Mer information finns i [migrera till Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Var finns på klientsidan lagringskryptering?

Vi rekommenderar nu att använda serversidan lagringskryptering (som är aktiverad som standard). Mer information finns i [Azure Storage Service Encryption för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
