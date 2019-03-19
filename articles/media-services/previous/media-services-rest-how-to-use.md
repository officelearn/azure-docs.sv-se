---
title: Media Services operations REST API-översikt | Microsoft Docs
description: Media Services REST API-översikt
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako;johndeu
ms.openlocfilehash: 1e8d21c39a428049594237900aa76f783a3ed0ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860365"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services operations REST API-översikt 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

Den **Media Services Operations REST** API används för att skapa jobb, tillgångar, Live Channels och andra resurser i ett Media Services-konto. Mer information finns i [Media Services Operations REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services tillhandahåller en REST-API som accepterar både JSON eller atom + pub XML-format. Media Services REST-API kräver specifika HTTP-huvuden som varje klient måste skicka när du ansluter till Media Services, samt en uppsättning valfria rubriker. I följande avsnitt beskrivs rubrikerna och HTTP-verb som du kan använda när du skapar förfrågningar och ta emot svar från Media Services.

Autentisering till Media Services REST API görs via Azure Active Directory-autentisering som beskrivs i artikeln [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Överväganden

Följande gäller när du använder REST.

* Vid frågor till entiteter, finns det en gräns på 1000 enheter som returneras i taget eftersom offentlig REST-v2 begränsar frågeresultaten till 1000 resultat. Du måste använda **hoppa över** och **ta** (.NET) / **upp** (REST) enligt beskrivningen i [.NET-exempel](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [detta REST-API exempel](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* När använder JSON och ange om du vill använda den **__metadata** nyckelord i begäran (till exempel till ett länkat-referens) måste du ställa in den **acceptera** sidhuvud till [JSON utförlig format](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(se exemplet nedan). OData förstår inte den **__metadata** -egenskapen i begäran, såvida inte du ange den till utförlig.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standard HTTP-begäranshuvuden som stöds av Media Services
Det finns en uppsättning nödvändiga rubriker som du måste inkludera i din begäran för varje anrop till Media Services, och också en uppsättning valfria huvuden kan du inkludera. I tabellen nedan visas rubrikerna som krävs:

| Huvud | Type | Värde |
| --- | --- | --- |
| Auktorisering |Ägar |Ägar är de enda godkända auktoriseringsmekanism. Värdet måste också innehålla den åtkomsttoken som tillhandahålls av Azure Active Directory. |
| x-ms-version |Decimal |2.17 (eller den senaste versionen)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Eftersom OData använder Media Services för att visa dess REST-API: er, ska sidhuvuden DataServiceVersion och MaxDataServiceVersion ingå i alla begäranden; men om de inte är förutsätter sedan för närvarande medietjänster DataServiceVersion värdet används är 3.0.
> 
> 

Följande är en uppsättning valfria rubriker:

| Huvud | Type | Värde |
| --- | --- | --- |
| Date |RFC 1123 datum |Tidsstämpel för begäran |
| Godkänn |Innehållstyp |Den begärda innehållstypen för svar som följande:<p> -application/json;odata=verbose<p> -application/atom + xml<p> Svaren kan ha en annan innehållstyp, till exempel en blob-fetch där ett lyckat svar innehåller blob-dataströmmen som skickas. |
| Accept-Encoding |Gzip, deflate |GZIP och DEFLATE kodning, när så är tillämpligt. Obs! Media Services kan ignorera den här rubriken och returnera okomprimerade data för stora resurser. |
| Accept-Language |”SV”, ”es” och så vidare. |Anger önskat språk för svaret. |
| Accept-Charset |Teckenuppsättningen typen like ”UTF-8” |Standardvärdet är UTF-8. |
| X-HTTP-Method |HTTP-metod |Tillåter klienter eller brandväggar som inte har stöd för HTTP-metoder som PUT- eller DELETE för att använda dessa metoder, dirigering via en GET-anrop. |
| Content-Type |Innehållstyp |Innehållstypen för begärandetexten i PUT eller POST-begäranden. |
| client-request-id |String |En anropare definierat värde som identifierar den angivna förfrågan. Om det här värdet inkluderas i svarsmeddelandet som ett sätt att matcha begäran. <p><p>**Viktigt**<p>Värden ska vara begränsad till 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>HTTP-svarshuvuden stöds av Media Services
Följande är en uppsättning rubriker som kan returneras till dig beroende på den resurs som du begär och du försöker att utföra åtgärden.

| Huvud | Type | Värde |
| --- | --- | --- |
| request-id |String |En unik identifierare för den aktuella åtgärden på tjänsten som genereras. |
| client-request-id |String |En identifierare som angetts av anroparen i den ursprungliga begäran om sådan finns. |
| Date |RFC 1123 datum |Datum och tid då begäran bearbetades. |
| Content-Type |Varierar |Innehållstypen för svarstexten. |
| Content-Encoding |Varierar |Gzip eller deflate efter behov. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standard HTTP-verb som stöds av Media Services
Följande är en fullständig lista över HTTP-verb som kan användas när att göra HTTP-begäranden:

| verb | Beskrivning |
| --- | --- |
| HÄMTA |Returnerar det aktuella värdet för ett objekt. |
| POST |Skapar ett objekt som baseras på data som tillhandahålls eller skickar ett kommando. |
| PUT |Ersätter ett objekt, eller skapar en namngiven objekt (om tillämpligt). |
| DELETE |Tar bort ett objekt. |
| SAMMANFOGA |Uppdaterar ett befintligt objekt med namngivna egenskapsändringar. |
| HEAD |Returnerar metadata för ett objekt för en GET-svar. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Identifiera och bläddra modellen för Media Services-entitet
Om du vill göra medietjänster entiteter enklare att hitta kan åtgärden $metadata användas. Det kan du hämta alla giltiga entitetstyper, Entitetsegenskaper, associationer, funktioner, åtgärder och så vidare. Du kan komma åt den här identifieringstjänsten genom att lägga till åtgärden $metadata i slutet av Media Services REST API-slutpunkten.

 /API/$ metadata.

Du bör lägga till ”? api-version=2.x” i slutet av URI: N om du vill visa metadata i en webbläsare eller inkluderar inte x-ms-version-huvudet i begäran.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autentisera med Media Services REST med Azure Active Directory

Autentisering på REST API görs via Azure Active Directory.
Mer information om hur du hämtar nödvändiga autentisering-information för Media Services-kontot från Azure Portal finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md).

Mer information om hur du skriver kod som ansluter till REST-API med hjälp av Azure AD-autentisering finns i artikeln [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Nästa steg
Läs hur du använder Azure AD-autentisering med Media Services REST-API i [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

