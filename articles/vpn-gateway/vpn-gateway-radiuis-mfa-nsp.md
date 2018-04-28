---
title: Säker Azure VPN gateway RADIUS-autentisering med NPS-servern för flerfunktionsautentisering | Microsoft Docs
description: Beskriver integrera Azure gateway RADIUS-autentisering med NPS-servern för flerfunktionsautentisering.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: 665e1914f44d7c5e650a1b632d8b11c6d8a5931a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrera Azure VPN gateway RADIUS-autentisering med NPS-server för Multifaktorautentisering 

Artikeln beskriver hur du integrerar Server (NPS) med Azure VPN gateway RADIUS-autentisering för att leverera Multi-Factor Authentication (MFA) för punkt-till-plats VPN-anslutningar. 

## <a name="prerequisite"></a>Krav

Om du vill aktivera MFA måste användarna vara i Azure Active Directory (Azure AD), som måste synkroniseras från antingen lokalt eller molnbaserade miljön. Dessutom måste användaren redan har slutfört den automatiska registreringen för Multifaktorautentisering.  Mer information finns i [Konfigurera mitt konto för tvåstegsverifiering](../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Detaljerade steg

### <a name="step-1-create-a-virtual-network-gateway"></a>Steg 1: Skapa en virtuell nätverksgateway

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det virtuella nätverket som är värd för den virtuella nätverksgatewayen, Välj **undernät**, och välj sedan **gatewayundernät** att skapa ett undernät. 

    ![Bilden om hur du lägger till gateway-undernät](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Skapa en virtuell nätverksgateway genom att ange följande inställningar:

    - **Gatewaytyp**: välj **VPN**.
    - **VPN-typ**: Välj **ruttbaserad**.
    - **SKU**: Välj en typ av SKU baserat på dina krav.
    - **Virtuellt nätverk**: Välj det virtuella nätverk som du skapade gateway-undernätet.

        ![Bilden om gateway-inställningar för virtuella nätverk](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Steg 2 konfigurera NPS för Azure MFA

1. Om NPS-servern [Installera NPS-tillägg för Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Öppna konsolen anmärkningar, högerklicka på **RADUIS klienter**, och välj sedan **ny**. Skapa RADUIS klienten genom att ange följande inställningar:

    - **Eget namn**: Skriv ett namn.
    - **Adress (IP eller DNS)**: Skriv gateway-undernätet som du skapade i steg 1.
    - **Delad hemlighet**: Skriv alla hemlig nyckel och spara den för senare användning.

    ![Bilden om RADUIS klientinställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  På den **Avancerat** fliken genom att ange leverantörens namn **RADIUS-Standard** och se till att den **ytterligare alternativ** inte är markerad.

    ![Bilden om RADUIS avancerade inställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Gå till **principer** > **nätverksprinciper**, dubbelklicka på **anslutningar till Microsoft Routing and Remote Access server** princip, Välj  **Bevilja åtkomst**, och klicka sedan på **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Steg 3 Konfigurera den virtuella nätverksgatewayen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna den virtuella nätverksgatewayen som du skapade. Se till att gateway-typ har angetts till **VPN** och att VPN-typ är **ruttbaserad**.
3. Klicka på **peka platskonfiguration** > **konfigurera nu**, och ange följande inställningar:

    - **-Adresspoolen**: Skriv gateway-undernätet som du skapade i steg 1.
    - **Autentiseringstypen**: Välj **RADIUS-autentisering**.
    - **Serverns IP-adress**: Ange IP-adressen för NPS-servern.

    ![Bilden om peka platsinställningar](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
