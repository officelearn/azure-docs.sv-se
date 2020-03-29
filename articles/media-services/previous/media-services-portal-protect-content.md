---
title: Konfigurera principer för innehållsskydd med hjälp av Azure-portalen | Microsoft-dokument
description: Den här artikeln visar hur du använder Azure-portalen för att konfigurera principer för innehållsskydd. Artikeln visar också hur du aktiverar dynamisk kryptering för dina tillgångar.
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
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978210"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurera principer för innehållsskydd med hjälp av Azure-portalen

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).   > Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)
>

 Med Azure Media Services kan du skydda dina media från det att datorn lämnas via lagring, bearbetning och leverans. Du kan använda Media Services för att leverera ditt innehåll krypterat dynamiskt med Advanced Encryption Standard (AES) med hjälp av 128-bitars krypteringsnycklar. Du kan också använda den med vanlig kryptering (CENC) med hjälp av PlayReady och/eller Widevine digital rights management (DRM) och Apple FairPlay. 

Media Services tillhandahåller en tjänst för att leverera DRM-licenser och AES-clearnycklar till auktoriserade klienter. Du kan använda Azure-portalen för att skapa en princip för nyckel-/licensauktorisering för alla typer av krypteringar.

Den här artikeln visar hur du konfigurerar en innehållsskyddsprincip med hjälp av portalen. Artikeln visar också hur du använder dynamisk kryptering på dina tillgångar.

## <a name="start-to-configure-content-protection"></a>Börja konfigurera innehållsskydd
Så här använder du portalen för att konfigurera globalt innehållsskydd med hjälp av ditt Media Services-konto:

1. Välj ditt Media Services-konto i [portalen.](https://portal.azure.com/)

1. Välj**Innehållsskydd** **för inställningar** > .

    ![Innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Princip för auktorisering av nyckel/licens
Media Services stöder flera sätt att autentisera användare som gör nyckel- eller licensbegäranden. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Din klient måste då uppfylla principen innan nyckeln/licensen kan levereras till den. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning.

Du kan använda portalen för att skapa en princip för nyckel-/licensauktorisering för alla typer av krypteringar.

### <a name="open-authorization"></a>Öppna auktorisering
Öppen begränsning innebär att systemet levererar nyckeln till alla som gör en nyckelbegäran. Den här begränsningen kan vara användbar för teständamål. 

### <a name="token-authorization"></a>Tokenauktorisering
Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten simple web token (SWT) och JSON Web Token (JWT). Media Services tillhandahåller ingen STS- Du kan skapa en anpassad STS eller använda Azure Access Control Service för att utfärda token. STS måste konfigureras för att skapa en token som har signerats med den angivna nyckeln och utfärda anspråk som du angav i tokenbegränsningskonfigurationen. Om token är giltig och anspråken i token matchar de som konfigurerats för nyckeln (eller licensen), returnerar Media Services nyckelleveranstjänst den begärda nyckeln (eller licensen) till klienten.

När du konfigurerar principen tokenbegränsad måste du ange den primära verifieringsnyckeln, utfärdaren och målgruppsparametrarna. Den primära verifieringsnyckeln innehåller nyckeln som token signerades med. Utfärdaren är den säkra tokentjänsten som utfärdar token. Målgruppen (kallas ibland scope) beskriver avsikten med token eller den resurs som token auktoriserar åtkomst till. Medietjänstnyckelleveranstjänsten verifierar att dessa värden i token matchar värdena i mallen.

![Princip för auktorisering av nyckel/licens](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licensmall
Licensmallen PlayReady anger de funktioner som är aktiverade på PlayReady-licensen. Mer information om licensmallen PlayReady finns i [licensmallen För PlayReady för Media Services PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Icke-ostört
Om du konfigurerar en licens som icke-olicensist hålls den bara i minnet medan spelaren använder licensen.  

![Skydd av icke-operistent innehåll](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Permanent
Om du konfigurerar en licens som beständig sparas den i beständig lagring på klienten.

![Beständigt innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licensmall
Licensemallen Widevine anger de funktioner som är aktiverade på dina Widevine-licenser.

### <a name="basic"></a>Basic
När du väljer **Basic**skapas mallen med alla standardvärden.

### <a name="advanced"></a>Avancerat
Mer information om Widevine-rättighetsmallen finns i [översikten över Widevine-licensmallen](media-services-widevine-license-template-overview.md).

![Avancerat innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguration
Om du vill aktivera FairPlay-kryptering väljer du **FairPlay-konfiguration**. Välj sedan **appcertifikatet** och ange **den hemliga programnyckeln**. Mer information om FairPlay-konfiguration och krav finns i [Skydda ditt HLS-innehåll med Apple FairPlay eller Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguration](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Använda dynamisk kryptering på din tillgång
Om du vill dra nytta av dynamisk kryptering kodar du källfilen till en uppsättning ADAPTiv-bitrate MP4-filer.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Välj en tillgång som du vill kryptera
Om du vill visa alla dina tillgångar väljer du **Inställningar** > **.**

![Tillgångsalternativ](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Kryptera med AES eller DRM
När du väljer **Kryptera** för en tillgång visas två alternativ: **AES** eller **DRM**. 

#### <a name="aes"></a>AES
AES-rensa nyckelkryptering är aktiverad på alla direktuppspelningsprotokoll: Smooth Streaming, HLS och MPEG-DASH.

![Krypteringskonfiguration](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>Drm
1. När du har valt **DRM**visas olika principer för innehållsskydd (som måste konfigureras av den här punkten) och en uppsättning direktuppspelningsprotokoll:

    a. **PlayReady och Widevine med MPEG-DASH** krypterar dynamiskt din MPEG-DASH-ström med PlayReady- och Widevine DRMs.

    b. **PlayReady och Widevine med MPEG-DASH + FairPlay med HLS** krypterar dynamiskt din MPEG-DASH-ström med PlayReady- och Widevine DRMs. Det här alternativet krypterar också dina HLS-strömmar med FairPlay.

    c. **PlayReady endast med Smooth Streaming, HLS och MPEG-DASH** krypterar dynamiskt Smooth Streaming, HLS och MPEG-DASH-strömmar med PlayReady DRM.

    d. **Widevine endast med MPEG-DASH** krypterar dynamiskt din MPEG-DASH med Widevine DRM.
    
    e. **FairPlay endast med HLS** krypterar dynamiskt din HLS-ström med FairPlay.

1. Om du vill aktivera FairPlay-kryptering väljer du **FairPlay-konfiguration**på bladet **Globala inställningar för innehållsskydd** . Välj sedan **appcertifikatet**och ange **den hemliga programnyckeln**.

    ![Krypteringstyp](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. När du har gjort krypteringsvalet väljer du **Använd**.

>[!NOTE] 
>Om du planerar att spela en AES-krypterad HLS i Safari, se [blogginlägget Krypterad HLS i Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

