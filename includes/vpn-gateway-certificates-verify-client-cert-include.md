---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320208"
---
Om du har problem med att ansluta kan du kontrollera följande:

- Om du har exporterat ett klientcertifikat med **Certificate Export Wizard** (guiden för certifikatexport) kontrollerar du att du har exporterat det som en .pfx-fil och valt **Include all certificates in the certification path if possible** (Inkludera alla certifikat i certifieringssökvägen om det är möjligt). När du exporterar det med det här värdet exporteras även rotcertifikatinformationen. När du har installerat certifikatet på klientdatorn installeras även rotcertifikatet i .pfx-filen. Kontrollera att rotcertifikatet har installerats genom att öppna **Hantera användarcertifikat** och välja **Betrodda rotcertifikatutfärdare\certifikat**. Kontrollera att rotcertifikatet finns med. Detta krävs för att autentiseringen ska fungera.

- Om du använde ett certifikat som utfärdades av en certifikatutfärdarlösning för företag och du inte kan autentisera kontrollerar du autentiseringsordningen på klientcertifikatet. Kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet, välja fliken **Information** och sedan välja **Förbättrad nyckelanvändning**. Kontrollera att *Klientautentisering* är den första posten i listan. Annars utfärdar du ett klientcertifikat baserat på den användarmall där *Klientautentisering* är den första posten i listan.

- Mer information om P2S-felsökning finns i [Felsöka P2S-anslutningar](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
