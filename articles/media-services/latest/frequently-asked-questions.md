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
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756588"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Media Services v3

Den här artikeln ger svar på Azure Media Services (AMS) v3 vanliga frågor och svar.

## <a name="v3-apis"></a>v3-API: er

### <a name="how-do-i-configure-media-reserved-units"></a>Hur konfigurerar jag Mediereserverade enheter?

För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

Mer information finns i [skala mediebearbetning med CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Vad är den rekommenderade metoden för att bearbeta videor?

Vi rekommenderar att du skickar in jobb med hjälp av en HTTP-URL som pekar på videon. Mer information [http (s) mata in](job-input-from-http-how-to.md). Du behöver inte skapa en tillgång med indatavideon innan de kan bearbetas.

### <a name="how-does-pagination-work"></a>Hur fungerar sidbrytning?

Media Services har stöd för $top för resurser som har stöd för OData men värdet som skickas till $top måste vara mindre än 1 000 (till exempel sidstorleken för sidbrytning).

På så sätt kan du antingen hämta ett litet antal objekt som använder $top (till exempel de 100 senaste objekten) eller sidan men alla objekt som använder sidbrytning. 

Media Services stöder inte växling via data med en användare som har angetts sidstorlek.

Mer information finns i [filtrering, sortering, växling](entities-overview.md).

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Hur du hämtar en entitet i Media Services v3?

v3 baseras på en enhetlig API-yta, som innehåller funktioner för både hantering och åtgärder som bygger på **Azure Resource Manager**. I enlighet med **Azure Resource Manager**, resursnamnen alltid är unika. Därför kan du använda alla strängar som unik identifierare (till exempel GUID) för din resurs.

## <a name="live-streaming"></a>Liveuppspelning 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hur du infogar radbrytningar/videor och avbildning pekdatorer under live stream?

Media Services v3 direktsänd kodning stöder ännu inte att infoga video- och bildfiler pekdatorer under live stream. 

Du kan använda en [livekodaren lokala](recommended-on-premises-live-encoders.md) växla källvideo. Många appar erbjuder möjligheten att byta källor, till exempel Telestream Wirecast, Studio mellan aktiviteter (på iOS), OBS Studio (kostnadsfri app) och många fler.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure-portalen för att hantera v3-resurser?

Inte ännu. Du kan använda någon av de stödda SDK: erna. Se självstudier och exempel i den här webbplatsen.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Finns det ett AssetFile begrepp i v3?

AssetFiles togs bort från AMS-API för att separera medietjänster från Storage SDK beroende. Lagring, inte Media Services, behåller nu den information som tillhör i lagring. 

Mer information finns i [migrera till Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Var finns på klientsidan lagringskryptering?

Vi rekommenderar nu att använda serversidan lagringskryptering (som är aktiverad som standard). Mer information finns i [Azure Storage Service Encryption för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över Media Services v3](media-services-overview.md)
