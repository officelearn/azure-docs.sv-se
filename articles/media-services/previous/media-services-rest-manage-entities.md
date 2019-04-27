---
title: Hantera Media Services-enheter med REST | Microsoft Docs
description: Lär dig mer om att hantera Media Services-entiteter med REST API.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ffbf30f2bfdf0a175513a8d2b9182b35c39f6aae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761717"
---
# <a name="managing-media-services-entities-with-rest"></a>Hantera Media Services-enheter med REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services är en REST-baserad tjänst som bygger på OData v3. Du kan lägga till, fråga, uppdatera och ta bort entiteter på ungefär samma sätt som på andra OData-tjänst. Undantag att appåtgärden anropas när så är tillämpligt. Läs mer på OData [Open Data Protocol dokumentation](https://www.odata.org/documentation/).

Det här avsnittet visar hur du hanterar Azure Media Services-enheter med REST.

>[!NOTE]
> Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Exempelvis den 1 April 2017 tas alla jobbposter i ditt konto som är äldre än den 31 December 2016 automatiskt bort. Om du behöver Arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs i det här avsnittet.

## <a name="considerations"></a>Överväganden  

Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Att lägga till enheter
Varje entitet i Media Services har lagts till i en entitetsuppsättning, till exempel tillgångar, via en HTTP POST-begäran.

I följande exempel visas hur du skapar en AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Fråga entiteter
Frågor och visa en lista över entiteter är enkelt och bara omfattar en hämta HTTP-begäran och valfritt OData-åtgärder.
I följande exempel hämtar en lista över alla MediaProcessor entiteter.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Du kan också hämta en specifik entitet eller alla entitetsuppsättningar som är associerade med en specifik entitet, som i följande exempel:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

I följande exempel returneras endast egenskapen State för alla jobb.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

I följande exempel returneras alla JobTemplates med namnet ”SampleTemplate”.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> $Expand åtgärden stöds inte i Media Services samt som inte stöds LINQ-metoderna som beskrivs i LINQ överväganden (WCF-datatjänster).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Uppräkning av stora mängder av entiteter
Vid frågor till entiteter, finns det en gräns på 1000 enheter som returneras i taget eftersom offentlig REST-v2 begränsar frågeresultaten till 1000 resultat. Använd **hoppa över** och **upp** att räkna upp genom det stora antal entiteter. 

I följande exempel visas hur du använder **hoppa över** och **upp** hoppas över först 2000 jobb och hämta sedan 1000 jobb.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Uppdaterar entiteter
Beroende på enhetstypen och tillstånd att är kan du uppdatera egenskaperna på denna entitet via en KORRIGERINGSFIL PUT eller sammanfoga HTTP-begäranden. Mer information om dessa åtgärder finns i [MERGE-PATCH/PUT](https://msdn.microsoft.com/library/dd541276.aspx).

I följande kodexempel visar hur du uppdaterar egenskapen namn på en entitet för tillgången.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Ta bort entiteter
Entiteter kan tas bort i Media Services med hjälp av en ta bort HTTP-begäran. Den ordning som du kan ta bort entiteter kan vara viktiga beroende på entiteten. Till exempel entiteter, till exempel tillgångar kräver att du återkalla (eller ta bort) alla positionerare som refererar till den tillgången innan du tar bort tillgången.

I följande exempel visas hur du tar bort en positionerare som används för att ladda upp en fil till blob-lagring.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

