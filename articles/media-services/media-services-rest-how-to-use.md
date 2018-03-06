---
title: "Media Services operations REST API-översikt | Microsoft Docs"
description: "Media Services REST API-översikt"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;johndeu
ms.openlocfilehash: 066959058576af830103aa98a12f0c36acfdbb14
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services operations REST API-översikt
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Den **Media Services Operations REST** API används för att skapa jobb, tillgångar, Live-kanaler och andra resurser i ett Media Services-konto. Mer information finns i [Media Services Operations REST API-referensen](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services tillhandahåller en REST-API som accepterar JSON eller atom + pub XML-format. Media Services REST API kräver specifika HTTP-huvuden som varje klient måste skicka när du ansluter till Media Services, samt en uppsättning valfria huvuden. I följande avsnitt beskrivs rubrikerna och HTTP-verb som du kan använda när du skapar förfrågningar och ta emot svar från Media Services.

Autentisering till Media Serivces REST API görs via Azure Active Directory-autentisering som beskrivs i artikel [använda Azure AD-autentisering för åtkomst till Azure Media Services-API med övriga](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Överväganden

Följande gäller när du använder RESTEN.

* När du frågar entiteter, finns det en gräns på 1000 entiteter som returneras i taget eftersom offentlig REST-v2 begränsar frågeresultaten till 1000 resultat. Du måste använda **hoppa över** och **ta** (.NET) / **upp** (REST) enligt beskrivningen i [exemplet .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [exemplet REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* När med JSON och ange om du vill använda den **__metadata** nyckelord i begäran (till exempel till referens ett länkat) måste du ange den **acceptera** sidhuvud till [utförlig JSON-format](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(se följande exempel). OData inte förstår det **__metadata** egenskap i begäran, om du inte anger det utförlig.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Vanliga HTTP-begärans sidhuvud stöds av Media Services
Det finns en uppsättning krävs huvuden måste du inkludera i din förfrågan och en uppsättning valfria huvuden du kan också inkludera för varje anrop som du gör i Media Services. I tabellen nedan visas de nödvändiga rubrikerna:

| Sidhuvud | Typ | Värde |
| --- | --- | --- |
| Auktorisering |Ägar |Ägar används endast godkända auktorisering. Värdet måste också innehålla den åtkomst-token som tillhandahålls av Azure Active Directory. |
| x-ms-version |Decimal |2.17 (eller den senaste versionen)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Eftersom OData använder Media Services för att visa dess REST-API: er, ska DataServiceVersion och MaxDataServiceVersion huvuden tas med i alla förfrågningar. men om de inte kan förutsätter sedan för närvarande Media Services DataServiceVersion värdet används 3.0.
> 
> 

Följande är en uppsättning valfria huvuden:

| Sidhuvud | Typ | Värde |
| --- | --- | --- |
| Date |RFC 1123 datum |Tidsstämpeln för begäran |
| Acceptera |Innehållstyp |Den begärda innehållstypen för svar, till exempel följande:<p> -program/json; odata = verbose<p> -application/atom + xml<p> Svaren kan ha en annan innehållstyp, till exempel en blob-fetch där ett lyckat svar innehåller blob-dataström som skickas. |
| Acceptera-kodning |Gzip, deflate |GZIP och DEFLATE kodning, i tillämpliga fall. Obs: För stora resurser Media Services kan ignorera det här huvudet och returnera data komprimeras. |
| Acceptera språk |”SV”, ”a” och så vidare. |Anger önskat språk för svaret. |
| Acceptera teckenuppsättning |Teckenuppsättning typen like ”UTF-8” |Standardvärdet är UTF-8. |
| X-HTTP-Method |HTTP-metoden |Gör att klienter eller brandväggar som inte stöder HTTP-metoder som till exempel PLACERA eller ta bort för att använda följande metoder, tunneldata via en GET-anrop. |
| Innehållstyp |Innehållstyp |Innehållstypen för begärandetexten i PUT- eller POST-begäranden. |
| Client-request-id |Sträng |En anropare definierat värde som identifierar den angivna begäranden. Om anges med det här värdet i svarsmeddelandet som ett sätt att matcha begäran. <p><p>**Viktigt**<p>Värden ska vara begränsad till 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>HTTP-svarshuvuden stöds av Media Services
Följande är en uppsättning huvuden som kan returneras till dig beroende på resursen som du begärt och den åtgärd som du ska utföra.

| Sidhuvud | Typ | Värde |
| --- | --- | --- |
| id för begäran |Sträng |En unik identifierare för den aktuella åtgärden på tjänsten som genereras. |
| Client-request-id |Sträng |En identifierare som angetts av anroparen i den ursprungliga begäranden om sådan finns. |
| Date |RFC 1123 datum |Datum/tid som begäran bearbetades. |
| Innehållstyp |Det varierar |Innehållstypen för brödtext för svar. |
| Innehållskodning |Det varierar |Gzip och deflate på lämpligt sätt. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Vanliga HTTP-verb som stöds av Media Services
Följande är en fullständig lista över HTTP-verb som kan användas när gör HTTP-begäranden:

| Verb | Beskrivning |
| --- | --- |
| HÄMTA |Returnerar det aktuella värdet för ett objekt. |
| POST |Skapar ett objekt baserat på de data som eller skickar ett kommando. |
| PLACERA |Ersätter ett objekt eller skapar ett namngivet objekt (i förekommande fall). |
| TA BORT |Tar bort ett objekt. |
| SAMMANFOGA |Uppdaterar ett befintligt objekt med namngivna egenskapsändringar. |
| HUVUDET |Returnerar metadata för ett objekt för en GET-svar. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Identifiera och bläddra Media Services entitet modellen
Om du vill göra Media Services entiteter mer synliga kan igen $metadata användas. Det kan du hämta alla giltiga entitetstyper, Entitetsegenskaper, kopplingar, funktioner, åtgärder och så vidare. Du kan öppna discovery-tjänsten genom att lägga till åtgärden $metadata till slutet av Media Services REST API-slutpunkt.

 /API/$ metadata.

Du bör lägga till ”? api-version=2.x” i slutet av URI: N om du vill visa metadata i en webbläsare eller ta inte med x-ms-version-huvudet i begäran.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autentisera med Media Services REST med Azure Active Directory

Autentisering på det REST API görs via Azure Active Directory(AAD).
Mer information om hur du hämtar information om autentisering krävs för ditt Media Services-konto från Azure Portal finns [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md).

Mer information om att skriva kod som ansluter till REST-API med Azure AD-autentisering finns i artikeln [använda Azure AD-autentisering för åtkomst till Azure Media Services-API med övriga](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Nästa steg
Information om hur du använder Azure AD-autentisering med Media Services REST API finns [använda Azure AD-autentisering för åtkomst till Azure Media Services-API med övriga](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

