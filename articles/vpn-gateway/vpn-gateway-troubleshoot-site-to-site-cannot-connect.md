---
title: 'Felsöka en VPN-anslutning från Azure-plats till plats som inte kan ansluta titleSuffix: Azure VPN Gateway'
description: Lär dig hur du felsöker en VPN-anslutning från plats till plats som plötsligt slutar fungera och inte kan återanslutas.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862585"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Felsökning: En VPN-anslutning från Azure-plats till plats kan inte ansluta och slutar fungera

När du har konfigurerat en plats-till-plats-VPN-anslutning mellan ett lokalt nätverk och ett virtuellt Azure-nätverk slutar VPN-anslutningen plötsligt att fungera och kan inte återanslutas. Den här artikeln innehåller felsökningssteg som hjälper dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

LÃ¶s problemet genom att först [fÃ¶ästã¶ser](vpn-gateway-resetgw-classic.md) till att azure VPN-gatewayen fÃ¶rsÃ¶rsÃ¶rsÃ¶k och lÃ¶sa tunneln frÃ¥r den lokala VPN-enheten. Om problemet kvarstår följer du dessa steg för att identifiera orsaken till problemet.

### <a name="prerequisite-step"></a>Förutsättningssteg

Kontrollera typen av Azure VPN-gateway.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Sök på typinformationen på sidan **Översikt** för VPN-gatewayen.
    
    ![Översikt över gatewayen](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1. Kontrollera om den lokala VPN-enheten har validerats

1. Kontrollera om du använder en [validerad VPN-enhet och operativsystemversion](vpn-gateway-about-vpn-devices.md#devicetable). Om enheten inte är en validerad VPN-enhet kan du behöva kontakta enhetstillverkaren för att se om det finns ett kompatibilitetsproblem.

2. Kontrollera att VPN-enheten är korrekt konfigurerad. Mer information finns i [Redigera exempel på enhetskonfiguration](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Steg 2. Verifiera den delade nyckeln

Jämför den delade nyckeln för den lokala VPN-enheten med Azure Virtual Network VPN för att se till att nycklarna matchar. 

Om du vill visa den delade nyckeln för Azure VPN-anslutningen använder du någon av följande metoder:

**Azure-portal**

1. Gå till vpn-gatewayens plats-till-plats-anslutning som du skapade.

2. Klicka på Delad **nyckel**i avsnittet **Inställningar** .
    
    ![Delad nyckel](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För distributionsmodellen för Azure Resource Manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

För den klassiska distributionsmodellen:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Steg 3. Verifiera VPN-peer-IPs

-   IP-definitionen i local **network gateway-objektet** i Azure bör matcha den lokala enhetens IP.
-   Azure gateway IP-definitionen som anges på den lokala enheten bör matcha Azure gateway IP.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Steg 4. Kontrollera UDR och NSG:er i gateway-undernätet

Sök efter och ta bort användardefinierad routning (UDR) eller NSG (Network Security Groups) i gatewayundernätet och testa resultatet. Om problemet är löst validerar du de inställningar som UDR eller NSG tillämpade.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Steg 5. Kontrollera den lokala externa gränssnittsadressen för VPN-enheten

- Om VPN-enhetens IP-adresss IP-adress inkluderas i definitionen **för lokalt nätverk** i Azure kan du uppleva sporadiska frånkopplingar.
- Enhetens externa gränssnitt måste vara direkt på Internet. Det bör inte finnas någon översättning av nätverksadresser eller brandvägg mellan Internet och enheten.
- Om du vill konfigurera brandväggskluster för att ha en virtuell IP måste du bryta klustret och exponera VPN-enheten direkt till ett offentligt gränssnitt som gatewayen kan samverka med.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Steg 6. Kontrollera att undernäten matchar exakt (Azure-principbaserade gateways)

-   Kontrollera att det eller de virtuella nätverksadressutrymmena matchar exakt mellan det virtuella Azure-nätverket och lokala definitioner.
-   Kontrollera att undernäten matchar exakt mellan den **lokala nätverksgatewayen** och lokala definitioner för det lokala nätverket.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Steg 7. Verifiera hälsoavsökningen för Azure gateway

1. Öppna hälsoavsökningen genom att bläddra till följande WEBBADRESS:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Klicka dig igenom certifikatvarningen.
3. Om du får ett svar anses VPN-gatewayen vara felfri. Om du inte får något svar kanske gatewayen inte är felfri eller så orsakar en NSG på gateway-undernätet problemet. Följande text är ett exempelsvar:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Steg 8. Kontrollera om den lokala VPN-enheten har den perfekta funktionen för sekretess framåt aktiverad

Den perfekta funktionen för framåtsekretess kan orsaka problem med frånkoppling. Om VPN-enheten har perfekt sekretess framåt inaktiverar du funktionen. Uppdatera sedan VPN-gatewayens IPsec-princip.

## <a name="next-steps"></a>Nästa steg

-   [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurera en IPsec/IKE-princip för VPN-anslutningar från plats till plats](vpn-gateway-ipsecikepolicy-rm-powershell.md)
