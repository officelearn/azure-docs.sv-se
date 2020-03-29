---
title: Så här skaffar du en mediaprocessorinstans med REST | Microsoft-dokument
description: Lär dig hur du skapar en medieprocessorkomponent för att koda, konvertera format, kryptera eller dekryptera medieinnehåll för Azure Media Services.
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
ms.openlocfilehash: 3d7b3922c9bb7eb79cd6436ba1b265714678fcc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774909"
---
# <a name="how-to-get-a-media-processor-instance"></a>Hämta en mediaprocessorinstans
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Resten](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
Medieprocessorer är en komponent som hanterar en viss video- eller ljudbehandlingsuppgift, till exempel kodning, formatkonvertering, kryptering eller dekryptering av medieinnehåll. Alla uppgifter som skickas till Media Services kräver att en medieprocessor kodar, krypterar eller konverterar video- eller ljudinnehållet. 

## <a name="azure-media-processors"></a>Azure-mediebehandlare 

Följande avsnitt innehåller listor över medieprocessorer:

* [Mediebearbetare för kodning](scenarios-and-availability.md#encoding-media-processors)
* [Mediebearbetare för analys](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Skaffa en medieprocessor

Följande REST-anrop visar hur du får en medieprocessorinstans efter namn (i det här fallet **Media Encoder Standard**). 

Begäran:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
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

## <a name="next-steps"></a>Efterföljande moment
Nu när du vet hur du hämtar en medieprocessorinstans går du till artikeln [Så här kodar du en tillgång](media-services-rest-get-started.md) som visar hur du använder Media Encoder Standard för att koda en tillgång.

