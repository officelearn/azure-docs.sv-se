---
title: Konfigurera innehållsskydd principer med hjälp av Azure portal | Microsoft Docs
description: Den här artikeln visar hur du använder Azure-portalen för att konfigurera principer för innehållsskydd. Artikeln beskriver också hur du aktiverar dynamisk kryptering för dina tillgångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c0571e202448951de0994d34f68e1649eabd5519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61128187"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurera innehållsskydd principer med hjälp av Azure portal
 Med Azure Media Services kan skydda du dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Du kan använda Media Services för att leverera ditt innehåll krypteras dynamiskt med den Standard AES (Advanced Encryption) med hjälp av 128-bitars krypteringsnycklar. Du kan också använda den med gemensam kryptering (CENC) med PlayReady och/eller Widevine digital rights management (DRM) och Apple FairPlay. 

Media Services tillhandahåller en tjänst för att leverera DRM-licenser och klartextnyckel nycklar till auktoriserade klienter. Du kan använda Azure-portalen för att skapa en auktoriseringsprincip för nyckel/licens för alla typer av krypteringar.

Den här artikeln visar hur du konfigurerar en princip för innehållsskydd med hjälp av portalen. Artikeln beskriver också hur du använder dynamisk kryptering för dina tillgångar.

## <a name="start-to-configure-content-protection"></a>Börja konfigurera innehållsskydd
Om du vill använda portalen för att konfigurera för globalt innehållsskydd med hjälp av Media Services-kontot, gör du följande:

1. I den [portal](https://portal.azure.com/), Välj Media Services-kontot.

1. Välj **inställningar** > **Content protection**.

    ![Innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Auktoriseringsprincip för nyckel/licens
Media Services stöder flera olika sätt att auktorisera användare som begär eller licensinformation. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten måste sedan uppfylla principen innan nyckel/licens kan levereras till den. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning.

Du kan använda portalen för att skapa en auktoriseringsprincip för nyckel/licens för alla typer av krypteringar.

### <a name="open-authorization"></a>Öppna auktorisering
Öppna begränsningen innebär att systemet ger nyckeln för alla som begär nycklar. Den här begränsningen kan vara användbart för testning. 

### <a name="token-authorization"></a>Token auktorisering
Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i simple webbtoken (SWT) och format som JSON Web Token (JWT). Media Services tillhandahåller inte en STS. Du kan skapa en anpassad STS eller Använd Azure Access Control Service att problemet token. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Om token är giltig och anspråken i token matchar de som konfigurerats för nyckeln (eller licens), returnerar Media Services-nyckelleveranstjänst begärda nyckeln (eller licens) till klienten.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Målgruppen (kallas ibland för omfång) beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

![Auktoriseringsprincip för nyckel/licens](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licensmall
PlayReady-licensmall anger de funktioner som är aktiverad för din PlayReady-licens. Mer information om licensmallen för PlayReady finns i den [översikt över Media Services PlayReady-licensmallen](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Ickebeständig
Om du konfigurerar en licens som ickebeständig lagras det i minnet enbart vid spelaren används licensen.  

![Ickebeständig innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Beständig
Om du konfigurerar en licens som beständig, sparas den i permanent lagring på klienten.

![Beständigt skydd för innehåll](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licensmall
Widevine-licensmall anger de funktioner som är aktiverad för Widevine-licenser.

### <a name="basic"></a>Basic
När du väljer **grundläggande**, mallen har skapats med alla standardvärden.

### <a name="advanced"></a>Avancerat
Mer information om rättighetsmall för Widevine finns i den [översikt över Widevine-licensmallen](media-services-widevine-license-template-overview.md).

![Avancerade innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguration
Om du vill aktivera FairPlay-kryptering, Välj **FairPlay-konfiguration**. Välj sedan den **appcertifikat** och ange den **hemliga nyckeln**. Mer information om FairPlay-konfiguration och krav finns i [skydda ditt innehåll med Apple FairPlay eller Microsoft PlayReady HLS](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguration](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Använda dynamisk kryptering för tillgången
Om du vill dra nytta av dynamisk kryptering, koda din källfil till en uppsättning MP4-filer med anpassningsbar bithastighet.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Välj en tillgång som du vill kryptera
Om du vill se alla dina tillgångar, **inställningar** > **tillgångar**.

![Alternativ för tillgångar](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Kryptera med AES eller DRM
När du väljer **Encrypt** för en tillgång, visas två alternativ: **AES** eller **DRM**. 

#### <a name="aes"></a>AES
AES-klartextnyckelkryptering viktiga kryptering är aktiverat för alla Direktuppspelningsprotokoll: Smooth Streaming, HLS och MPEG-DASH.

![Krypteringskonfiguration](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. När du har valt **DRM**, visas olika content protection-principer (som måste konfigureras med den här punkten) och en uppsättning protokoll för direktuppspelning:

    a. **PlayReady och Widevine med MPEG-DASH** dynamiskt krypterar dina MPEG-DASH-strömmar med PlayReady och Widevine DRM: er.

    b. **PlayReady och Widevine med MPEG-DASH + FairPlay med HLS** dynamiskt kryptera din MPEG-DASH-strömmar med PlayReady och Widevine DRM: er. Det här alternativet krypterar även dina HLS-strömmar med FairPlay.

    c. **Enbart PlayReady med Smooth Streaming, HLS och MPEG-DASH** dynamiskt krypterar Smooth Streaming, HLS och MPEG-DASH-strömmar med PlayReady DRM.

    d. **Widevine endast med MPEG-DASH** dynamiskt krypterar dina MPEG-DASH med Widevine DRM.
    
    e. **FairPlay endast med HLS** dynamiskt krypterar HLS dataströmmen med FairPlay.

1. Att aktivera FairPlay-kryptering på den **Content Protection globala inställningar** bladet väljer **FairPlay-konfiguration**. Välj sedan den **appcertifikat**, och ange den **hemliga nyckeln**.

    ![Krypteringstyp](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. När du har gjort krypteringsvalet, väljer **tillämpa**.

>[!NOTE] 
>Om du planerar att spela upp en AES-krypterad HLS i Safari, finns i bloggposten [krypterad HLS i Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

