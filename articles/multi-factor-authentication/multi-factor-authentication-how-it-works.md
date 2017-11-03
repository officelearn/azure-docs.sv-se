---
title: Azure Multi-Factor Authentication - hur det fungerar
description: "Azures Multi-Factor Authentication bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Det ger ökad säkerhet genom att kräva andra formen av autentisering och ger stark autentisering via en mängd alternativ för enkel verifiering."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 6fee02885cc76b3a4fdad11e8702f623d6fe6597
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-multi-factor-authentication-works"></a>Hur Azure Multi-Factor Authentication fungerar
Säkerheten för tvåstegsverifiering ligger i dess överlappande tillvägagångssättet. Att kompromettera flera autentiseringsfaktorer presenterar en betydande utmaning för angripare. Även om någon lyckas att ta reda på användarens lösenord, är så oanvändbar utan också tillgång till betrodda enheten. 

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azures Multi-Factor Authentication bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess.  Det ger ökad säkerhet genom att kräva andra formen av autentisering och ger stark autentisering via en mängd alternativ för enkel verifiering.


## <a name="methods-available-for-two-step-verification"></a>Metoderna för tvåstegsverifiering
När en användare loggar in, skickas en ytterligare verifiering för användaren.  Här följer en lista över metoder som kan användas för andra verifieringen.

| Verifieringsmetod | Beskrivning |
| --- | --- |
| Telefonsamtal |Ett anrop placeras till en användarens registrerade telefon. Användaren anger en PIN-kod om nödvändigt trycker på #-knappen. |
| Textmeddelande |Ett textmeddelande skickas till en användarens mobiltelefon med en sexsiffrig kod. Användaren anger den här koden på sidan logga in. |
| Meddelanden via mobilapp |En begäran om identitetsverifiering skickas till en användarens Smartphone. Användaren anger en PIN-kod om det behövs och väljer **Kontrollera** på mobilappen. |
| Verifieringskod från mobilapp |Mobila appen, som körs på en användares Smartphone, visar en Verifieringskod som ändrar var 30: e sekund. Användaren söker efter senaste koden och registrerar den på sidan logga in. |
| OATH-token från tredje part | Azure Multi-Factor Authentication-servern kan konfigureras för att godkänna verifieringsmetoderna från tredje part. |

Azure Multi-Factor Authentication ger valbar verifieringsmetoderna för moln- och server. Du kan välja vilka metoder som är tillgängliga för användarna: telefonsamtal, text, avisering via app eller app-koder. Mer information finns i [valbar verifieringsmetoderna](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Nästa steg

- Läs mer om de olika [versioner och av förbrukningsmetoder för Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

- Välj om du vill distribuera Azure MFA [i molnet eller lokalt](multi-factor-authentication-get-started.md)

- Läs svar på [vanliga frågor och svar](multi-factor-authentication-faq.md)