---
title: Använd castLabs för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs
description: 'Den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRM: er. PlayReady-licensen kommer från licensserver för Media Services PlayReady och Widevine-licens levereras av castLabs licensservern.'
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
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: dfb82e91b0f65b85d34b7e20d57ed9929469321f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863265"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd castLabs för att leverera Widevine-licenser till Azure Media Services 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Översikt

Den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en dataström som krypteras dynamiskt med AMS med både PlayReady och Widevine DRM: er. PlayReady-licensen kommer från licensserver för Media Services PlayReady och Widevine-licens levereras av **castLabs** licensservern.

Du kan använda för att spela upp direktuppspelade innehåll som skyddas av CENC (PlayReady och/eller Widevine), [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Se [AMP dokumentet](https://amp.azure.net/libs/amp/latest/docs/) mer information.

Följande diagram visar en övergripande Azure Media Services och castLabs Integreringsarkitektur.

![Integrering](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Vanliga system som ställer in

* Medieinnehåll lagras i AMS.
* Nyckel-ID: N för nycklar lagras i både castLabs och AMS.
* både castLabs och AMS har inbyggd autentisering med enhetstoken. I följande avsnitt beskrivs autentiseringstoken. 
* När en klient begär för att strömma videon, innehållet krypteras dynamiskt med **Common Encryption** (CENC) och dynamiskt paketerade av AMS Smooth Streaming och DASH. Vi kan också leverera PlayReady M2TS elementär stream kryptering för HLS strömningsprotokoll.
* PlayReady-licens från AMS-licensserver hämtas och Widevine-licens har hämtats från castLabs licensservern. 
* Media Player avgör automatiskt vilken licens att hämta baserat på plattformsfunktion för klienten. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Autentisering tokengenerering för att få en licens

Både castLabs och AMS stöder tokenformat för JWT (JSON Web Token) används för att auktorisera en licens. 

### <a name="jwt-token-in-ams"></a>JWT-token i AMS

I följande tabell beskrivs JWT-token i AMS. 

| Utfärdare | Utfärdaren sträng från valt skydda säkerhetstokentjänst (STS) |
| --- | --- |
| Målgrupp |Publik sträng från används STS |
| Anspråk |En uppsättning anspråk |
| NotBefore |Starta tokens giltighet |
| Går ut |End giltigheten hos token |
| SigningCredentials |Den nyckel som delas med PlayReady-servern för fjärrskrivbordslicenser, castLabs licensservern och STS, det kan vara antingen symmetriskt eller asymmetriskt nyckel. |

### <a name="jwt-token-in-castlabs"></a>JWT-token i castLabs

I följande tabell beskrivs JWT-token i castLabs. 

| Namn | Beskrivning |
| --- | --- |
| optData |En JSON-sträng som innehåller information om dig. |
| CRT |En JSON-sträng som innehåller information om tillgången, dess licensrättigheter för information och uppspelning. |
| IAT |Den aktuella datumet/tiden i epoch. |
| jti |En unik identifierare om denna token (varje token kan bara användas en gång i castLabs systemet). |

## <a name="sample-solution-setup"></a>Exemplet lösning installationen

Den [exempel lösning](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) består av två projekt:

* En konsolapp som kan användas för att ställa in DRM begränsningar för en redan insamlade tillgång för både PlayReady och Widevine.
* Ett webbprogram som lämnar ut token, vilket kan ses som en mycket förenklad version av en Säkerhetstokentjänst.

Använda konsolprogrammet:

1. Ändra app.config för hur du konfigurerar AMS autentiseringsuppgifter, castLabs autentiseringsuppgifter, STS konfiguration och delad nyckel.
2. Överföra en tillgång till AMS.
3. Hämta UUID från överförda tillgången och ändra rad 32 i filen Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Använd en AssetId för namngivning av tillgången i castLabs system (Line 44 i filen Program.cs).
   
   Du måste ange AssetId för **castLabs**; måste vara en alfanumerisk sträng som unikt.
5. Kör programmet.

Använda Web Application (STS):

1. Ändra web.config till installationsprogrammet castlabs återförsäljaren ID, STS-konfigurationen och den delade nyckeln.
2. Distribuera till Azure Websites.
3. Gå till webbplatsen.

## <a name="playing-back-a-video"></a>Spela upp en video

Om du vill spela upp en video som krypterats med gemensam kryptering (PlayReady och/eller Widevine), kan du använda den [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html). När du kör konsolappen eko innehåll nyckel-ID och Manifest-URL.

1. Öppna en ny flik och starta din STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Gå till [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Klistra in strömnings-URL.
4. Klicka på den **avancerade alternativ** kryssrutan.
5. I den **Protection** listrutan, väljer PlayReady och/eller Widevine.
6. Klistra in den token som du fick från din STS i Token-textrutan. 
   
   Licensservern castLab behöver inte den ”ägar =” prefix framför token. Så ta bort som innan du skickar token.
7. Uppdatera spelaren.
8. Bör spela upp videon.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

