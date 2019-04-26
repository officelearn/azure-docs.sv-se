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
ms.date: 04/18/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 6876ffd76c5a7ba5745e8014bca2abdc5e5264a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60321952"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3-översikt

Azure Media Services är en molnbaserad plattform för att skapa lösningar för videor i sändningskvalitet för direktuppspelning, förbättra tillgängligheten och distributionen, analysera innehållet och mycket mer. Om du är en programutvecklare, ett callcenter, en myndighet eller ett underhållningsföretag kan Media Services hjälpa dig att skapa program som levererar medieupplevelser med fantastisk kvalitet för stora målgrupper på dagens populäraste mobila enheter och webbläsare. 

> [!NOTE]
> För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) eller en av [SDK:erna som stöds](developers-guide.md).

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

## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3? 

Lär dig hur du kodar och paketerar innehåll, strömmar videor på begäran, sänder live och analyserar dina videoklipp med Media Services v3. Självstudier, API-referenser och annan dokumentation visar hur du säkert levererar ljud- eller videoströmmar på begäran och live, som kan skalas till miljontals användare.

Innan du börjar utveckla granska [grundläggande begrepp](concepts-overview.md)<br/>

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
* [Börja utveckla med SDK:er](developers-guide.md)
* [Koda med HTTPS som indata - jobb .NET](job-input-from-http-how-to.md)  
* [Övervaka händelser – Portal](monitor-events-portal-how-to.md)
* [Kryptera dynamiskt med multi-DRM - .NET](protect-with-drm.md) 
* [Koda med en anpassad transformering – CLI](custom-preset-cli-howto.md)

## <a name="provide-feedback"></a>Ge feedback

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Hur kan jag komma igång med v3? 

> [!div class="nextstepaction"]
> [Lär dig mer om grundläggande begrepp](concepts-overview.md)<br/>
> [Utveckla med Media Services v3-API med SDK: er](developers-guide.md) 

