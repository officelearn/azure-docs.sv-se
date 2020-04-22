---
title: Skyddat innehåll i Azure Media Player
description: Azure Media Player stöder för närvarande AES-128-bitars kuvert krypterat innehåll och vanligt krypterat innehåll.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726496"
---
# <a name="protected-content"></a>Skyddat innehåll #

Azure Media Player stöder för närvarande AES-128-bitars kuvert krypterat innehåll och vanligt krypterat innehåll (via PlayReady och Widevine) eller krypterat innehåll via FairPlay. För att kunna spela upp skyddat innehåll på rätt `protectionInfo`sätt måste du tala om för Azure Media Player . Den här informationen finns per källa `<source>` och kan `data-setup`läggas till direkt på taggen via .  Du kan också `protectionInfo` lägga till direkt som en parameter om du ställer in källan dynamiskt.

`protectionInfo`accepterar ett JSON-objekt och innehåller:

- `type`: `AES` `PlayReady` eller `Widevine` eller eller`FairPlay`
- `certificateUrl`: Detta bör vara en direkt länk till din värd FairPlay cert

- `authenticationToken`: Det här är ett alternativfält för att lägga till en okodad autentiseringstoken

> [!IMPORTANT]
> **CertifikatObjektet** behövs bara för FairPlay DRM.***
>[!NOTE]
> StandardteknikenOrder har ändrats för att `html5FairPlayHLS` rymma den nya tekniken, särskilt för att spela upp FairPlay-innehåll internt i webbläsare som stöder det (Safari på OSX 8+). Om du har FairPlay-innehåll att spela upp **och** du har ändrat standardteknikordern till en anpassad i programmet måste du lägga till den här nya tekniken i ditt techOrder-objekt. Vi rekommenderar att du inkluderar det före silverlightSS så att ditt innehåll inte spelas upp via PlayReady.

## <a name="code-sample"></a>Kodexempel ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

eller, med flera DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Alla webbläsare/plattformar kan inte spela upp skyddat innehåll. Mer information om vad som stöds finns i avsnittet [Uppspelningsteknik.](azure-media-player-playback-technology.md)
> [!IMPORTANT]
> Token som skickas till spelaren är avsedd för skyddat innehåll och används endast för autentiserade användare. Det antas att programmet använder SSL eller någon annan form av säkerhetsåtgärd. Dessutom är slutanvändaren assummed att lita på att inte missbruka token; Om så inte är fallet, vänligen involvera dina säkerhetsexperter.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)