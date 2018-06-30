---
title: Vanliga frågor och svar om Azure Media Services v3 | Microsoft Docs
description: Den här artikeln innehåller svar på Azure Media Services v3 vanliga frågor och svar.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/29/2018
ms.author: juliako
ms.openlocfilehash: 098a34aba8e5ce23f64d4bb07e3b9622aa2adb8e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110428"
---
# <a name="azure-media-services-v3-preview-frequently-asked-questions"></a>Azure Media Services v3 vanliga frågor och svar om (förhandsgranskning)

Den här artikeln innehåller svar på Azure Media Services (AMS) v3 vanliga frågor och svar.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure-portalen för att hantera v3 resurser?

Inte ännu. Du kan använda en av de SDK: er som stöds. Se självstudier och exempel i den här dokumentet.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Finns det en API för att konfigurera Media reserverade enheter?

Media Services-teamet är eliminera RUs i v3. Men tjänster som behövs arbetet har inte slutförts. Fram till dess kan kunder behöver använda Azure-portalen eller AMS v2 API: er för att ange RUs (enligt beskrivningen i [skalning media bearbetning](../previous/media-services-scale-media-processing-overview.md). 

När du använder **VideoAnalyzerPreset** och/eller **AudioAnalyzerPreset**, ange ditt Media Services-konto till 10 S3 Media reserverade enheter.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Har V3 tillgången något AssetFile begrepp?

AssetFiles har tagits bort från AMS API för att avgränsa Media Services från Storage SDK: N beroende. Nu behåller lagring, inte Media Services, den information som tillhör i lagring. 

## <a name="where-did-client-side-storage-encryption-go"></a>Var finns på klientsidan lagringskryptering?

Vi rekommenderar nu serversidan lagringskryptering (som är aktiverad som standard). Mer information finns i [Azure Storage Service-kryptering för Data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Vad är den rekommenderade överför-metoden?

Vi rekommenderar en användning av HTTP (s). Mer information finns i [http (s) mata in](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Hur fungerar sidbrytning?

Media Services stöder $top för resurser som har stöd för OData men värdet som skickas till $top måste vara mindre än 1000 (till exempel sidstorleken för sidbrytning).

På så sätt kan du få antingen ett litet antal objekt med $top (till exempel 100 senaste artiklarna) eller om sidan alla objekt med hjälp av sidbrytningar. 

Media Services stöder inte sidindelning via data med en angiven användare sidstorlek.

Mer information finns i [filtrering, sortering, växling](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Hur du hämtar en entitet i Media Services v3?

v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som bygger på **Azure Resource Manager**. I enlighet med **Azure Resource Manager**, resursnamnen alltid är unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Media Services v3-översikt](media-services-overview.md)
