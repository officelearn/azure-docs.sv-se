---
title: Översikt över Azure Communication Services som anropar klient bibliotek
titleSuffix: An Azure Communication Services concept document
description: Ger en översikt över det anropande klient biblioteket.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d8216bb8c30448e7e91ec9a1bcc531b720eca30c
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665199"
---
# <a name="calling-client-library-overview"></a>Översikt över klientbiblioteket för samtal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Det finns två olika familjer av att anropa klient bibliotek för *klienter* och *tjänster.* För närvarande är tillgängliga klient bibliotek avsedda för slut användar upplevelser: webbplatser och inbyggda appar.

Tjänst klient biblioteken är inte tillgängliga än och ger till gång till planerna för rå röst och video data som passar för integrering med robotar och andra tjänster.

## <a name="calling-client-library-capabilities"></a>Anropa klient biblioteks funktioner

I följande lista presenteras de funktioner som för närvarande är tillgängliga i Azure Communication Services som anropar klient bibliotek.

| Grupp med funktioner | Kapacitet                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Kärn funktioner | Placera ett ett-till-ett-samtal mellan två användare                                                                           | ✔️   | ✔️            | ✔️  
|                   | Placera ett grupp samtal med fler än två användare (upp till 350 användare)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Marknadsför ett ett-till-ett-samtal med två användare i ett grupp samtal med fler än två användare                                 | ✔️   | ✔️            | ✔️ 
|                   | Delta i ett grupp anrop efter att det har startat                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Bjud in en annan VoIP-deltagare för att ansluta till ett pågående grupp anrop                                                       | ✔️   | ✔️            | ✔️ 
|                   | Tyst/avljud av MIC                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Växla mellan kameror                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Lokalt undantag/ej Behåll                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Aktiv talare                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Välj högtalare för samtal                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Välj mikrofon för samtal                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Visa status för en deltagare<br/>*Inaktiv, tidig media, anslutning, ansluten, stoppad, i lobbyn, frånkopplat*         | ✔️   | ✔️            | ✔️           
|                   | Visa status för ett samtal<br/>*Tidig media, inkommande, anslutning, ringa, ansluten, stoppa, koppla från, frånkopplad* | ✔️   | ✔️            | ✔️           
|                   | Visa om en deltagare är avstängd                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Visa orsaken till att en deltagare lämnat ett samtal                                                                       | ✔️   | ✔️            | ✔️     
| Skärm delning    | Dela hela skärmen inifrån programmet                                                                 | ✔️   | ❌            | ❌           
|                   | Dela ett enskilt program (från listan över program som körs)                                                | ✔️   | ❌            | ❌           
|                   | Dela en webbläsare-flik från listan med öppna flikar                                                                  | ✔️   | ❌            | ❌           
|                   | Deltagare kan visa fjärrskärms resurs                                                                            | ✔️   | ✔️            | ✔️         
| Tjänstgöring            | Lista deltagare                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Ta bort en deltagare                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Placera ett ett-till-ett-samtal med en PSTN-deltagare                                                                     | ✔️   | ✔️            | ✔️   
|                   | Placera ett grupp samtal med PSTN-deltagare                                                                           | ✔️   | ✔️            | ✔️
|                   | Marknadsför ett ett-till-ett-samtal med en PSTN-deltagare i ett grupp anrop                                                 | ✔️   | ✔️            | ✔️
|                   | Ringa ut från ett grupp anrop som en PSTN-deltagare                                                                    | ✔️   | ✔️            | ✔️   
| Allmänt           | Testa din mikrofon, talare och kamera med en ljud test tjänst (tillgänglig genom att anropa 8: echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Anropar stöd för klient biblioteks webbläsare

Följande tabell visar en uppsättning webbläsare och versioner som stöds och som är tillgängliga för tillfället.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Anropar klient bibliotek** | Chrome *, ny kant | Chrome *, Safari** | Chrome  | Chrome | Chrome | Safari * * |


* Observera att den senaste versionen av Chrome stöds förutom de tidigare två versionerna.<br/>

* * Observera att Safari-versionerna 13.1 + stöds. Utgående video för Safari macOS stöds inte ännu, men stöds i iOS. Delning av utgående skärm stöds endast på Desktop iOS.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Mer information finns i följande artiklar:
- Bekanta dig med allmänna [samtals flöden](../call-flows.md)
- Läs mer om [samtals typer](../voice-video-calling/about-call-types.md)
- [Planera din PSTN-lösning](../telephony-sms/plan-solution.md)
