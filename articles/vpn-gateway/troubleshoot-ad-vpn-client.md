---
title: 'VPN Gateway: Felsöka VPN-klient - Azure AD-autentisering'
description: Felsöka VPN Gateway P2S Azure AD-autentiseringsklienter
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151965"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Felsöka en VPN-klient för Azure AD-autentisering

Den här artikeln hjälper dig att felsöka en VPN-klient för att ansluta till ett virtuellt nätverk med Point-to-Site VPN och Azure Active Directory-autentisering.

## <a name="view-status-log"></a><a name="status"></a>Visa statuslogg

Visa statusloggen för felmeddelanden.

![loggar](./media/troubleshoot-ad-vpn-client/1.png)

1. Klicka på pilikonen längst ned till höger i klientfönstret för att visa **statusloggarna**.
2. Kontrollera om det finns fel i loggarna som kan tyda på problemet.
3. Felmeddelanden visas i rött.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Rensa inloggningsinformation

Rensa inloggningsinformationen.

![logga in](./media/troubleshoot-ad-vpn-client/2.png)

1. Välj ... bredvid den profil som du vill felsöka. Välj **Konfigurera -> rensa sparat konto**.
2. Välj **Spara**.
3. Prova att ansluta.
4. Om anslutningen fortfarande misslyckas fortsätter du till nästa avsnitt.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Kör diagnostik

Kör diagnostik på VPN-klienten.

![diagnostik](./media/troubleshoot-ad-vpn-client/3.png)

1. Klicka på **...** bredvid den profil som du vill köra diagnostik på. Välj **Diagnostisera -> kör diagnos**.
2. Klienten kommer att köra en serie tester och visa resultatet av testet

   * Internet-åtkomst – Kontrollerar om klienten har Internet-anslutning
   * Klientautentiseringsuppgifter – Kontrollera om slutpunkten för Azure Active Directory-autentisering kan nås
   * Servermatchningsbar – Kontaktar DNS-servern för att matcha IP-adressen för den konfigurerade VPN-servern
   * Server som kan nås – Kontrollerar om VPN-servern svarar eller inte
3. Om något av testerna misslyckas kontaktar du nätverksadministratören för att lösa problemet.
4. I nästa avsnitt visas hur du samlar in loggarna om det behövs.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Samla in klientloggfiler

Samla in VPN-klientloggfilerna. Loggfilerna kan skickas till support/administratör via en metod som du väljer. Till exempel e-post.

1. Klicka på "..." bredvid den profil som du vill köra diagnostik på. Välj **Diagnostisera -> Visa loggkatalog .**

   ![visa loggar](./media/troubleshoot-ad-vpn-client/4.png)
2. Utforskaren öppnas för mappen som innehåller loggfilerna.

   ![visa fil](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Skapa en Azure Active Directory-klient för P2S Open VPN-anslutningar som använder Azure AD-autentisering](openvpn-azure-ad-tenant.md).