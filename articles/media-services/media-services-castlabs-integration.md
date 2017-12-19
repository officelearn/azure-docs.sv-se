---
title: "Med hjälp av castLabs för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRMs. PlayReady-licens kommer från licensserver för Media Services PlayReady och Widevine-licens levereras med castLabs licensservern."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: 5b69e804809f834e81221fb2787a997a52dbe286
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd castLabs för att leverera Widevine-licenser till Azure Media Services
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRMs. PlayReady-licens kommer från licensserver för Media Services PlayReady och Widevine-licens levereras av **castLabs** licensservern.

Att spela upp innehåll som skyddas av CENC (PlayReady eller Widevine) för strömning, kan du använda [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Se [AMP dokumentet](http://amp.azure.net/libs/amp/latest/docs/) mer information.

Följande diagram visar en övergripande Azure Media Services och castLabs integrering arkitektur.

![Integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Vanliga systemet
* Medieinnehåll lagras i AMS.
* Nyckel-ID: N innehåll nycklar lagras i både castLabs och AMS.
* castLabs och AMS har inbyggda-tokenautentisering. I följande avsnitt beskrivs autentiseringstoken. 
* När en klient begär för att strömma videon, innehållet krypteras dynamiskt med **Common Encryption** (CENC) och dynamiskt paketerade av AMS Smooth Streaming-och STRECK. Vi kan också leverera PlayReady M2TS elementära dataströmmen kryptering för HLS strömningsprotokoll.
* PlayReady-licens har hämtats från AMS licensservern och Widevine-licens har hämtats från castLabs licensservern. 
* Media Player bestämmer automatiskt vilka licens för att hämta baserat på klientens plattformskapacitet. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Autentisering-token generering för att hämta en licens
Både castLabs och AMS stöder JWT (JSON Web Token) token-format som används för att auktorisera en licens. 

### <a name="jwt-token-in-ams"></a>JWT-token i AMS
I följande tabell beskrivs JWT-token i AMS. 

| Utfärdare | Utfärdaren sträng från valt Secure säkerhetstokentjänst (STS) |
| --- | --- |
| Målgrupp |Målgruppen sträng från används STS |
| Anspråk |En uppsättning anspråk |
| Inte före |Starta tokens giltighet |
| Förfaller |End giltigheten hos token |
| SigningCredentials |Den nyckel som delas med PlayReady licensservern castLabs licensservern och STS, det kan vara antingen symmetriskt eller asymmetriskt nyckel. |

### <a name="jwt-token-in-castlabs"></a>JWT-token i castLabs
I följande tabell beskrivs JWT-token i castLabs. 

| Namn | Beskrivning |
| --- | --- |
| optData |En JSON-sträng som innehåller information om dig. |
| CRT |En JSON-sträng som innehåller information om tillgången, dess licens info och uppspelning rättigheter. |
| IAT |Aktuella datetime i epok. |
| jti |En unik identifierare om denna token (varje token kan bara användas en gång i castLabs system). |

## <a name="sample-solution-set-up"></a>Ställ in exempellösning
Den [exempel lösning](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) består av två projekt:

* En konsolapp som kan användas för att ange DRM begränsningar för en redan infogade tillgång för både PlayReady och Widevine.
* Ett webbprogram som lämnar ut tokens som kan ses som en mycket förenklad version av en Säkerhetstokentjänst.

Använda konsolprogrammet:

1. Ändra app.config om du vill konfigurera AMS autentiseringsuppgifter, castLabs autentiseringsuppgifter, STS-konfiguration och delad nyckel.
2. Ladda upp en tillgång till AMS.
3. Hämta UUID från den överförda tillgången och ändra rad 32 i filen Program.cs:
   
      var objIAsset = _context. Assets.Where (x = > x.Id == ”nb:cid:UUID:dac53a5d-1 500-80bd-b864-f1e4b62594cf”). FirstOrDefault();
4. Använd en AssetId för namngivning av tillgången i castLabs system (44 raden i filen Program.cs).
   
   Du måste ange AssetId för **castLabs**; måste vara en unik alfanumerisk sträng.
5. Kör programmet.

Använda Web Application (STS):

1. Ändra filen web.config till installationsprogrammet castlabs tillhör affärsenhet ID, STS-konfigurationen och den delade nyckeln.
2. Distribuera till Azure Websites.
3. Gå till webbplatsen.

## <a name="playing-back-a-video"></a>Spela upp en video
Att spela upp en video som krypterats med vanliga kryptering (PlayReady eller Widevine), kan du använda den [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). När du kör appen konsolen eko innehåll nyckel-ID och Manifest-URL.

1. Öppna en ny flik och starta din STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Gå till [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Klistra in i strömnings-URL.
4. Klicka på den **avancerade alternativ** kryssrutan.
5. I den **skydd** listrutan, Välj PlayReady och/eller Widevine.
6. Klistra in den token som du har fått från din STS i textrutan Token. 
   
   Licensservern castLab behöver inte den ”ägar =” prefix framför token. Så ta bort som innan du skickar token.
7. Uppdatera spelaren.
8. Bör spela upp videon.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

