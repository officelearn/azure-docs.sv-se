---
title: Säkra Azure VPN gateway RADIUS-autentisering med NPS-server för multi-Factor Authentication | Microsoft Docs
description: Beskriver integrera Azure gateway RADIUS-autentisering med NPS-server för Multifaktorautentisering.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4e11b1bc16f874f892288f9677a71023f483de7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458142"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrera Azure VPN gateway RADIUS-autentisering med NPS-server för Multifaktorautentisering 

Artikeln beskriver hur du integrerar nätverksprincipserver (NPS) med Azure VPN gateway RADIUS-autentisering för att leverera Multi-Factor Authentication (MFA) för punkt-till-plats VPN-anslutningar. 

## <a name="prerequisite"></a>Krav

Om du vill aktivera MFA måste användarna vara i Azure Active Directory (Azure AD), som måste synkroniseras från antingen lokalt eller molnbaserade miljö. Dessutom måste användaren redan har slutfört den automatiska registreringen för MFA.  Mer information finns i [konfigurerar mitt konto för tvåstegsverifiering](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Detaljerade steg

### <a name="step-1-create-a-virtual-network-gateway"></a>Steg 1: Skapa en virtuell nätverksgateway

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det virtuella nätverket som är värd för den virtuella nätverksgatewayen, väljer **undernät**, och välj sedan **gatewayundernätet** att skapa ett undernät. 

    ![Bild som visar hur du lägger till gateway-undernät](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Skapa en virtuell nätverksgateway genom att ange följande inställningar:

    - **Gateway-typ**: Välj **VPN**.
    - **VPN-typ**: Välj **Routningsbaserad**.
    - **SKU**: Välj en SKU-typen baserat på dina krav.
    - **Virtuellt nätverk**: Välj det virtuella nätverket där du skapade gateway-undernätet.

        ![Bild som visar inställningar för virtuell nätverksgateway](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Steg 2 konfigurera NPS för Azure MFA

1. På NPS-servern [Installera NPS-tillägget för Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Öppna konsolen NSP:, högerklicka på **RADUIS klienter**, och välj sedan **New**. Skapa RADUIS-klienten genom att ange följande inställningar:

    - **Eget namn**: Skriv ett namn.
    - **Adress (IP eller DNS)**: Ange gateway-undernätet som du skapade i steg 1.
    - **Delad hemlighet**: Skriv alla hemlig nyckel och spara den för senare användning.

      ![Bild som visar RADUIS-klientinställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  På den **Avancerat** fliken genom att ange leverantörsnamnet **RADIUS-Standard** och se till att den **ytterligare alternativ** inte är markerad.

    ![Bild som visar RADUIS avancerade inställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Gå till **principer** > **nätverksprinciper**, dubbelklicka på **anslutningar till Microsoft Routing and Remote Access server** principer, Välj  **Bevilja åtkomst**, och klicka sedan på **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Steg 3 Konfigurera den virtuella nätverksgatewayen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna den virtuella nätverksgatewayen som du skapade. Se till att gateway-typ har angetts till **VPN** och att den VPN-typ är **routningsbaserad**.
3. Klicka på **peka platskonfiguration** > **konfigurera nu**, och sedan anger du följande inställningar:

    - **Adresspool**: Ange gateway-undernätet som du skapade i steg 1.
    - **Autentiseringstyp**: Välj **RADIUS-autentisering**.
    - **Serverns IP-adress**: Ange IP-adressen för NPS-servern.

      ![Bild som visar att platsinställningar](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
