---
title: Översikt över Azure Media Services v3
titleSuffix: Azure Media Services
description: En översikt på hög nivå över Azure Media Services v3 med länkar till snabbstarter, självstudier och kodexempel.
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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: bd3890757377525cf9c178866a2a2fbc0791b9de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79461018"
---
# <a name="azure-media-services-v3-overview"></a>Översikt över Azure Media Services v3

Azure Media Services är en molnbaserad plattform för att skapa lösningar för videor i sändningskvalitet för direktuppspelning, förbättra tillgängligheten och distributionen, analysera innehållet och mycket mer. Oavsett om du är apputvecklare, ett callcenter, en myndighet eller ett underhållningsföretag hjälper Media Services dig att skapa appar som levererar medieupplevelser av enastående kvalitet till stora målgrupper på dagens mest populära mobila enheter och webbläsare.

Media Services v3 SDK:er baseras på [Media Services v3 OpenAPI Specification (Swagger).](https://aka.ms/ams-v3-rest-sdk)

> [!NOTE]
> För närvarande kan du använda [Azure-portalen](https://portal.azure.com/) för att: hantera Media Services v3 [Live Events](live-events-outputs-concept.md), visa (inte hantera) [v3-tillgångar](assets-concept.md), [få information om hur du använder API: er](access-api-portal.md). För alla andra hanteringsuppgifter (till exempel [Transforms and Jobs](transforms-jobs-concept.md) and [Content protection)](content-protection-overview.md)använder du [REST API](https://docs.microsoft.com/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK:er som](media-services-apis-overview.md#sdks)stöds .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Azure Media Services, och du får inte använda Media Services eller någon Azure-tjänst på ett sätt som bryter mot andras rättigheter eller som kan vara skadliga för andra.

Innan du laddar upp någon video/bild till Media Services måste du ha alla rättigheter att använda videon/bilden, inklusive, där så krävs enligt lag, alla nödvändiga medgivanden från enskilda personer (om sådana finns) i videon/bilden, för användning, bearbetning och lagring av deras data i Media Services och Azure. Vissa jurisdiktioner kan införa särskilda rättsliga krav för insamling, onlinebehandling och lagring av vissa kategorier av uppgifter, såsom biometriska uppgifter. Innan du använder Media Services och Azure för bearbetning och lagring av data som omfattas av särskilda juridiska krav måste du se till att sådana juridiska krav som kan gälla för dig uppfylls.

Mer information om efterlevnad, sekretess och säkerhet i Media Services finns i Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). Om du har Microsofts sekretessskyldigheter, datahantering och lagringspraxis, inklusive hur du tar bort dina data, läser du Microsofts [sekretesspolicy,](https://privacy.microsoft.com/PrivacyStatement) [villkoren för onlinetjänster](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") och [tillägget för databehandling](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Genom att använda Medietjänster samtycker du till att vara bunden av OST, DPA och sekretesspolicyn.
 
## <a name="what-can-i-do-with-media-services"></a>Vad kan jag göra med Media Services?

Med Media Services kan du skapa en mängd olika mediearbetsflöden i molnet. Några exempel på vad du kan göra med Medietjänster är:

* Leverera video i olika format, så att de kan spelas upp på en mängd olika webbläsare och enheter. För både on-demand och live streaming leverans till olika klienter (mobila enheter, TV, PC, och så vidare), video-och ljudinnehåll måste kodas och paketeras på lämpligt sätt. Information om hur du levererar och strömmar innehållet finns i [Snabbstart: koda och strömma filer](stream-files-dotnet-quickstart.md).
* Streama direktsända sportevenemang till en stor online-publik, som fotboll, baseball, college och high school sport, och mycket mer.
* Sända offentliga möten och evenemang, som rådhus, kommunfullmäktigemöten och lagstiftande organ.
* Analysera inspelat video- eller ljudinnehåll. För att till exempel uppnå högre kundnöjdhet kan organisationer extrahera tal till text och skapa sökindex och instrumentpaneler. De kan sedan extrahera information kring vanliga klagomål, källor till klagomål och andra relevanta data.
* Skapa en prenumerationsvideotjänst och strömma DRM-skyddat innehåll när en kund (till exempel en filmstudio) behöver begränsa åtkomst och användning av upphovsrättsskyddat arbete.
* Leverera offline-innehåll för uppspelning på flygplan tåg och bilar. En kund kan behöva ladda ner innehåll till sin telefon eller surfplatta för uppspelning när de vet att de kan kopplas ned från nätverket.
* Implementera en utbildningsvideoplattform för e-utbildning med Azure Media Services- och [Azure Cognitive Services API:er](https://docs.microsoft.com/azure/?pivot=products&panel=ai) för tal-till-text-texttextning, översättning till flera språk och så vidare.
* Använd Azure Media Services tillsammans med [API:er för Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) för att lägga till textning och beskrivningar till videor och därmed tillgodose en bredare publik (till exempel personer med handikapp) eller användare som vill kunna läsa på ett annat språk.
* Aktivera Azure CDN för att uppnå hög skalning för att bättre hantera omedelbara höga belastningar (till exempel start av en produktlansering).

## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3? 

Läs om hur du kodar och paketerar innehåll, streamar videor på begäran, sänder live och analyserar dina videor med Media Services v3. Självstudier, API-referenser och annan dokumentation visar hur du säkert levererar ljud- eller videoströmmar på begäran och live, som kan skalas till miljontals användare.

> [!TIP]
> Innan du börjar utveckla, granska:<br/>* [Grundläggande begrepp](concepts-overview.md) (incudes viktiga begrepp, som förpackning, kodning och skydd)<br/>* [Utveckla med Api:er för mediatjänster v3](media-services-apis-overview.md) (innehåller information om åtkomst till API:er, namngivningskonventioner och så vidare)

### <a name="sdks"></a>SDK:er

Börja utveckla med [Azure Media Services v3-klient-SDK:er](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Snabbstarter  

Snabbstarterna visar grundläggande dag-1-instruktioner för nya kunder att snabbt prova Media Services.

* [Strömma videofiler – .NET](stream-files-dotnet-quickstart.md)
* [Strömma videofiler – CLI](stream-files-cli-quickstart.md)
* [Strömma videofiler – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>självstudiekurserna

Självstudierna visar scenariobaserade procedurer för några av de vanligaste Media Services-uppgifterna.

* [Koda fjärrfiler och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda uppladdade filer och strömma video – NET](stream-files-tutorial-with-api.md)
* [Strömma live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128, dynamisk kryptering – .NET](protect-with-aes128.md)

### <a name="samples"></a>Exempel

Använd [den här exempelwebbläsaren](https://docs.microsoft.com/samples/browse/?products=azure-media-services) för att bläddra bland Azure Media Services-kodexempel.

### <a name="how-to-guides"></a>Anvisningar

Så här innehåller guider kodexempel som visar hur du slutför en uppgift. I det här avsnittet hittar du många exempel. Här är några av dem:

* [Skapa ett konto – CLI](create-account-cli-how-to.md)
* [Använd API:er – CLI](access-api-cli-how-to.md)
* [Koda med HTTPS som jobbinmatning - .NET](job-input-from-http-how-to.md)  
* [Övervaka händelser – portalen](monitor-events-portal-how-to.md)
* [Kryptera dynamiskt med multi-DRM - .NET](protect-with-drm.md) 
* [Så här kodar du med en anpassad transformering - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om grundläggande begrepp](concepts-overview.md)
