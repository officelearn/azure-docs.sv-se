---
title: Översikt över Azure Media Services v3 | Microsoft Docs
description: Den här artikeln innehåller en översikt av Media Services och innehåller länkar till artiklar för mer information.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/29/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 19e94fc65ddc1719c601397adfe77f8f9445e4fa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662133"
---
# <a name="what-is-azure-media-services-v3"></a>Vad är Azure Media Services v3?

Azure Media Services är en molnbaserad plattform för att skapa lösningar för videor i sändningskvalitet för direktuppspelning, förbättra tillgängligheten och distributionen, analysera innehållet och mycket mer. Om du är en programutvecklare, ett callcenter, en myndighet eller ett underhållningsföretag kan Media Services hjälpa dig att skapa program som levererar medieupplevelser med fantastisk kvalitet för stora målgrupper på dagens populäraste mobila enheter och webbläsare. 

> [!NOTE]
> För närvarande kan använda du inte Azure-portalen för att hantera v3-resurser. Använd den [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), eller någon av stöds [SDK: er](developers-guide.md).

## <a name="what-can-i-do-with-media-services"></a>Vad kan jag göra med Media Services?

Media Services hjälper dig att skapa en mängd olika mediearbetsflöden i molnet. Följande är några exempel på vad som kan utföras med Media Services.  

* Leverera video i olika format, så att de kan spelas upp på en mängd olika webbläsare och enheter. För både strömning på begäran och liveuppspelning till olika klienter (mobila enheter, TV, datorer, etc.), måste video- och ljudinnehållet vara kodat och paketerat korrekt. Information om hur du levererar och strömmar innehållet finns i [Snabbstart: Koda och strömma filer](stream-files-dotnet-quickstart.md).
* Strömma sporthändelser live till en stor online-publik, till exempel fotboll, baseball, universitets- och gymnasiesporter med mera. 
* Sänd offentliga möten och händelser såsom för kommunalråd, statsråd och lagstiftande organ.
* Analysera inspelat video- eller ljudinnehåll. För att till exempel uppnå högre kundnöjdhet kan organisationer extrahera tal till text och skapa sökindex och instrumentpaneler. De kan sedan extrahera information kring vanliga klagomål, källor till klagomål och andra relevanta data.
* Skapa en prenumerationsvideotjänst och strömma DRM-skyddat innehåll när en kund (till exempel en filmstudio) behöver begränsa åtkomst och användning av upphovsrättsskyddat arbete.
* Leverera offline-innehåll för uppspelning på flygplan tåg och bilar. En kund kan behöva ladda ner innehåll till sin telefon eller surfplatta för uppspelning när de vet att de kan kopplas ned från nätverket.
* Implementera en videoplattform för e-utbildning med Azure Media Services och [Azure Cognitive Services-API:er](https://docs.microsoft.com/azure/#pivot=products&panel=ai) för tal-till-text, översättning till flera språk, o.s.v. 
* Använd Azure Media Services tillsammans med [API:er för Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) för att lägga till textning och beskrivningar till videor och därmed tillgodose en bredare publik (till exempel personer med handikapp) eller användare som vill kunna läsa på ett annat språk.
* Aktivera Azure CDN för att uppnå hög skalning för att bättre hantera omedelbara höga belastningar (till exempel start av en produktlansering). 

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Designprinciper för v3 API

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3 API:er returnerar inte hemlighet eller autentiseringsuppgifter för en åtgärd av typen **hämta** eller **lista**. Nycklarna är alltid null, tomma eller oberoende av svaret. Du måste anropa en separat åtgärdsmetod för att få hemlighet och autentiseringsuppgifter. Med separata åtgärder kan du ställa in olika RBAC-säkerhetsbehörigheter om vissa API:er hämtar/visar hemligheter medan andra API:er inte gör det. Information om hur du hanterar åtkomst med RBAC finns i dokumentationen om att [hantera åtkomst med RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Exempel på det här kan vara att 

* inte returnera ContentKey-värden i Hämta för StreamingLocator, 
* inte returnera begränsningsnycklar för get för ContentKeyPolicy, 
* inte returnera frågesträngsdelen av webbadressen (för att ta bort signaturen) för jobbets HTTP-indatawebbadresser.

Se exemplet [Hämta princip för innehållsnyckel – .NET](get-content-key-policy-dotnet-howto.md).


## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3? 

Lär dig mer om att koda och paketera, strömma videor på begäran, sända live, analysera dina videoklipp med Media Services v3. Självstudier, API-referenser och annan dokumentation visar hur du säkert levererar ljud- eller videoströmmar på begäran och live, som kan skalas till miljontals användare.

### <a name="quickstarts"></a>Snabbstarter  

Snabbstarterna visar grundläggande dag 1 anvisningar för nya kunder kan snabbt komma igång med Media Services.

* [Stream videofiler – .NET](stream-files-dotnet-quickstart.md)
* [Stream videofiler – CLI](stream-files-cli-quickstart.md)
* [Stream videofiler – Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Självstudier 

Självstudierna visar scenario-baserade procedurer för några av de viktigaste uppgifterna för Media Services.

* [Koda fjärrfil och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda överförda filen och strömma video – .NET](stream-files-tutorial-with-api.md)
* [Stream live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamisk kryptering – .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Instruktionsguider

Artiklar innehåller exempel som visar hur du utför en uppgift. I det här avsnittet hittar du många exempel, här är några av dem:

* [Skapa ett konto – CLI](create-account-cli-how-to.md)
* [Använd API:er – CLI](access-api-cli-how-to.md)
* [Börja utveckla med SDK: er](developers-guide.md)
* [Koda med HTTPS som indata - jobb .NET](job-input-from-http-how-to.md)  
* [Övervaka händelser – Portal](monitor-events-portal-how-to.md)
* [Kryptera dynamiskt med multi-DRM - .NET](protect-with-drm.md) 
* [Koda med en anpassad transformering med hjälp av CLI](custom-preset-cli-howto.md)

## <a name="next-steps"></a>Nästa steg

Hur kan jag komma igång med v3? 

> [!div class="nextstepaction"]
> [Lär dig mer om grundläggande begrepp](concepts-overview.md)<br/>
> [Utveckla med Media Services v3-API med SDK: er](developers-guide.md) 

