---
title: 'Azure VPN Gateway: Felsöka point-to-site-anslutningar: Mac OS X-klienter'
description: Åtgärder för felsökning av P2S Mac OS X VPN-klientanslutningar
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425737"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Felsöka VPN-anslutningar från Mac OS X VPN-klienter

Den här artikeln hjälper dig att felsöka anslutningsproblem från Mac OS X med den inbyggda VPN-klienten och IKEv2. VPN-klienten i Mac för IKEv2 är mycket grundläggande och tillåter inte mycket anpassning. Det finns bara fyra inställningar som behöver kontrolleras:

* Serveradress
* Fjärr-ID
* Lokalt ID
* Autentiseringsinställningar
* OS-version (10.11 eller senare)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Felsöka certifikatbaserad autentisering
1. Kontrollera VPN-klientinställningarna. Gå till **nätverksinställningen** genom att trycka på Kommando + Skift och skriv sedan "VPN" för att kontrollera VPN-klientinställningarna. Klicka på den VPN-post som måste undersökas i listan.

   ![IKEv2-certifikatbaserad autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Kontrollera att **serveradressen** är den fullständiga FQDN-adressen och innehåller cloudapp.net.
3. **Fjärr-ID:t** ska vara samma som serveradressen (Gateway FQDN).
4. Det **lokala ID:t** ska vara samma som **ämnet för** klientcertifikatet.
5. Klicka på **Autentiseringsinställningar för** att öppna sidan Autentiseringsinställningar.

   ![Inställningar för autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Kontrollera att **certifikatet** har valts i listrutan.
7. Klicka på knappen **Markera** och kontrollera att rätt certifikat är markerat. Spara ändringarna genom att klicka på **OK.**

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Felsöka autentisering av användarnamn och lösenord

1. Kontrollera VPN-klientinställningarna. Gå till **nätverksinställningen** genom att trycka på Kommando + Skift och skriv sedan "VPN" för att kontrollera VPN-klientinställningarna. Klicka på den VPN-post som måste undersökas i listan.

   ![Användarnamn för IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Kontrollera att **serveradressen** är den fullständiga FQDN-adressen och innehåller cloudapp.net.
3. **Fjärr-ID:t** ska vara samma som serveradressen (Gateway FQDN).
4. Det **lokala ID:t** kan vara tomt.
5. Klicka på knappen **Autentiseringsinställning** och kontrollera att "Användarnamn" är markerat i listrutan.

   ![Inställningar för autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Kontrollera att rätt autentiseringsuppgifter har angetts.

## <a name="additional-steps"></a><a name="additional"></a>Ytterligare steg

Om du provar föregående steg och allt är korrekt konfigurerat hämtar du [Wireshark](https://www.wireshark.org/#download) och utför en paketfångst.

1. Filtrera på *isakmp* och titta på **IKE_SA** paket. Du bör kunna titta på sa-förslagsuppgifterna under **Nyttolasten: Security Association**. 
2. Kontrollera att klienten och servern har en gemensam uppsättning.

   ![Paket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Om det inte finns något serversvar på nätverksspårningarna kontrollerar du att du har aktiverat IKEv2-protokollet på sidan Azure Gateway-konfiguration på Azure Portal-webbplatsen.

## <a name="next-steps"></a>Nästa steg
Mer hjälp finns i [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
