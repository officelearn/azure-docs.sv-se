---
title: Hur du hämtar en Medieprocessor-instans med hjälp av REST | Microsoft Docs
description: Lär dig hur du skapar ett media processorkomponenten för att koda, konvertera formatet, kryptera eller dekryptera medieinnehåll för Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 3e0bd654deca9db8ac13f4af9c4732ba42c01e97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790253"
---
# <a name="how-to-get-a-media-processor-instance"></a>Hur du hämtar en Medieprocessor-instans
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
Media processorer är en komponent som hanterar en specifik video eller ljud bearbetning aktivitet, till exempel kodning, kryptering eller dekryptering medieinnehåll-Formatkonvertering. Alla aktiviteter som har skickats till Media Services kräver en medieprocessor att koda, kryptera eller konvertering av video eller ljud innehåll. 

## <a name="azure-media-processors"></a>Azure media-processorer 

Följande avsnitt innehåller en lista över media processorer:

* [Kodning media-processorer](scenarios-and-availability.md#encoding-media-processors)
* [Analytics-medieprocessorer](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Vid åtkomst till entiteter i Media Services måste du ange specifika namn på huvudfält och värden i HTTP-begäranden. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API: et finns [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Hämta en medieprocessor

Följande REST-anrop visar hur du hämtar en media-processor med namnet (i det här fallet **Media Encoder Standard**). 

Begäran:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

Svar:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du hämtar en media processor, gå till den [koda en tillgång](media-services-rest-get-started.md) artikel som visar hur du använder Media Encoder Standard koda en tillgång.

