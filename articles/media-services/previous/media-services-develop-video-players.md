---
title: Utveckla videospelarprogram
description: Avsnittet innehåller länkar till Spelarramverk och plugin-program som du kan använda för att utveckla egna klientprogram som kan använda strömmande media från Media Services.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 71d8b020fe96094c15965fd82615e3182e333990
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788692"
---
# <a name="develop-video-player-applications"></a>Utveckla videospelarprogram
## <a name="overview"></a>Översikt
Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar. Det här avsnittet innehåller också länkar till SDK: er och Spelarramverk som du kan använda för att utveckla egna klientprogram som kan använda strömmande media från Azure Media Services.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](http://aka.ms/ampinfo) är en webbtjänst videospelare som byggts för att spela upp medieinnehåll från Microsoft Azure Media Services på en mängd olika webbläsare och enheter. Azure Media Player använder branschstandarder som HTML5 Media källa tillägg (mus) och krypterade Media tillägg (EME) att tillhandahålla en utökade anpassningsbar strömning. När dessa normer inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelning-teknik som används, kommer utvecklare har ett enhetligt gränssnitt JavaScript API: er. Detta ger innehåll som hanteras av Azure Media Services ska spelas upp över ett brett utbud av enheter och webbläsare utan någon extra arbete.

Microsoft Azure Media Services kan för innehållet ska hanteras med DASH, Smooth Streaming och HLS strömningsformat att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt länken bästa baserat på de plattform/webbläsarfunktioner. Microsoft Azure Media Services kan även användas för dynamisk kryptering av tillgångar med PlayReady-kryptering eller AES-128-bitars kryptering för intervall. Azure Media Player kan för dekryptering av PlayReady och AES-128-bitars krypterat innehåll när korrekt konfigurerad. 

Mer information:

* [Azure Media Player](http://aka.ms/ampinfo)
* [Dokumentation för Azure Media Player](http://aka.ms/ampdocs) 
* [Azure Media Player komma igång blogg](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Registrera dig för att hålla dig uppdaterad med den senaste versionen från Azure Media Player](http://aka.ms/ampsignup)
* [Lägga till nya funktioner som efterfrågas, idéer, feedback](http://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Andra verktyg för att skapa Player-program
Du kan också använda någon av följande SDK:

* [Utjämna strömmande klient-SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store-App](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](http://playerframework.codeplex.com/) 
* [HTML5 Player Framework-dokumentationen](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming-plugin-programmet för OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licensiering Microsoft® Smooth Streaming klienten portera Kit](http://aka.ms/sspk) 
* [XBOX Video programutveckling](http://xbox.create.msdn.com/) 

## <a name="advertising"></a>Reklam
Azure Media Services tillhandahåller support för ad infogning via Windows Media-plattformen: Spelarramverk. Spelarramverk med stöd för ad är tillgängliga för Windows 8, Silverlight, Windows Phone 8 och iOS-enheter. Varje player framework innehåller exempelkod som visar hur du implementerar en player-programmet. Det finns tre olika typer av annonser som du kan infoga i media:

Linjär – fullständig ram annonser som pausar huvudsakliga video

Linjära – överlägget annonser som visas när den huvudsakliga videon spelas, vanligtvis en logotyp eller andra statisk avbildning som placerats i Windows Media player

Medföljande – annonser som visas utanför Windows Media player

Annonser kan placeras på valfri plats i tidslinjen för den huvudsakliga video. Du måste se Windows Media player när spela upp ad och vilka annonser som ska spelas upp. Detta görs med hjälp av en uppsättning XML-baserade standardfiler: Video Ad Service mall (VAST), Digital Video flera Ad spelningslista (VMAP), Media abstrakt ordningsföljd mall (MAST) och Digital Video Player Ad Interface Definition (VPAID). STORA filer ange vilka annonser ska visas. VMAP filer ange när att spela upp olika annonser och innehålla stora XML. MAST filer är ett annat sätt att sekvens annonser som även kan innehålla stora XML. VPAID filer definierar ett gränssnitt mellan videospelaren och ad eller ad-server. Mer information finns i [lägga till Ads](https://msdn.microsoft.com/library/dn387398.aspx).

Information om textning och Active Directory-stöd i direktsänd strömning videor finns [stöds textning och Ad infogning standarder](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Bädda in MPEG-DASH-anpassad direktuppspelad video i ett HTML5-program med DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js-databas](https://github.com/Dash-Industry-Forum/dash.js)

