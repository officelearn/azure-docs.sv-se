---
title: Felsöka Azure punkt-till-plats-VPN-anslutningar från klienter på Mac OS X | Microsoft Docs
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
ms.openlocfilehash: bc8e95b07d984505fc0e5fdad41fe120463b5b0e
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585534"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Felsöka punkt-till-plats-VPN-anslutningar från VPN för Mac OS X-klienter

Den här artikeln hjälper dig att felsöka problem med punkt-till-plats-anslutningen från Mac OS X med hjälp av inbyggda VPN-klienten och IKEv2. VPN-klienten i Mac för IKEv2 är mycket enkel och inte tillåter att för mycket anpassning. Det finns bara fyra inställningar som behöver du:

* Serveradress
* Fjärr-ID
* Lokala ID: T
* Autentiseringsinställningar
* OS-Version (10.11 eller senare)


## <a name="VPNClient"></a> Felsöka certifikatbaserad autentisering
1. Kontrollera inställningarna för VPN-klienten. Gå till den **nätverksinställningen** genom att trycka på Kommando + Skift och skriv sedan ”VPN” för att kontrollera inställningarna för VPN-klienten. I listan klickar du på VPN-posten som behöver undersökas.

  ![IKEv2 certifikatbaserad autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Kontrollera att den **serveradress** är fullständig FQDN och inkluderar cloudapp.net.
3. Den **fjärr-ID för** ska vara samma som serveradress (Gateway FQDN).
4. Den **lokala ID: T** bör vara samma som den **ämne** för klientcertifikatet.
5. Klicka på **autentiseringsinställningar** att öppna inställningssidan för autentisering.

  ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Kontrollera att **certifikat** väljs i listrutan.
7. Klicka på den **Välj** knappen och kontrollera att rätt certifikat har valts. Klicka på **OK** spara några ändringar.

## <a name="ikev2"></a>Felsöka autentisering med användarnamn och lösenord

1. Kontrollera inställningarna för VPN-klienten. Gå till den **nätverksinställningen** genom att trycka på Kommando + Skift och skriv sedan ”VPN” för att kontrollera inställningarna för VPN-klienten. I listan klickar du på VPN-posten som behöver undersökas.

  ![IKEv2-användarnamnet](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Kontrollera att den **serveradress** är fullständig FQDN och inkluderar cloudapp.net.
3. Den **fjärr-ID för** ska vara samma som serveradress (Gateway FQDN).
4. Den **lokala ID: T** kan vara tom.
5. Klicka på den **autentisering på** knappen och kontrollera att ”användarnamn” har valts i listrutan.

  ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Kontrollera att rätt autentiseringsuppgifter har angetts.

## <a name="additional"></a>Ytterligare steg

Om du försöker de föregående stegen och allt är korrekt konfigurerad, kan du ladda ned [Wireshark](https://www.wireshark.org/#download) och utföra ett infångat paket.

1. Filtrera på *isakmp* och titta på den **IKE_SA** paket. Du bör kunna titta på informationen för SA-förslag under den **nyttolast: säkerhetsassociation**. 
2. Kontrollera att klienten och servern har en gemensam uppsättning.

  ![paket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>Nästa steg
Mer hjälp finns i [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
