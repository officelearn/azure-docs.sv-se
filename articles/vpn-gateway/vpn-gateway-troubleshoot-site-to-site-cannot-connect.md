---
title: "Felsöka en Azure VPN-anslutning för plats-till-plats som inte kan ansluta | Microsoft Docs"
description: "Lär dig hur du felsöker en plats-till-plats VPN-anslutning som plötsligt slutar fungera och kan inte återanslutas."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: genli
ms.openlocfilehash: 55cfba5e9730b123bba20dfdc5d10c1157352a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Felsökning: Azure plats-till-plats VPN-anslutningen kan inte ansluta och slutar fungera

När du har konfigurerat en plats-till-plats VPN-anslutning mellan ett lokalt nätverk och Azure-nätverk, VPN-anslutningen plötsligt att slutar fungera och kan inte återanslutas. Den här artikeln innehåller åtgärder för att hjälpa dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningssteg

Försök för att lösa problemet först till [återställa Azure VPN-gatewayen](vpn-gateway-resetgw-classic.md) och återställa tunneln från lokala VPN-enheten. Om problemet kvarstår följer du stegen nedan för att identifiera orsaken till problemet.

### <a name="prerequisite-step"></a>Innan du börjar:

Kontrollera vilken typ av Azure VPN-gatewayen.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Kontrollera den **översikt** sidan i VPN-gateway för informationen.
    
    ![Översikt över gatewayen](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1. Kontrollera om lokala VPN-enhet har verifierats

1. Kontrollera om du använder en [verifiera VPN-enhet och operativsystemversion](vpn-gateway-about-vpn-devices.md#devicetable). Om enheten inte är en verifierad VPN-enhet kan du behöva kontakta tillverkaren av enheten för att se om det finns ett kompatibilitetsproblem.

2. Kontrollera att VPN-enheten är korrekt konfigurerad. Mer information finns i [redigera enheten configuration exempel](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Steg 2. Kontrollera den delade nyckeln

Jämför den delade nyckeln för lokala VPN-enhet till Azure virtuella nätverk VPN för att se till att det matchar nycklarna. 

Använd någon av följande metoder om du vill visa den delade nyckeln för Azure VPN-anslutningen:

**Azure Portal**

1. Gå till VPN-gateway för plats-till-plats-anslutningen som du skapade.

2. I den **inställningar** klickar du på **delad nyckel**.
    
    ![Delad nyckel](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

För Azure Resource Manager-distributionsmodellen:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

För den klassiska distributionsmodellen:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Steg 3. Kontrollera VPN-peer IP-adresser

-   IP-definition i den **lokala nätverksgateway** objektet i Azure måste matcha IP-Adressen för lokala enheter.
-   IP-Azure gateway-definition som är inställd på den lokala enheten ska matcha Azure gateway-IP.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Steg 4. Kontrollera UDR och NSG: er på gateway-undernätet.

Sök efter och ta bort användardefinierade routning (UDR) eller Nätverkssäkerhetsgrupper (NSG: er) i gatewayundernät och testa resultatet. Verifiera inställningarna som UDR eller NSG tillämpas om problemet är löst.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Steg 5. Kontrollera den lokala VPN externa gränssnitt Enhetsadressen

- Om Internet-riktade IP-adressen för VPN-enhet som ingår i den **lokala nätverket** definition i Azure, kan du uppleva sporadiska frånkopplingar.
- Enhetens externa gränssnittet måste vara direkt på Internet. Det bör finnas några nätverksadresser eller brandvägg mellan Internet och enheten.
- Om du vill konfigurera brandväggen kluster om du vill att en virtuell IP-adress, måste du bryta klustret och exponera VPN-enhet direkt till ett offentligt gränssnitt som gatewayen kan samverka med.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Steg 6. Kontrollera att undernäten matchar exakt (Azure policy-baserad gateway)

-   Kontrollera att undernäten matchar exakt mellan de virtuella Azure-nätverket och lokala definitioner för virtuella Azure-nätverket.
-   Kontrollera att undernäten matchar exakt mellan de **lokala nätverksgateway** och lokala definitioner för det lokala nätverket.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Steg 7. Kontrollera hälsoavsökningen Azure gateway

1. Gå till den [hälsoavsökningen](https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe).

2. Klicka dig igenom certifikatvarningen.
3. Om du får ett svar, anses VPN-gatewayen felfritt. Om du inte får ett svar, gatewayen kanske inte är felfri eller en NSG på gateway-undernätet som orsakar problemet. Följande är ett exempelsvar:

    &lt;? xml-version = ”1.0”? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">primära-instansen: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / sträng&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Steg 8. Kontrollera om lokala VPN-enhet har PFS-funktionen aktiverad

Funktionen PFS kan orsaka problem för frånkoppling. Om VPN-enhet har PFS aktiverad, kan du inaktivera funktionen. Uppdatera VPN-gateway IPsec-princip.

## <a name="next-steps"></a>Nästa steg

-   [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurera en princip för IPsec/IKE för plats-till-plats VPN-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md)
