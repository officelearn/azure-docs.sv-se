---
title: Integrera NPS med VPN Gateway RADIUS-autentisering för MFA
description: Beskriver integrering av Azure gateway RADIUS-autentisering med NPS-server för multifaktorautentisering.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 941b6ac86941824351f83592998e8735e3eb8ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780376"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrera Azure VPN gateway RADIUS-autentisering med NPS-server för multifaktorautentisering 

I artikeln beskrivs hur du integrerar NPS (Network Policy Server) med Azure VPN gateway RADIUS-autentisering för att leverera MFA (Multi-Factor Authentication) för vpn-anslutningar från punkt till plats. 

## <a name="prerequisite"></a>Krav

För att aktivera MFA måste användarna vara i Azure Active Directory (Azure AD), som måste synkroniseras från antingen den lokala miljön eller molnmiljön. Dessutom måste användaren redan ha slutfört den automatiska registreringsprocessen för MFA.  Mer information finns i [Konfigurera mitt konto för tvåstegsverifiering](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Detaljerade steg

### <a name="step-1-create-a-virtual-network-gateway"></a>Steg 1: Skapa en virtuell nätverksgateway

1. Logga in på [Azure portal](https://portal.azure.com).
2. I det virtuella nätverket som ska vara värd för den virtuella nätverksgatewayen väljer du **Undernät**och väljer sedan **Gateway-undernät** för att skapa ett undernät. 

    ![Bilden om hur du lägger till gateway-undernät](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Skapa en virtuell nätverksgateway genom att ange följande inställningar:

    - **Gatewaytyp**: välj **VPN**.
    - **VPN-typ**: Välj **Ruttbaserad**.
    - **SKU**: Välj en SKU-typ baserat på dina behov.
    - **Virtuellt nätverk:** Välj det virtuella nätverk där du skapade gateway-undernätet.

        ![Avbildningen om inställningar för virtuell nätverksgateway](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Steg 2 Konfigurera NPS för Azure MFA

1. Installera [NPS-tillägget för Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)på NPS-servern .
2. Öppna NPS-konsolen, högerklicka på **RADIUS-klienter**och välj sedan **Ny**. Skapa RADIUS-klienten genom att ange följande inställningar:

    - **Eget namn:** Skriv vilket namn som helst.
    - **Adress (IP eller DNS)**: Skriv gatewayundernätet som du skapade i steg 1.
    - **Delad hemlighet**: skriv en hemlig nyckel och kom ihåg den för senare användning.

      ![Bilden om RADIUS-klientinställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  På fliken **Avancerat** anger du leverantörsnamnet till **RADIUS Standard** och kontrollerar att kryssrutan **Ytterligare alternativ** inte är markerad.

    ![Bilden om RADIUS-klient avancerade inställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Gå till **Principer** > **Nätverksprinciper**, dubbelklicka på **Anslutningar till Microsoft Routning och fjärråtkomstserverprincip,** välj **Bevilja åtkomst**och klicka sedan på **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Steg 3 Konfigurera den virtuella nätverksgatewayen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna den virtuella nätverksgateway som du skapade. Kontrollera att gatewaytypen är inställd på **VPN** och att **VPN-typen**är ruttbaserad .
3. Klicka på **Peka på platskonfiguration** > **Konfigurera nu**och ange sedan följande inställningar:

    - **Adresspool:** Skriv gateway-undernätet som du skapade i steg 1.
    - **Autentiseringstyp**: Välj **RADIUS-autentisering**.
    - **Server-IP-adress**: Skriv IP-adressen för NPS-servern.

      ![Bilden om pekar på webbplatsinställningar](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nästa steg

- [Azure multifaktorautentisering](../active-directory/authentication/multi-factor-authentication.md)
- [Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
