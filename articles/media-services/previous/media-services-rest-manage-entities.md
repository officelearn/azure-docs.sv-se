---
title: Hantera Medietjänster med REST | Microsoft-dokument
description: Den här artikeln visar hur du hanterar Media Services-entiteter med REST API.
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
ms.openlocfilehash: a03bc24b689df342be40536c26149a7611fc5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283321"
---
# <a name="managing-media-services-entities-with-rest"></a>Hantera Media Services-entiteter med REST  

> [!div class="op_single_selector"]
> * [Resten](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services är en REST-baserad tjänst som bygger på OData v3. Du kan lägga till, fråga, uppdatera och ta bort entiteter på ungefär samma sätt som du kan på alla andra OData-tjänster. Undantag kommer att tas upp när det är tillämpligt. Mer information om OData finns i [Dokumentation för Open Data Protocol](https://www.odata.org/documentation/).

Det här avsnittet visar hur du hanterar Azure Media Services-entiteter med REST.

>[!NOTE]
> Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Den 1 april 2017 tas till exempel alla jobbpost i ditt konto som är äldre än den 31 december 2016 bort automatiskt. Om du behöver arkivera jobb-/uppgiftsinformationen kan du använda koden som beskrivs i det här avsnittet.

## <a name="considerations"></a>Överväganden  

När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Lägga till entiteter
Varje entitet i Media Services läggs till i en entitetsuppsättning, till exempel Tillgångar, via en POST HTTP-begäran.

I följande exempel visas hur du skapar en AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Att fråga entiteter
Att fråga och lista entiteter är enkelt och omfattar endast en GET HTTP-begäran och valfria OData-åtgärder.
I följande exempel hämtas en lista över alla MediaProcessor-entiteter.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Du kan också hämta en viss entitet eller alla entitetsuppsättningar som är associerade med en viss entitet, till exempel i följande exempel:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

I följande exempel returneras endast egenskapen State för alla jobb.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

I följande exempel returneras alla JobTemplates med namnet "SampleTemplate".

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> Den $expand åtgärden stöds inte i Media Services samt de LINQ-metoder som inte stöds som beskrivs i WCF Data Services (LINQ Considerations).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Räkna upp genom stora samlingar av enheter
Vid fråga entiteter finns det en gräns på 1 000 entiteter som returneras samtidigt eftersom offentliga REST v2 begränsar frågeresultat till 1 000 resultat. Använd **hoppa över** och **överst** för att räkna upp genom den stora samlingen av entiteter. 

Följande exempel visar hur du använder **hoppa över** och **högst upp** för att hoppa över de första 2000 jobben och få nästa 1000 jobb.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Uppdatera entiteter
Beroende på entitetstypen och tillståndet som den befinner sig i kan du uppdatera egenskaper på den entiteten via en PATCH-, PUT- eller MERGE HTTP-begäranden. Mer information om dessa åtgärder finns i [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

I följande kodexempel visas hur du uppdaterar egenskapen Name på en tillgångsentitet.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Ta bort entiteter
Entiteter kan tas bort i Media Services med hjälp av en DELETE HTTP-begäran. Beroende på entiteten kan den ordning i vilken du tar bort entiteter vara viktig. Entiteter som Tillgångar kräver till exempel att du återkallar (eller tar bort) alla positionerare som refererar till den specifika tillgången innan tillgången tas bort.

I följande exempel visas hur du tar bort en sökare som användes för att överföra en fil till blob-lagring.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

