---
title: Felsöka en Azure VPN-anslutning för plats-till-plats som inte kan ansluta | Microsoft Docs
description: Lär dig hur du felsöker en plats-till-plats VPN-anslutning som plötsligt slutar fungera och det går inte att återanslutas.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 01729971169011002fa4231f043f82f105f81cdc
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414434"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Felsökning: En Azure VPN-anslutning för plats-till-plats kan inte ansluta och slutar fungera

När du har konfigurerat en plats-till-plats VPN-anslutning mellan ett lokalt nätverk och ett Azure-nätverk, VPN-anslutningen plötsligt att slutar fungera och kan inte återanslutas. Den här artikeln innehåller felsökningssteg för att hjälpa dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

För att lösa problemet, försöker först [återställa Azure VPN-gatewayen](vpn-gateway-resetgw-classic.md) och återställa tunneln från den lokala VPN-enheten. Om problemet kvarstår, följer du stegen nedan för att identifiera orsaken till problemet.

### <a name="prerequisite-step"></a>Innan du börjar:

Kontrollera vilken typ av Azure VPN-gateway.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Kontrollera den **översikt** för VPN-gateway för informationen.
    
    ![Översikt över gatewayen](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1. Kontrollera om den lokala VPN-enheten har verifierats

1. Kontrollera om du använder en [verifierade VPN-enhet- och operativsystemets version](vpn-gateway-about-vpn-devices.md#devicetable). Om enheten inte är en validerade VPN-enhet kan du behöva kontakta enhetstillverkaren för att se om det finns ett kompatibilitetsproblem.

2. Kontrollera att VPN-enheten är korrekt konfigurerad. Mer information finns i [redigera enhetens Konfigurationsexempel](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Steg 2. Kontrollera den delade nyckeln

Jämför den delade nyckeln för den lokala VPN-enheten till Azure Virtual Network VPN för att se till att nycklarna matchar. 

Använd någon av följande metoder om du vill visa den delade nyckeln för Azure VPN-anslutningen:

**Azure Portal**

1. Gå till VPN-gateway plats-till-plats-anslutningen som du skapade.

2. I den **inställningar** klickar du på **delad nyckel**.
    
    ![Delad nyckel](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För Azure Resource Manager-distributionsmodellen:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

För den klassiska distributionsmodellen:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Steg 3. Verifiera VPN-peer IP-adresser

-   IP-definitionen i den **lokala nätverksgateway** objektet i Azure måste matcha IP-Adressen för den lokala enheten.
-   Azure gateway IP-definition som är inställd på den lokala enheten ska matcha Azure gateway-IP.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Steg 4. Kontrollera UDR och NSG: er på gateway-undernätet

Sök efter och ta bort en användardefinierad routning (UDR) eller Nätverkssäkerhetsgrupper (NSG) på gateway-undernätet och testar sedan resultatet. Om problemet är löst, verifierar du de inställningar som UDR eller NSG tillämpas.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Steg 5. Kontrollera den lokala VPN-enhetens externa gränssnittet på adress

- Om internetuppkopplade IP-adressen för VPN-enheten som ingår i den **lokalt nätverk** definition i Azure, kan du uppleva sporadiska frånkopplingar.
- Enhetens externa gränssnittet måste vara direkt på Internet. Det bör finnas några nätverksadresser eller en brandvägg mellan Internet och enheten.
- Om du vill konfigurera klustring för att du har en virtuell IP-brandväggen måste du bryta klustret och exponera VPN-enhet direkt till ett offentligt gränssnitt som gatewayen kan användas med.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Steg 6. Kontrollera att undernäten matchar exakt (Azure principbaserade gatewayer)

-   Kontrollera att vnet-adress space(s) matchar exakt mellan Azure-nätverk och lokala definitioner.
-   Kontrollera att undernäten matchar exakt mellan de **lokala nätverksgateway** och lokala definitioner för det lokala nätverket.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Steg 7. Kontrollera hälsoavsökningen Azure-gateway

1. Öppna hälsoavsökning genom att bläddra till följande URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Klicka dig igenom certifikatvarningen.
3. Om du får ett svar, anses VPN-gatewayen felfritt. Om du inte får ett svar kan gatewayen kanske inte är felfri eller en NSG på gateway-undernätet som orsakar problemet. Följande text är ett exempelsvaret:

    &lt;? XML-version = ”1.0”? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">primära-instansen: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Steg 8. Kontrollera om den lokala VPN-enheten har funktionen PFS aktiverad

PFS-funktionen kan orsaka frånkoppling av problem. Om VPN-enheten har PFS aktiverat kan du inaktivera funktionen. Uppdatera sedan VPN-gateway IPsec-principen.

## <a name="next-steps"></a>Nästa steg

-   [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurera en IPsec/IKE-princip för plats-till-plats VPN-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md)
