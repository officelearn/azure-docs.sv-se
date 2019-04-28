---
title: Utveckla videospelarprogram
description: Avsnittet innehåller länkar till Spelarramverk och plugin-program som du kan använda för att utveckla egna klientprogram som kan använda strömmande media från Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465651"
---
# <a name="develop-video-player-applications"></a>Utveckla videospelarprogram
## <a name="overview"></a>Översikt
Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar. Det här avsnittet innehåller också länkar till SDK: er och Spelarramverk som du kan använda för att utveckla egna klientprogram som kan använda strömmande media från Azure Media Services.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) är en web videospelare som utformats för att spela upp medieinnehåll från Microsoft Azure Media Services på en mängd olika webbläsare och enheter. Azure Media Player används branschstandarder som HTML5, Media käll-tillägg (MSE) och tillägg EME (Encrypted Media) för att ge en avancerad och anpassningsbar direktuppspelning. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelningsteknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er. Detta gör innehåll som hanteras av Azure Media Services kan spelas upp på ett brett utbud av enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services kan innehåll tillhandahålls med DASH, Smooth Streaming och HLS att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt den bästa länk beroende på plattform webbläsarkapacitet. Microsoft Azure Media Services kan även användas för dynamisk kryptering av tillgångar med PlayReady-kryptering eller AES-128 bitars kuvert-kryptering. Azure Media Player möjliggör med PlayReady och AES-128 bitars krypterat innehåll när korrekt konfigurerad. 

Mer information:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player-dokumentation](https://aka.ms/ampdocs) 
* [Azure Media Player komma igång blogg](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Registrera dig för att hålla dig uppdaterad med senast från Azure Media Player](https://aka.ms/ampsignup)
* [Lägg till nya funktionbegäran, idéer, feedback](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Andra verktyg för att skapa Player-program
Du kan också använda någon av följande SDK: erna:

* [Smooth Streaming-klient SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store-App](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](https://playerframework.codeplex.com/) 
* [HTML5 Player Framework-dokumentationen](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming-plugin-programmet för OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licensiering Microsoft® jämn direktuppspelning Klientportningskit](https://aka.ms/sspk) 
* [XBOX Video programutveckling](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Reklam
Azure Media Services har stöd för ad-insättning via Windows Media-plattform: Spelarramverk. Spelarramverk med stöd för ad är tillgängliga för Windows 8, Silverlight, Windows Phone 8 och iOS-enheter. Varje player framework innehåller exempelkod som visar hur du implementerar en player-program. Det finns tre olika typer av annonser som du kan lägga till i dina media:

Linjär – fullständig ramens annonser som pausar huvudsakliga videon

Icke-linjära – överlägget annonser som visas när huvudsakliga videon spelas, vanligtvis en logotyp eller andra statiska image som placeras i spelaren

Tillhörande – annonser som visas utanför spelaren

Annonser kan placeras när som helst i tidslinjen för den huvudsakliga video. Du måste meddela spelaren när du vill spela upp ad och vilka annonser att spela upp. Detta görs med hjälp av en uppsättning XML-baserade standardfiler: Video Ad Tjänstmallen (VAST), Digital Video flera Ad spellistan (VMAP), Media abstrahera sekvensering mallen (MAST) och digitala videospelaren Ad gränssnittsdefinitionen (VPAID). STORA filer ange vilka annonser att visa. VMAP anger när du vill spela upp olika annonser och innehålla stora XML. MAST filer är ett annat sätt att sekvens annonser som även kan innehålla stora XML. VPAID filer definierar ett gränssnitt mellan videospelaren och ad eller ad-servern. Mer information finns i [Infoga annonser](https://msdn.microsoft.com/library/dn387398.aspx).

Läs om hur textning och annonser stöd i Live direktuppspelning videor [stöds textning och Ad-insättning standarder](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Bädda in MPEG-DASH-anpassad direktuppspelad video i ett HTML5-program med DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub-lagringsplatsen för dash.js](https://github.com/Dash-Industry-Forum/dash.js)

