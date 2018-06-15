---
title: Felsöka Azure punkt-till-plats VPN-anslutningar från klienter för Mac OS X | Microsoft Docs
description: Steg för att felsöka P2S Mac OS X VPN-klientanslutningar
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 1cf8195cbf65f27c71a4db18c0c61c8a25673acd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30248165"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Felsöka punkt-till-plats VPN-anslutningar från Mac OS X VPN-klienter

Den här artikeln hjälper dig att felsöka problem med nätverksanslutningen punkt-till-plats från Mac OS X med den inbyggda VPN-klienten och IKEv2. VPN-klienten i Mac för IKEv2 är grundläggande och tillåter inte att för mycket anpassning. Det finns fyra inställningar som ska kontrolleras:

* Serveradress
* Fjärr-ID
* Lokalt ID
* Autentiseringsinställningar
* OS-Version (10.11 eller senare)


## <a name="VPNClient"></a> Felsöka certifikatbaserad autentisering
1. Kontrollera inställningarna för VPN-klienten. Gå till den **nätverksinställningen** genom att trycka på Kommando + Skift och skriv sedan ”VPN” för att kontrollera inställningarna för VPN-klienten. I listan klickar du på VPN-posten som ska undersökas.

  ![IKEv2 certifikatbaserad autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Kontrollera att den **serveradress** är fullständigt FQDN och inkluderar cloudapp.net.
3. Den **fjärr-ID för** ska vara samma som adressen till servern (Gateway FQDN).
4. Den **lokalt ID** ska vara samma som den **ämne** av klientcertifikatet.
5. Klicka på **autentiseringsinställningar** att öppna sidan Inställningar för autentisering.

  ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Kontrollera att **certifikat** väljs i listrutan.
7. Klicka på den **Välj** knappen och kontrollera att rätt certifikat har valts. Klicka på **OK** att spara några ändringar.

## <a name="ikev2"></a>Felsökning av autentisering med användarnamn och lösenord

1. Kontrollera inställningarna för VPN-klienten. Gå till den **nätverksinställningen** genom att trycka på Kommando + Skift och skriv sedan ”VPN” för att kontrollera inställningarna för VPN-klienten. I listan klickar du på VPN-posten som ska undersökas.

  ![Användarlösenordet för IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Kontrollera att den **serveradress** är fullständigt FQDN och inkluderar cloudapp.net.
3. Den **fjärr-ID för** ska vara samma som adressen till servern (Gateway FQDN).
4. Den **lokalt ID** kan vara tom.
5. Klicka på den **autentisering** knappen och kontrollera att ”användarnamn” är markerad i listrutan.

  ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Kontrollera att rätt autentiseringsuppgifter har angetts.

## <a name="additional"></a>Ytterligare steg

Om du försöker de föregående stegen och allt är korrekt konfigurerad, hämtar [Wireshark](https://www.wireshark.org/#download) och utföra en paketinsamling.

1. Filtrera efter *iskmp* och titta på den **IKE_SA** paket. Du ska kunna visa SA förslag informationen under den **nyttolast: säkerhetsassociation**. 
2. Kontrollera att klienten och servern har en gemensam uppsättning.

  ![Paket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>Nästa steg
Mer hjälp finns [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
