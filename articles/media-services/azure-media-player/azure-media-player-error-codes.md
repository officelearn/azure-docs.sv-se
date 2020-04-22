---
title: Felkoder för Azure Media Player
description: En felkodsreferens för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727260"
---
# <a name="error-codes"></a>Felkoder #

När uppspelningen inte kan starta eller har stoppats `error()` utlöses en felhändelse och funktionen returnerar en kod och ett valfritt meddelande som hjälper apputvecklaren att få mer information. `error().message`visas inte för användaren.  Meddelandet som visas för användaren `error().code` baseras på bitarna 27-20, se tabellen nedan.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Felkoder, bitar [31-28] (4 bitar) ##

Beskriv felets område.

- 0 – Okänt
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - Blinkar
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Felkoder, bitar [27-0] (28 bitar) ##

Beskriv detaljer om felet, bitar 27-20 ger en hög nivå, bitar 19-0 ger mer information om det finns.


| amp.errorCode. -Jag har inte tid med det här. | Koder, bitar [27-0] (28 bitar) | Beskrivning |
|---|---:|---|
| **MEDIA_ERR_ABORTED felintervall (0x0100000 - 0x01FFFFF)** | | |
| avbrutenErrUnknown | 0x0100000 | Allmänt avbrottsfel |
| avbrutenErr Implementerade | 0x0100001 | Avbryt fel, har inte implementerats |
| avbrutenErrHttpMixedContentBlocked | 0x0100002 | Avbryt fel, blockerat blandat innehåll `http://` - `https://` inträffar vanligtvis när du läser in en ström från en sida |
| **MEDIA_ERR_NETWORK fel startvärde (0x0200000 - 0x02FFFFF)** | | |
| nätverkErrOkänd | 0x0200000 | Allmänt nätverksfel |
| nätverkarehttpbadurlformat | 0x0200190 | Http 400 fel svar |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401 fel svar |
| networkErrHttpUserForbidden | 0x0200193 | Http 403 fel svar |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404 fel svar |
| networkErrHttpNotTilllämt | 0x0200195 | Http 405 fel svar |
| networkErrhttpgone | 0x020019A | Http 410 fel svar |
| networkErrHttpFörkonditionEndräpad | 0x020019C | Http 412 fel svar |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500 fel svar
| nätverkarehttpbadgateway | 0x02001F6 | Http 502 fel svar |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503 fel svar |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 fel svar |
| nätverkErrTimeout | 0x0200258 | Tidsgränsen för nätverket
| nätverkErrErr | 0x0200259 | Felsvar för nätverksanslutning |
| **MEDIA_ERR_DECODE fel (0x0300000 - 0x03FFFFF)** | | |
| avkodaErrOkänd | 0x0300000 | Allmänt avkodningsfel |
| **MEDIA_ERR_SRC_NOT_SUPPORTED fel (0x0400000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Fel på den allmänna källan stöds inte |
| srcErrParsePresentation | 0x0400001 | Fel vid presentationstolsning |
| srcErrParseSegment | 0x0400002 | Fel vid del av segmentet |
| srcErrUnsupportedPresentation | 0x0400003 | Presentationen stöds inte |
| srcErrInvalidSegment | 0x0400004 | Ogiltigt segment |
| **MEDIA_ERR_ENCRYPTED fel startvärde(0x0500000 - 0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Allmänt krypterat fel | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter hittades inte |
| encryptErrDecrypterInit | 0x0500002 | Initieringsfel för dekryptering |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter stöds inte |
| encryptErrKeyAcquire | 0x0500004 | Det gick inte att hämta nyckeln |
| encryptErrDecryption | 0x0500005 | Dekryptering av segmentet misslyckades |
| encryptErrLicenseAcquire | 0x0500006 | Det gick inte att hämta licensen |
| **SRC_PLAYER_MISMATCH fel startvärde(0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchOkänd | 0x0600000 | Generisk ingen matchande teknisk spelare att spela källan |
| srcPlayerMismatchFlashNotInstallerad | 0x0600001 |Flash plugin är inte installerat, om installerat källan kan spela. *ELLER* Flash 30 är installerat och spelar upp AES-innehåll.  Om så är fallet, försök med en annan webbläsare. Flash 30 stöds inte idag från och med 7 juni. Se [kända problem](azure-media-player-known-issues.md) för mer information. Om 0x006000003 installeras inte både Flash och Silverlight om det anges i techOrder.|
| srcPlayerMismatchSilverlightIntinstallerad | 0x0600002 | Silverlight plugin är inte installerat, om installerat källan kan spela. Om 0x006000003 installeras inte både Flash och Silverlight om det anges i techOrder. |
| | 0x00600003 | Både Flash och Silverlight är inte installerade, om det anges i techOrder. |
| **Okända fel (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Okända fel |

## <a name="user-error-messages"></a>Felmeddelanden för användare ##

Användarmeddelande som visas baseras på felkodens bitar 27-20.

- MEDIA_ERR_ABORTED (1) -"Du avbröt videouppspelningen"
- MEDIA_ERR_NETWORK (2) - "Ett nätverksfel orsakade att videohämtningen misslyckades halvvägs."
- MEDIA_ERR_DECODE (3) - "Videouppspelningen avbröts på grund av ett korruptionsproblem eller på grund av att videon som används funktioner som din webbläsare inte stödde."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Videon kunde inte läsas in, antingen för att servern eller nätverket misslyckades eller för att formatet inte stöds."
- MEDIA_ERR_ENCRYPTED (5) - "Videon är krypterad och vi har inte nycklarna för att dekryptera den."
- SRC_PLAYER_MISMATCH (6) - "Ingen kompatibel källa hittades för den här videon."
- MEDIA_ERR_UNKNOWN (0xFF) - "Ett okänt fel uppstod."

## <a name="examples"></a>Exempel ##

### <a name="0x10600001"></a>0x10600001 ##

"Ingen kompatibel källa hittades för den här videon." visas för slutanvändaren.

Det finns ingen teknisk spelare som kan spela de begärda källorna, men om Flash plugin är installerat, är det troligt att en källa kan spelas.  

### <a name="0x20200194"></a>0x20200194 ###

"Ett nätverksfel gjorde att videohämtningen misslyckades halvvägs." visas för slutanvändaren.

AzureHtml5JS kunde inte uppspelning från ett http 404-svar.

### <a name="categorizing-errors"></a>Kategorisera fel ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Fånga ett specifikt fel ###

Följande kod fångar bara 404 fel:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)