---
title: Översikt över REST API-api för mediatjänsters operationer | Microsoft-dokument
description: API:et "Media Services Operations REST" används för att skapa jobb, tillgångar, livekanaler och andra resurser i ett Media Services-konto. Den här artikeln innehåller en Azure Media Services v2 REST API-översikt.
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
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 597839f633ed2b925b86c5f859a0fb2d3b64dd59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773661"
---
# <a name="media-services-operations-rest-api-overview"></a>Översikt över REST-API-API för Media Services-åtgärder 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

REST-API:et för **Media Services Operations** används för att skapa jobb, tillgångar, livekanaler och andra resurser i ett Media Services-konto. Mer information finns i [Media Services Operations REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services tillhandahåller ett REST API som accepterar både JSON- eller atom+pub XML-format. REST API för Media Services kräver specifika HTTP-huvuden som varje klient måste skicka när de ansluter till Media Services, samt en uppsättning valfria rubriker. I följande avsnitt beskrivs de rubriker och HTTP-verb som du kan använda när du skapar begäranden och tar emot svar från Media Services.

Autentisering till REST-API:et för Media Services sker via Azure Active Directory-autentisering som beskrivs i artikeln [Använd Azure AD-autentisering för att komma åt Azure Media Services API med REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Överväganden

Följande överväganden gäller när du använder REST.

* Vid fråga entiteter finns det en gräns på 1 000 entiteter som returneras samtidigt eftersom offentliga REST v2 begränsar frågeresultat till 1 000 resultat. Du måste använda **Skip** and **Take** (.NET)/ **top** (REST) enligt beskrivningen i det [här .NET-exemplet](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [det här REST API-exemplet](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* När du använder JSON och anger att nyckelordet **__metadata** i begäran (till exempel för att referera till ett länkat objekt) måste du ange **formatet Acceptera** till [JSON-verbos (se](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) följande exempel). Odata förstår inte **egenskapen __metadata** i begäran, såvida du inte ställer in den på att verbose.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standard-HTTP-begäranden som stöds av Media Services
För varje samtal du gör till Media Services finns det en uppsättning obligatoriska rubriker som du måste inkludera i din begäran och även en uppsättning valfria rubriker som du kanske vill inkludera. I tabellen nedan visas de rubriker som krävs:

| Huvud | Typ | Värde |
| --- | --- | --- |
| Auktorisering |Bearer |Bärare är den enda godkända tillståndsmekanismen. Värdet måste också innehålla åtkomsttoken som tillhandahålls av Azure Active Directory. |
| x-ms-version |Decimal |2.17 (eller senaste versionen)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Eftersom Media Services använder OData för att exponera sina REST-API:er bör rubrikerna DataServiceVersion och MaxDataServiceVersion inkluderas i alla begäranden. Men om de inte är det, antar Media Services för närvarande att dataserviceversion-värdet som används är 3.0.
> 
> 

Följande är en uppsättning valfria rubriker:

| Huvud | Typ | Värde |
| --- | --- | --- |
| Datum |RFC 1123 datum |Tidsstämpel för begäran |
| Acceptera |Innehållstyp |Den begärda innehållstypen för svaret, till exempel följande:<p> -application/json;odata=verbose<p> - ansökan /atom+xml<p> Svaren kan ha en annan innehållstyp, till exempel en blob fetch, där ett lyckat svar innehåller blob-strömmen som nyttolast. |
| Acceptera-kodning |Gzip, töm |GZIP- och DEFLATE-kodning, i förekommande fall. För stora resurser kan Media Services ignorera det här huvudet och returnera icke-komprimerade data. |
| Accept-Language |"en", "es", och så vidare. |Anger önskat språk för svaret. |
| Acceptera-Charset |Charset typ som "UTF-8" |Standard är UTF-8. |
| X-HTTP-metod |HTTP-metod |Tillåter klienter eller brandväggar som inte stöder HTTP-metoder som PUT eller DELETE att använda dessa metoder, tunnel via ett GET-anrop. |
| Content-Type |Innehållstyp |Innehållstyp för begärandetexten i PUT- eller POST-begäranden. |
| klient-begäran-id |String |Ett anropardefinierat värde som identifierar den angivna begäran. Om det anges inkluderas det här värdet i svarsmeddelandet som ett sätt att mappa begäran. <p><p>**Viktigt**<p>Värdena bör begränsas till 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standard-HTTP-svarshuvuden som stöds av Media Services
Följande är en uppsättning rubriker som kan returneras till dig beroende på vilken resurs du begärde och vilken åtgärd du avsåg att utföra.

| Huvud | Typ | Värde |
| --- | --- | --- |
| begäran-id |String |En unik identifierare för den aktuella åtgärden, tjänst som genereras. |
| klient-begäran-id |String |En identifierare som anges av anroparen i den ursprungliga begäran, om sådan finns. |
| Datum |RFC 1123 datum |Datum/tid då begäran bearbetades. |
| Content-Type |Varierar |Innehållstypen för svarstexten. |
| Content-Encoding |Varierar |Gzip eller töm, beroende på vad som är tillämpligt. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standard-HTTP-verb som stöds av Media Services
Följande är en fullständig lista över HTTP-verb som kan användas vid http-begäranden:

| Verb | Beskrivning |
| --- | --- |
| HÄMTA |Returnerar det aktuella värdet för ett objekt. |
| POST |Skapar ett objekt baserat på de data som tillhandahålls eller skickar ett kommando. |
| PUT |Ersätter ett objekt eller skapar ett namngivet objekt (i förekommande fall). |
| DELETE |Tar bort ett objekt. |
| Sammanfoga |Uppdaterar ett befintligt objekt med namngivna egenskapsändringar. |
| HEAD |Returnerar metadata för ett objekt för ett GET-svar. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Upptäck och bläddra i entitymodellen Media Services
För att göra Media Services-entiteter mer upptäckbara kan $metadata-åtgärden användas. Det gör att du kan hämta alla giltiga entitetstyper, entitetsegenskaper, associationer, funktioner, åtgärder och så vidare. Genom att lägga till $metadata-åtgärden i slutet av slutpunkten för REST API-api för Media Services kan du komma åt den här identifieringstjänsten.

 /api/$metadata.

Du bör lägga till "?api-version=2.x" i slutet av URI om du vill visa metadata i en webbläsare, eller inte inkludera x-ms-versionshuvudet i din begäran.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autentisera med Media Services REST med Azure Active Directory

Autentisering på REST API görs via Azure Active Directory(AAD).
Mer information om hur du får nödvändig autentiseringsinformation för ditt Media Services-konto från Azure Portal finns i [Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md).

Mer information om hur du skriver kod som ansluter till REST API med Azure AD-autentisering finns i artikeln [Använd Azure AD-autentisering för att komma åt Azure Media Services API med REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Azure AD-autentisering med REST API för Media Services finns i [Använda Azure AD-autentisering för att komma åt Azure Media Services API med REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

