---
title: Använda castLabs för att leverera Widevine-licenser till Azure Media Services | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en ström som är dynamiskt krypterad av AMS med både PlayReady- och Widevine DRMs.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969148"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd castLabs för att leverera Widevine-licenser till Azure Media Services 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en ström som är dynamiskt krypterad av AMS med både PlayReady- och Widevine DRMs. PlayReady-licensen kommer från Licensservern Media Services PlayReady och Widevine-licensen levereras av **castLabs** licensserver.

Om du vill spela upp direktuppspelat innehåll som skyddas av CENC (PlayReady och/eller Widevine) kan du använda [Azure Media Player](https://aka.ms/azuremediaplayer). Mer information finns i [AMP-dokument.](https://amp.azure.net/libs/amp/latest/docs/)

Följande diagram visar en hög nivå Azure Media Services och castLabs integration arkitektur.

![Integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typisk systemuppsättning

* Medieinnehåll lagras i AMS.
* Nyckel-ID för innehållsnycklar lagras i både castLabs och AMS.
* castLabs och AMS har båda inbyggd tokenautentisering. I följande avsnitt beskrivs autentiseringstoken. 
* När en klient begär att strömma videon krypteras innehållet dynamiskt med **gemensam kryptering** (CENC) och paketeras dynamiskt av AMS till Smooth Streaming och DASH. Vi levererar även PlayReady M2TS elementär strömkryptering för HLS streamingprotokoll.
* PlayReady-licensen hämtas från AMS-licensservern och Widevine-licensen hämtas från castLabs licensserver. 
* Media Player bestämmer automatiskt vilken licens att hämta baserat på klientplattformens kapacitet. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Autentiseringstokengenerering för att hämta en licens

Både castLabs och AMS stöder JWT(JSON Web Token) tokenformat som används för att auktorisera en licens. 

### <a name="jwt-token-in-ams"></a>JWT-token i AMS

I följande tabell beskrivs JWT-token i AMS. 

| Utfärdare | Utfärdarsträng från vald Secure Token Service (STS) |
| --- | --- |
| Målgrupp |Målgruppssträng från den använda STS |
| Anspråk |En uppsättning anspråk |
| NotBefore |Start giltighet av token |
| Upphör |Slutgiltighet för token |
| SigningCredentials |Nyckeln som delas mellan PlayReady License Server, castLabs License Server och STS, kan det vara antingen symmetrisk eller asymmetrisk nyckel. |

### <a name="jwt-token-in-castlabs"></a>JWT token i castLabs

I följande tabell beskrivs JWT-token i castLabs. 

| Namn | Beskrivning |
| --- | --- |
| optData (optData) |En JSON-sträng som innehåller information om dig. |
| Crt |En JSON-sträng som innehåller information om tillgången, dess licensinformation och uppspelningsrättigheter. |
| Iat |Den aktuella datumtiden i epok. |
| jti (på ett sätt) |En unik identifierare om den här token (varje token kan bara användas en gång i castLabs-systemet). |

## <a name="sample-solution-setup"></a>Exempel på lösningsinstallation

[Provlösningen](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) består av två projekt:

* En konsolapp som kan användas för att ange DRM-begränsningar för en redan intjänad tillgång, för både PlayReady och Widevine.
* Ett webbprogram som delar ut token, som kan ses som en mycket förenklad version av en STS.

Så här använder du konsolprogrammet:

1. Ändra app.config för att konfigurera AMS-autentiseringsuppgifter, castLabs-autentiseringsuppgifter, STS-konfiguration och delad nyckel.
2. Ladda upp en tillgång till AMS.
3. Hämta UUID från den uppladdade tillgången och ändra linje 32 i Program.cs-filen:
   
      var objIAsset = _context. Tillgångar.Var(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Använd en AssetId för att namnge tillgången i castLabs-systemet (rad 44 i Program.cs filen).
   
   Du måste ange AssetId för **castLabs;** Det måste vara en unik alfanumerisk sträng.
5. Kör programmet.

Så här använder du webbprogrammet (STS):

1. Ändra web.config till setup castlabs merchant ID, STS-konfigurationen och den delade nyckeln.
2. Distribuera till Azure-webbplatser.
3. Navigera till webbplatsen.

## <a name="playing-back-a-video"></a>Spela upp en video

Om du vill spela upp en video krypterad med vanlig kryptering (PlayReady och/eller Widevine) kan du använda [Azure Media Player](https://aka.ms/azuremediaplayer). När konsolappen körs eka content key-ID:t och manifest-URL:en.

1. Öppna en ny flik och starta din STS: http://[www.yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Gå till [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Klistra in webbadressen för direktuppspelning.
4. Klicka på kryssrutan **Avancerade alternativ.**
5. I listrutan **Skydd** väljer du PlayReady och/eller Widevine.
6. Klistra in token som du fick från din STS i textrutan Token. 
   
   CastLab-licensservern behöver inte prefixet "Bearer=" framför token. Så ta bort det innan du skickar in token.
7. Uppdatera spelaren.
8. Videon ska spelas upp.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

