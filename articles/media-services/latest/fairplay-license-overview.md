---
title: Azure Media Services och Apple FairPlay licensiera support | Microsoft Docs
description: Det här avsnittet ger en översikt över en Apple FairPlay-licens krav och konfiguration.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2018
ms.author: juliako
ms.openlocfilehash: ef2865152d94612a38f238de9cbd7d2cb08f5cbb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009100"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay licenskrav och konfiguration 

Azure Media Services kan du kryptera ditt HLS-innehåll med **Apple FairPlay** (AES-128 CBC). Media Services tillhandahåller också en tjänst för leverans av FairPlay-licenser. När en spelare försöker att spela upp ditt innehåll med FairPlay-skyddad, skickas en begäran till licensleveranstjänst du erhåller en licens. Om Licenstjänsten godkänner begäran, utfärdar den licens som skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

Media Services tillhandahåller också API: er som du kan använda för att konfigurera FairPlay-licenser. Det här avsnittet innehåller information om FairPlay-licens och visar hur du kan konfigurera en **FairPlay** licensiera med hjälp av Media Sercies APIs. 

## <a name="requirements"></a>Krav

Följande krävs när du använder Media Services för att kryptera ditt HLS-innehåll med **Apple FairPlay** och använder Media Services för att leverera FairPlay-licenser:

* Registrera dig med [Apples Program för utveckling](https://developer.apple.com/).
* Apple kräver innehållets ägare att hämta den [distributionspaketet](https://developer.apple.com/contact/fps/). Tillstånd att du redan implementerat nyckel Security modulen (KSM) med Media Services och att du begär slutpaketet FPS. Det finns instruktioner i slutpaketet FPS att generera certifiering och få programmet hemlig nyckel (ASK). Du kan använda fråga för att konfigurera FairPlay.
* Följande måste anges för Media Services nyckel/licens leverans sida:

    * **App (Appcertifikat – AC)**: det här är en .pfx-fil som innehåller den privata nyckeln. Du skapar den här filen och krypterar dem med ett lösenord. .Pfx-filen shoul vara i Base64-format.

        Följande steg beskriver hur du skapar en PFX-fil för FairPlay:

        1. Installera OpenSSL, från https://slproweb.com/products/Win32OpenSSL.html.

            Gå till mappen där FairPlay-certifikat och andra filer som levereras av Apple är.
        2. Kör följande kommando från kommandoraden. Detta omvandlar .cer-filen till en .pem-fil.

            ”C:\OpenSSL-Win32\bin\openssl.exe” x509-informera der-i FairPlay.cer-ut FairPlay-out.pem
        3. Kör följande kommando från kommandoraden. Detta omvandlar PEM-filen till en .pfx-fil med den privata nyckeln. OpenSSL uppmanas sedan lösenordet för PFX-filen.

            ”C:\OpenSSL-Win32\bin\openssl.exe” pkcs12-export - ut FairPlay-out.pfx-inkey privatekey.pem-i FairPlay-out.pem - passin file:privatekey-pem-pass.txt
            
    * **Lösenord för App-Cert**: lösenordet för att skapa .pfx-filen.
    * **Be**: den här nyckeln tas emot när du skapar certifieringen via Apple Developer-portalen. Varje Utvecklingsteamet tar emot en unik be. Spara en kopia av fråga och lagra den på en säker plats. Du måste konfigurera be som FairPlayAsk med Media Services.
    
* Följande måste anges av klientsidan FPS:

  * **App (Appcertifikat – AC)**: det här är en.cer/.der-fil som innehåller den offentliga nyckeln som operativsystemet använder för att kryptera vissa nyttolast. Media Services behöver veta om den eftersom det krävs av spelaren. Nyckelleveranstjänst dekrypterar den med hjälp av motsvarande privata nyckel.

* Få en verklig be första om du vill spela upp en krypterad FairPlay-dataström, och sedan generera ett verkligt certifikat. Den här processen skapar alla tre delar:

  * .der fil
  * .pfx-fil
  * lösenordet för PFX

## <a name="fairplay-and-player-apps"></a>FairPlay och spelare appar

När innehållet krypteras med **Apple FairPlay**, enskilda video och ljud exemplen krypteras med hjälp av den **AES-128 CBC** läge. **FairPlay Streaming** (FPS) är integrerad i enhetens operativsystem, med inbyggt stöd på iOS- och Apple TV. Safari på OS X kan FPS med stöd för tillägg EME (Encrypted Media) användargränssnitt.

Azure Media Player stöder också FairPlay uppspelning. Mer information finns i [dokumentation för Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Du kan utveckla dina egna player-appar med hjälp av iOS SDK. Om du vill kunna spela upp innehåll med FairPlay som du behöver implementera licens för exchange-protokollet. Det här protokollet har inte angetts av Apple. Det är upp till varje app hur du skickar begäranden om leverans av nyckel. Media Services FairPlay-nyckelleveranstjänst förväntar sig SPC kommer som ett www-form-url kodad post-meddelande, i följande format:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay Konfigurationsexempel för .NET

Du kan använda Media Services-API för att konfigurera FairPlay-licenser. När spelaren försöker att spela upp ditt innehåll med FairPlay-skyddad, skickas en begäran till licensleveranstjänst att få licensen. Om Licenstjänsten godkänner begäran, skickar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

> [!NOTE]
> Vanligtvis vill du konfigurera alternativ för FairPlay endast en gång, eftersom du bara har en uppsättning av en certifikatutfärdare och en fråga.

I följande exempel används [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) konfigurera licensen.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Nästa steg

[Översikt](content-protection-overview.md)