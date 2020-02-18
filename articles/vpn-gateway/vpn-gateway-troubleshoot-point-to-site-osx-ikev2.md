---
title: 'Azure-VPN Gateway: Felsöka punkt-till-plats-anslutningar: Mac OS X-klienter'
description: Steg för att felsöka P2S Mac OS X VPN-klientanslutningar
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425737"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Felsöka punkt-till-plats-VPN-anslutningar från VPN för Mac OS X-klienter

Den här artikeln hjälper dig att felsöka problem med punkt-till-plats-anslutningen från Mac OS X med hjälp av inbyggda VPN-klienten och IKEv2. VPN-klienten i Mac för IKEv2 är mycket enkel och inte tillåter att för mycket anpassning. Det finns bara fyra inställningar som behöver du:

* Serveradress
* Fjärr-ID
* Lokala ID: T
* Autentiseringsinställningar
* OS-Version (10.11 eller senare)


## <a name="VPNClient"></a>Felsöka certifikatbaserad autentisering
1. Kontrollera inställningarna för VPN-klienten. Gå till **nätverks inställningen** genom att trycka på Kommando + Skift och skriv sedan "VPN" för att kontrol lera inställningarna för VPN-klienten. I listan klickar du på VPN-posten som behöver undersökas.

   ![IKEv2 certifikatbaserad autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Kontrol lera att **Server adressen** är fullständigt FQDN och att den innehåller cloudapp.net.
3. **Fjärr-ID** ska vara detsamma som Server adressen (Gateway FQDN).
4. Det **lokala ID: t** ska vara samma som för klient certifikatets **ämne** .
5. Klicka på **autentiseringsinställningar** för att öppna sidan autentiseringsinställningar.

   ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Kontrol lera att **certifikatet** är markerat i list rutan.
7. Klicka på knappen **Välj** och kontrol lera att rätt certifikat har valts. Spara ändringarna genom att klicka på **OK** .

## <a name="ikev2"></a>Felsöka användar namn och lösenordsautentisering

1. Kontrollera inställningarna för VPN-klienten. Gå till **nätverks inställningen** genom att trycka på Kommando + Skift och skriv sedan "VPN" för att kontrol lera inställningarna för VPN-klienten. I listan klickar du på VPN-posten som behöver undersökas.

   ![IKEv2-användarnamnet](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Kontrol lera att **Server adressen** är fullständigt FQDN och att den innehåller cloudapp.net.
3. **Fjärr-ID** ska vara detsamma som Server adressen (Gateway FQDN).
4. **Lokalt ID** kan vara tomt.
5. Klicka på knappen **autentiseringsinställningar** och kontrol lera att "username" är markerat i list rutan.

   ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Kontrollera att rätt autentiseringsuppgifter har angetts.

## <a name="additional"></a>Ytterligare steg

Om du försöker med föregående steg och allt är korrekt konfigurerat, laddar du ned [wireshark](https://www.wireshark.org/#download) och utför en paket fångst.

1. Filtrera på *ISAKMP* och titta på **IKE_SA** paket. Du bör kunna se information om SA-förslagen under **nytto lasten: säkerhets Association**. 
2. Kontrollera att klienten och servern har en gemensam uppsättning.

   ![paket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Om det inte finns något Server svar på nätverks spåren kontrollerar du att IKEv2-protokollet har Aktiver ATS på konfigurations sidan för Azure gateway på Azure Portal webbplats.

## <a name="next-steps"></a>Nästa steg
Mer hjälp finns i [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
