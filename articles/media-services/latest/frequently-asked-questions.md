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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: dccbc6e57e970ec7089f81fccb33b741b9c00e74
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376728"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Media Services v3

Den här artikeln ger svar på Azure Media Services (AMS) v3 vanliga frågor och svar.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure-portalen för att hantera v3-resurser?

Inte ännu. Du kan använda någon av de stödda SDK: erna. Se självstudier och exempel i den här webbplatsen.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Finns det ett API för att konfigurera Mediereserverade enheter?

Media Services-teamet är eliminera ru: er i v3. Men tjänst som är nödvändig arbetet har inte slutförts. Fram till dess kommer kunder har du använder Azure portal eller AMS v2 API: er för att ange ru: er (enligt beskrivningen i [skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). 

När du använder **VideoAnalyzerPreset** och/eller **AudioAnalyzerPreset**, ange ditt Media Services-konto till 10 Mediereserverade S3-enheter.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Finns det något AssetFile begrepp V3 tillgång?

AssetFiles togs bort från AMS-API för att separera medietjänster från Storage SDK beroende. Lagring, inte Media Services, behåller nu den information som tillhör i lagring. 

## <a name="where-did-client-side-storage-encryption-go"></a>Var finns på klientsidan lagringskryptering?

Vi rekommenderar nu serversidan lagringskryptering (som är aktiverad som standard). Mer information finns i [Azure Storage Service Encryption för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Vad är metoden rekommenderade upload?

Vi rekommenderar att du matar in användning av HTTP (s). Mer information finns i [http (s) mata in](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Hur fungerar sidbrytning?

Media Services har stöd för $top för resurser som har stöd för OData men värdet som skickas till $top måste vara mindre än 1 000 (till exempel sidstorleken för sidbrytning).

På så sätt kan du antingen hämta ett litet antal objekt som använder $top (till exempel de 100 senaste objekten) eller sidan men alla objekt som använder sidbrytning. 

Media Services stöder inte växling via data med en användare som har angetts sidstorlek.

Mer information finns i [filtrering, sortering, växling](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Hur du hämtar en entitet i Media Services v3?

v3 baseras på en enhetlig API-yta, som innehåller funktioner för både hantering och åtgärder som bygger på **Azure Resource Manager**. I enlighet med **Azure Resource Manager**, resursnamnen alltid är unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över Media Services v3](media-services-overview.md)
