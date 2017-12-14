---
title: "Konfigurera innehållsskydd principer med hjälp av Azure portal | Microsoft Docs"
description: "Den här artikeln visar hur du använder Azure-portalen för att konfigurera principer för innehållsskydd. Artikeln beskriver också hur du aktiverar dynamisk kryptering för dina tillgångar."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: ecc766abb5df38813b3eb6dde98cdc9afd24ac6b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Konfigurera principer för innehållsskydd med Azure-portalen
Microsoft Azure Media Services (AMS) gör det möjligt för dig att skydda mediet från den tidpunkt som den lämnar din dator via lagring, bearbetning och leverans. Media Services kan du leverera ditt innehåll krypteras dynamiskt med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar), vanliga kryptering (CENC) med PlayReady och/eller Widevine DRM och Apple FairPlay. 

AMS är en tjänst för att leverera DRM-licenser och AES avmarkera nycklar till auktoriserade klienter. Azure-portalen kan du skapa en **nyckel/licens auktoriseringsprincip** för alla typer av krypteringar.

Den här artikeln visar hur du konfigurerar en princip för innehållsskydd med Azure-portalen. Artikeln beskriver också hur du använder dynamisk kryptering till dina tillgångar.

## <a name="start-configuring-content-protection"></a>Börja konfigurera innehållsskydd
Om du vill använda portalen för att börja konfigurera innehållsskydd globala för AMS-kontot, gör du följande:
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **inställningar** > **innehåll skydd**.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Auktoriseringsprincip för nyckel/licens
AMS stöder flera olika sätt att autentisera användare som begär eller licensinformation. Innehållsnyckelns auktoriseringsprincip måste konfigureras av dig och uppfyllas av klienten för nyckel/licensen till delived till klienten. Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar: **öppna** eller **token** begränsning.

Azure-portalen kan du skapa en **nyckel/licens auktoriseringsprincip** för alla typer av krypteringar.

### <a name="open-authorization"></a>Öppna auktorisering
Öppna begränsning innebär att systemet ger nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning. 

### <a name="token-authorization"></a>Token auktorisering
Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formatet Simple Web Tokens (SWT) och JSON-Webbtoken (JWT)-format. Media Services tillhandahåller inte Secure Token tjänster. Du kan skapa en anpassad STS eller använda Microsoft Azure ACS problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Media Services viktiga tjänsten returneras den begärda (eller licensinformation) till klienten om token är giltig och anspråk i token matchar de som konfigurerats för nyckeln (eller licens).

När du konfigurerar token begränsad princip, måste du ange den primära Verifieringsnyckeln och utfärdaren målgruppen parametrar. Primära Verifieringsnyckeln innehåller den nyckel som token som signerats med, utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady license-mall
PlayReady-licensmall anger funktionen aktiverad för PlayReady-licens. Detaljerad information om mallen PlayReady-licens finns [Media Services PlayReady licens mall översikt](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Icke-beständiga
Om du konfigurerar licens som icke-beständig är det endast lagras i minnet medan Windows Media player använder licensen.  

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Beständig
Om du konfigurerar licensen som beständiga sparas den i permanent lagringsutrymme på klienten.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licensmall
Widevine-licensmall anger funktionen aktiverad på Widevine-licenser.

### <a name="basic"></a>Basic
När du väljer **grundläggande**, mallen kommer att skapas med alla standardvärden värden.

### <a name="advanced"></a>Advanced
Detaljerad information om rättighetsprincipmall Widevine finns [Widevine-licens mall översikt](media-services-widevine-license-template-overview.md).

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguration
Om du vill aktivera FairPlay kryptering som du behöver ange App certifikat och programmet hemlig nyckel (be) via FairPlay konfigurationsalternativet. Detaljerad information om FairPlay konfiguration och krav finns [detta](media-services-protect-hls-with-FairPlay.md) artikel.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Tillämpa dynamisk kryptering på din tillgång
Om du vill dra nytta av dynamisk kryptering, måste du koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Välj en tillgång som du vill kryptera
Om du vill se alla dina tillgångar väljer **inställningar** > **tillgångar**.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Kryptera med AES eller DRM
När du trycker på **kryptera** på en tillgång, visas två alternativ: **AES** eller **DRM**. 

#### <a name="aes"></a>AES
AES Rensa nyckelkryptering aktiveras på alla strömningsprotokoll: Smooth Streaming, HLS och MPEG-DASH.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
När du väljer fliken DRM visas med olika alternativ för principer för innehållsskydd (som måste du ha konfigurerat nu) + en uppsättning strömningsprotokoll.

* **PlayReady och Widevine med MPEG-DASH** -krypterar dynamiskt med PlayReady och Widevine DRMs MPEG-DASH strömmen.
* **PlayReady och Widevine med MPEG-DASH + FairPlay med HLS** -dynamiskt krypteras du MPEG-DASH-dataström med PlayReady och Widevine DRMs. Kommer också att kryptera din HLS dataströmmar med FairPlay.
* **PlayReady endast med Smooth Streaming, HLS och MPEG-DASH** -dynamiskt krypteras Smooth Streaming, HLS, MPEG-DASH-dataströmmar med PlayReady DRM.
* **Widevine endast med MPEG-DASH** -dynamiskt kryptera du MPEG-DASH med Widevine DRM.
* **FairPlay endast med HLS** -krypterar dynamiskt HLS strömmen med FairPlay.

Om du vill aktivera FairPlay kryptering som du behöver ange App certifikat och programmet hemlig nyckel (be) via alternativet FairPlay konfiguration av inställningsbladet innehållsskydd.

![Skydda innehåll](./media/media-services-portal-content-protection/media-services-content-protection009.png)

När du gör val av kryptering, tryck på **tillämpa**.

>[!NOTE] 
>Om du planerar att spela upp en AES krypteras HLS i Safari, finns det [krypterade HLS i Safari blogginlägget](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

