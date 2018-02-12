---
title: "Säker Azure VPN gateway RADIUS-autentisering med NPS-servern för flerfunktionsautentisering | Microsoft Docs"
description: "Beskriver integrera Azure gateway RADIUS-autentisering med NPS-servern för flerfunktionsautentisering."
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: genli
ms.openlocfilehash: 0754c6cab9de2f93e9a57e202227a81c51bedf4c
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrera Azure VPN gateway RADIUS-autentisering med NPS-server för Multifaktorautentisering 

Artikeln beskriver hur du integrerar Server (NPS)-server med Azure VPN gateway RADIUS-autentisering för att leverera Multi-Factor Authentication (MFA) för punkt-till-plats VPN-anslutningar. 

## <a name="prerequisite"></a>Krav

Om du vill aktivera MFA måste användarna måste vara i Azure Active Directory (synkroniserats från lokala eller endast moln) och användaren måste redan ha slutfört bevis registreringen för MFA.  Användare kan slutföra bevis snabbare med https://myapps.microsoft.com.

## <a name="detailed-steps"></a>Detaljerade steg

### <a name="step-1-create-virtual-network-gateway"></a>Steg 1 Skapa virtuell nätverksgateway

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I det virtuella nätverket som är värd för den virtuella nätverksgatewayen, Välj **undernät**, och välj sedan **gatewayundernät** att skapa ett undernät. 

    ![Bilden om hur du lägger till gateway-undernät](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Skapa en virtuell nätverksgateway med följande inställningar:

    - **Gateway-typ**: Välj **VPN**.
    - **VPN-typ**: Välj **ruttbaserad**.
    - **SKU**: Välj en typ av SKU baserat på dina krav.
    - **Virtuellt nätverk**: Välj det virtuella nätverk som du skapade gateway-undernätet.

        ![Bilden om gateway-inställningar för virtuella nätverk](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Steg 2 konfigurera NPS för Azure MFA

1. Om NPS-servern [Installera NPS-tillägg för Azure MFA](../multi-factor-authentication/multi-factor-authentication-nps-extension.md#install-the-nps-extension).
2. Öppna anmärkningar konsolen, högerklicka på **RADUIS klienter**väljer **ny**. Skapa en RADUIS-klient med följande inställningar:

    - **Eget namn**: Skriv ett namn.
    - **Adress (IP eller DNS)**: Skriv gateway-undernätet som du skapade i steg 1.
    - **Delad hemlighet**: Ange eventuella hemlighet och komma ihåg det. Vi använder den senare.

    ![Bilden om RADUIS klientinställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  I den **Avancerat** fliken genom att ange leverantörens namn **RADIUS-Standard** och se till att den **ytterligare alternativ** inte är markerade.

    ![Bilden om RADUIS avancerade inställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Gå till **principer** > **nätverksprinciper**, dubbelklicka på **anslutningar till Microsoft Routing and Remote Access server** princip, Välj  **Bevilja åtkomst**, och klicka sedan på **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Steg 3 Konfigurera den virtuella nätverksgatewayen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna den virtuella nätverksgatewayen som du har skapat, se till att gateway-typen är **VPN** och VPN-typ är **ruttbaserad**.
3. Klicka på **peka platskonfiguration** > **konfigurera nu**, och Lägg sedan till följande inställningar:

    - **-Adresspoolen**: Skriv gateway-undernätet som du skapade i steg 1.
    - **Autentiseringstypen**: Välj **RADIUS-autentisering**.
    - **Serverns IP-adress**: Ange IP-adressen för NPS-servern.

    ![Bilden om peka platsinställningar](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)
