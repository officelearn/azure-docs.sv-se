---
title: Så här hämtar du en Medieprocessor-instans med hjälp av REST | Microsoft Docs
description: Lär dig hur du skapar ett media processorkomponenten för att koda, konvertera formatet, kryptera eller dekryptera medieinnehåll för Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d342cff6d322195ee88a74215f814be7d702aa5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761984"
---
# <a name="how-to-get-a-media-processor-instance"></a>Så här hämtar du en Medieprocessor-instans
> [!div class="op_single_selector"]
> * [NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
Mediebearbetare är en komponent som hanterar en specifik video eller ljud bearbetning av aktivitet, till exempel kodning, Formatkonvertering, kryptering eller dekryptering medieinnehåll. Alla aktiviteter som skickas till Media Services kräver en medieprocessor att koda, kryptera eller konvertering av video- eller ljudinnehåll innehåll. 

## <a name="azure-media-processors"></a>Azure media-processorer 

Följande avsnitt innehåller en lista över mediebearbetare:

* [Mediebearbetare för kodning](scenarios-and-availability.md#encoding-media-processors)
* [Mediebearbetare för analys](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Hämta en medieprocessor

Följande REST-anrop visar hur du hämtar en media processorinstans efter namn (i det här fallet **Media Encoder Standard**). 

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
Nu när du vet hur du hämtar en media processorinstans går du till den [koda en tillgång](media-services-rest-get-started.md) artikel som visar hur du använder Media Encoder Standard koda en tillgång.

