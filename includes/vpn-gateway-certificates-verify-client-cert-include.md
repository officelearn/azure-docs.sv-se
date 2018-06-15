---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197144"
---
Om du har problem med att ansluta kan du kontrollera följande:

- Om du har exporterat ett klientcertifikat kontrollerar du att du har exporterat det som en PFX-fil med standardvärdet ”Ta med om möjligt alla certifikat i certifieringssökvägen”. När du exporterar det med det här värdet exporteras även rotcertifikatinformationen. När certifikatet installeras på klientdatorn installeras även rotcertifikatet som finns i PFX-filen på klientdatorn. Rotcertifikatinformationen måste vara installerad på klientdatorn. Du kan kontrollera detta genom att gå till **Hantera användarcertifikat** och navigera till **Betrodda rotcertifikatutfärdare\Certifikat**. Kontrollera att rotcertifikatet visas i listan. Rotcertifikatet måste finnas för att autentiseringen ska fungera.

- Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan.

- Mer information om P2S-felsökning finns i [Felsöka P2S-anslutningar](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).