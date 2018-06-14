---
title: Konfigurera innehållsskydd principer med hjälp av Azure portal | Microsoft Docs
description: Den här artikeln visar hur du använder Azure-portalen för att konfigurera principer för innehållsskydd. Artikeln beskriver också hur du aktiverar dynamisk kryptering för dina tillgångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 805e1246dbc984582528d2b351d2f14ab2e811fc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
ms.locfileid: "27608622"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurera innehållsskydd principer med hjälp av Azure portal
 Du kan skydda mediet från den tidpunkt som den lämnar din dator via lagring, bearbetning och leverans med Azure Media Services. Du kan använda Media Services för att leverera ditt innehåll krypterad dynamiskt med den Standard AES (Advanced Encryption) med 128-bitars krypteringsnycklar. Du kan också använda den med vanliga kryptering (CENC) med hjälp av PlayReady och/eller Widevine DRM digital rights management () och Apple FairPlay. 

Media Services tillhandahåller en tjänst för att leverera DRM-licenser och AES avmarkera nycklar till auktoriserade klienter. Du kan använda Azure-portalen för att skapa en nyckel-licens auktoriseringsprincip för alla typer av krypteringar.

Den här artikeln visar hur du konfigurerar en princip för innehållsskydd med hjälp av portalen. Artikeln beskriver också hur du använder dynamisk kryptering till dina tillgångar.

## <a name="start-to-configure-content-protection"></a>Börja konfigurera innehållsskydd
Om du vill använda portalen för att konfigurera globala innehållsskydd med hjälp av Media Services-kontot, gör du följande:

1. I den [portal](https://portal.azure.com/), Välj Media Services-kontot.

2. Välj **inställningar** > **innehåll skydd**.

    ![Innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Auktoriseringsprincip för nyckel/licens
Media Services stöder flera olika sätt att autentisera användare som begär eller licensinformation. Du måste konfigurera principen för auktorisering av innehållsnyckel. Klienten måste sedan uppfylla principen innan nyckel/licens kan skickas till den. Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar, öppen eller token begränsningar.

Du kan använda portalen för att skapa en nyckel-licens auktoriseringsprincip för alla typer av krypteringar.

### <a name="open-authorization"></a>Öppna auktorisering
Öppna begränsning innebär att systemet levererar nyckeln till alla som begär nycklar. Den här begränsningen kan vara användbart för testning. 

### <a name="token-authorization"></a>Token auktorisering
Begränsad token principen måste åtföljas av en token som utfärdas av en säkerhetstokentjänst (STS). Media Services stöder token i simple web token (SWT) och JSON-Webbtoken (JWT)-format. Media Services ger inte en Säkerhetstokentjänst. Du kan skapa en anpassad STS eller använda Azure Access Control Service problemet tokens. STS måste konfigureras för att skapa en token som signerats med angiven nyckel och utfärda anspråk som du angav i tokenbegränsningar-konfigurationen. Om token är giltig och anspråk i token som matchar de som konfigurerats för nyckeln (eller licens), returnerar Media Services viktiga tjänsten begärda nyckel (eller licens) till klienten.

Du måste ange primär Verifieringsnyckeln, utfärdare och målgrupp parametrar när du konfigurerar begränsad token-principen. Primär Verifieringsnyckeln innehåller den nyckel som token som signerats med. Utfärdaren är den säkra tokentjänst som utfärdar token. Målgruppen (kallas ibland för scope) beskrivs syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services viktiga tjänsten verifierar att dessa värden i token matchar värdena i mallen.

![Auktoriseringsprincip för nyckel/licens](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady license-mall
PlayReady-licensmall anger de funktioner som är aktiverad för PlayReady-licens. Mer information om mallen PlayReady-licens finns på [Media Services PlayReady licens mall översikt](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Uppdateringsvärdet
Om du konfigurerar en licens som Uppdateringsvärdet lagras den i minnet endast när Windows Media player använder licensen.  

![Uppdateringsvärdet innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Beständig
Om du konfigurerar en licens som beständiga sparas den i permanent lagringsutrymme på klienten.

![Beständiga innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licensmall
Widevine-licensmall anger de funktioner som är aktiverad för Widevine-licenser.

### <a name="basic"></a>Basic
När du väljer **grundläggande**, mallen har skapats med alla standardvärden.

### <a name="advanced"></a>Advanced
Mer information om rättighetsprincipmall Widevine finns i [Widevine-licens mall översikt](media-services-widevine-license-template-overview.md).

![Avancerade innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguration
Välj för att aktivera kryptering FairPlay **FairPlay configuration**. Välj sedan den **App certifikat** och ange den **programmet hemlig nyckel**. Mer information om FairPlay konfiguration och krav finns [skydda ditt innehåll med Apple FairPlay eller Microsoft PlayReady HLS](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguration](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Tillämpa dynamisk kryptering på din tillgång
Om du vill dra nytta av dynamisk kryptering, koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Välj en tillgång som du vill kryptera
Om du vill se alla dina tillgångar väljer **inställningar** > **tillgångar**.

![Alternativet tillgångar](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Kryptera med AES eller DRM
När du väljer **kryptera** i en tillgång, ser du två alternativ: **AES** eller **DRM**. 

#### <a name="aes"></a>AES
AES Rensa kryptering är aktiverat på alla strömningsprotokoll: Smooth Streaming, HLS och MPEG-DASH.

![Krypteringskonfiguration](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. När du har valt **DRM**, du se olika innehållsskydd principer (som måste konfigureras med den här punkten) och en uppsättning strömningsprotokoll:

    a. **PlayReady och Widevine med MPEG-DASH** krypterar dynamiskt med PlayReady och Widevine DRMs MPEG-DASH strömmen.

    b. **PlayReady och Widevine med MPEG-DASH + FairPlay med HLS** krypterar dynamiskt med PlayReady och Widevine DRMs MPEG-DASH strömmen. Det här alternativet krypterar även din HLS dataströmmar med FairPlay.

    c. **PlayReady endast med Smooth Streaming, HLS och MPEG-DASH** krypterar dynamiskt Smooth Streaming, HLS och MPEG-DASH-dataströmmar med PlayReady DRM.

    d. **Widevine endast med MPEG-DASH** krypterar dynamiskt MPEG-DASH med Widevine DRM.
    
    e. **FairPlay endast med HLS** krypterar dynamiskt HLS strömmen med FairPlay.

2. Aktivera FairPlay kryptering på den **Content Protection globala inställningar** bladet väljer **FairPlay configuration**. Välj sedan den **App certifikat**, och ange den **programmet hemlig nyckel**.

    ![Krypteringstyp](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. När du har gjort val av kryptering, Välj **tillämpa**.

>[!NOTE] 
>Om du planerar att spela upp ett HLS som krypterats med AES i Safari finns i bloggposten [krypterade HLS i Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

