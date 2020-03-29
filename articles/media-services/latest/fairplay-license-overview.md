---
title: Licenssupport för Media Services och Apple FairPlay – Azure | Microsoft-dokument
description: Det här avsnittet innehåller en översikt över en Apple FairPlay-licenskrav och konfiguration.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733580"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Licenskrav för och konfiguration av Apple FairPlay 

Med Azure Media Services kan du kryptera ditt HLS-innehåll med **Apple FairPlay** (AES-128 CBC). Media Services tillhandahåller också en tjänst för att leverera FairPlay-licenser. När en spelare försöker spela upp ditt FairPlay-skyddade innehåll skickas en begäran till licensleveranstjänsten för att få en licens. Om licenstjänsten godkänner begäran utfärdar den licensen som skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

Media Services tillhandahåller också API:er som du kan använda för att konfigurera dina FairPlay-licenser. I det här avsnittet beskrivs FairPlay-licenskrav och hur du kan konfigurera en **FairPlay-licens** med Hjälp av Api:er för Media Services. 

## <a name="requirements"></a>Krav

Följande krävs när du använder Media Services för att kryptera ditt HLS-innehåll med **Apple FairPlay** och använda Media Services för att leverera FairPlay-licenser:

* Registrera dig med [Apples utvecklingsprogram](https://developer.apple.com/).
* Apple kräver att innehållsägaren hämtar [distributionspaketet](https://developer.apple.com/contact/fps/). Tillstånd som du redan har implementerat Key Security Module (KSM) med Media Services och att du begär det slutliga FPS-paketet. Det finns instruktioner i det slutliga FPS-paketet för att generera certifiering och hämta Application Secret Key (ASK). Du använder ASK för att konfigurera FairPlay.
* Följande saker måste anges på Media Services nyckel/licensleveranssida:

    * **App Cert (AC)**: Det här är en PFX-fil som innehåller den privata nyckeln. Du skapar den här filen och krypterar den med ett lösenord. PFX-filen ska vara i Base64-format.

        I följande steg beskrivs hur du skapar en PFX-certifikatfil för FairPlay:

        1. Installera OpenSSL https://slproweb.com/products/Win32OpenSSL.htmlfrån .

            Gå till mappen där FairPlay-certifikatet och andra filer som levereras av Apple finns.
        2. Kör följande kommando från kommandoraden. Detta konverterar .cer-filen till en PEM-fil.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Kör följande kommando från kommandoraden. Detta konverterar PEM-filen till en PFX-fil med den privata nyckeln. Lösenordet för PFX-filen ställs sedan in av OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **App Cert lösenord:** Lösenordet för att skapa .pfx-filen.
    * **ASK:** Den här nyckeln tas emot när du genererar certifieringen med hjälp av Apples utvecklarportal. Varje utvecklingsteam får en unik ASK. Spara en kopia av ASK och förvara den på ett säkert ställe. Du måste konfigurera ASK som FairPlayAsk med Media Services.
    
* Följande saker måste ställas in av FPS-klientsidan:

  * **App Cert (AC)**: Detta är en .cer/.der-fil som innehåller den offentliga nyckeln, som operativsystemet använder för att kryptera vissa nyttolaster. Media Services behöver veta om det eftersom det krävs av spelaren. Nyckelleveranstjänsten dekrypterar den med motsvarande privata nyckel.

* Om du vill spela upp en FairPlay-krypterad ström får du en riktig ASK först och genererar sedan ett riktigt certifikat. Denna process skapar alla tre delarna:

  * .der-fil
  * .pfx-fil
  * lösenord för PFX

## <a name="fairplay-and-player-apps"></a>FairPlay- och spelarappar

När ditt innehåll krypteras med **Apple FairPlay**krypteras de enskilda video- och ljudexemplen med hjälp av **CBC-läget AES-128.** **FairPlay Streaming** (FPS) är integrerat i enhetens operativsystem, med inbyggt stöd på iOS och Apple TV. Safari på OS X aktiverar FPS med hjälp av EME-gränssnittsstödet (Encrypted Media Extensions).

Azure Media Player stöder också FairPlay-uppspelning. Mer information finns i [Azure Media Player-dokumentationen](https://amp.azure.net/libs/amp/latest/docs/index.html).

Du kan utveckla dina egna spelarappar med hjälp av iOS SDK. För att kunna spela FairPlay-innehåll måste du implementera licensutbytesprotokollet. Det här protokollet har inte angetts av Apple. Det är upp till varje app hur du skickar viktiga leveransförfrågningar. Media Services FairPlay nyckelleveranstjänsten förväntar sig att SPC kommer som ett www-form-url-kodat inläggsmeddelande i följande form:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay-konfiguration .NET-exempel

Du kan använda Api för Media Services för att konfigurera FairPlay-licenser. När spelaren försöker spela upp ditt FairPlay-skyddade innehåll skickas en begäran till licensleveranstjänsten för att erhålla licensen. Om licenstjänsten godkänner begäran utfärdar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

> [!NOTE]
> Vanligtvis vill du bara konfigurera FairPlay-principalternativ en gång, eftersom du bara har en uppsättning en certifiering och en ASK.

I följande exempel används [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) för att konfigurera licensen.

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

Kolla in hur du [skyddar med DRM](protect-with-drm.md)
