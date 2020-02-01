---
title: Utveckla videospelarprogram
description: Avsnittet innehåller länkar till spelares ramverk och plugin-program som du kan använda för att utveckla dina egna klient program som kan använda strömmande medier från Media Services.
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
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906838"
---
# <a name="develop-video-player-applications"></a>Utveckla videospelarprogram
## <a name="overview"></a>Översikt
Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar. Det här avsnittet innehåller också länkar till SDK: er och Player-ramverk som du kan använda för att utveckla egna klient program som kan använda strömmande medier från Azure Media Services.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) är en webb Videos pelare som skapats för att spela upp medie innehåll från Microsoft Azure Media Services på en rad olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att ge en omfattande anpassningsbar strömmande upplevelse. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelnings teknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er. Detta gör att innehåll som hanteras av Azure Media Services kan spelas upp i en mängd olika enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services gör att innehåll kan hanteras med streck, Smooth Streaming och HLS strömnings format för att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt upp den bästa länken utifrån plattforms-och webb läsar funktionerna. Microsoft Azure Media Services också tillåta dynamisk kryptering av till gångar med PlayReady-kryptering eller AES-128-bitars kryptering. Azure Media Player tillåter dekryptering av PlayReady-och AES-128-bitars krypterat innehåll när det är lämpligt konfigurerat. 

Mer information:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player dokumentation](https://aka.ms/ampdocs) 
* [Azure Media Player Komma igång blogg](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Registrera dig för att hålla dig uppdaterad med den senaste från Azure Media Player](https://aka.ms/ampsignup)
* [Lägg till nya funktions begär Anden, idéer, feedback](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Andra verktyg för att skapa Player-program
Du kan också använda någon av följande SDK: er:

* [Smooth Streaming klient-SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store-app](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](https://playerframework.codeplex.com/) 
* [Dokumentation om HTML5 Player Framework](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming-plugin-programmet för OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licens för Microsoft® Smooth Streaming client porting kit](https://aka.ms/sspk) 
* [Utveckling av XBOX video program](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>Reklam
Azure Media Services ger stöd för AD-infogning via Windows Media-plattformen: Player-ramverk. Player-ramverk med stöd för AD är tillgängliga för Windows 8-, Silverlight-, Windows Phone 8-och iOS-enheter. Varje Player-ramverk innehåller exempel kod som visar hur du implementerar ett Player-program. Det finns tre olika typer av annonser som du kan infoga i dina media:

Linjär – hel ram annonser som pausar huvud videon

Dislines – överläggs annonser som visas som huvud video spelas, vanligt vis en logo typ eller en annan statisk bild som placeras i spelaren

Companion – annonser som visas utanför spelaren

Annonser kan placeras var som helst i huvud videons tids linje. Du måste meddela spelaren när annonsen ska spelas upp och vilka annonser som ska spelas upp. Detta görs med hjälp av en uppsättning standard-XML-baserade filer: mall för video AD-tjänst (stor), digital video med flera AD-spel (VMAP), pool för medie abstrakt sekvens (MAST) och Digital Video Player AD-gränssnitts definition (VPAID). STORA filer anger vilka annonser som ska visas. VMAP-filer anger när olika annonser ska spelas upp och innehåller enorma XML-filer. Huvudfiler är ett annat sätt att sekvensera annonser som också kan innehålla enorma XML. VPAID-filer definierar ett gränssnitt mellan Videos pelaren och AD-eller AD-servern. Mer information finns i [Infoga annonser](https://msdn.microsoft.com/library/dn387398.aspx).

Information om dold textning och stöd för annonser i direktsända strömmande videor finns i [stöd för dold textning och AD-infogning](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Bädda in MPEG-DASH-anpassad direktuppspelad video i ett HTML5-program med DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub bindestreck. js-lagringsplats](https://github.com/Dash-Industry-Forum/dash.js)

