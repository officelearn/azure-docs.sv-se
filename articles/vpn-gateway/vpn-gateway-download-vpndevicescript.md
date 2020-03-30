---
title: Ladda ned KONFIGURATIONSskript för VPN-enheter för S2S VPN-anslutningar
description: I den här artikeln går du igenom hämtning av VPN-enhetskonfigurationsskript för S2S VPN-anslutningar med Azure VPN Gateways med Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162144"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Ladda ned KONFIGURATIONSskript för VPN-enheter för S2S VPN-anslutningar

I den här artikeln går du igenom hämtning av VPN-enhetskonfigurationsskript för S2S VPN-anslutningar med Azure VPN Gateways med Azure Resource Manager. I följande diagram visas arbetsflödet på hög nivå.

![ladda ner-skript](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Följande enheter har tillgängliga skript:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Om konfigurationsskript för VPN-enheter

En vpn-anslutning över flera lokaler består av en Azure VPN-gateway, en lokal VPN-enhet och en IPsec S2S VPN-tunnel som förbinder de två. Det typiska arbetsflödet innehåller följande steg:

1. Skapa och konfigurera en Azure VPN-gateway (virtuell nätverksgateway)
2. Skapa och konfigurera en Azure-gateway för lokalt nätverk som representerar din lokala nätverks- och VPN-enhet
3. Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gatewayen och den lokala nätverksgatewayen
4. Konfigurera den lokala VPN-enheten som representeras av den lokala nätverksgatewayen för att upprätta den faktiska S2S VPN-tunneln med Azure VPN-gatewayen

Du kan slutföra steg 1 till och med 3 med [Azure-portalen,](vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)eller [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Det sista steget innebär att konfigurera lokala VPN-enheter utanför Azure. Med den här funktionen kan du hämta ett konfigurationsskript för din VPN-enhet med motsvarande värden för din Azure VPN-gateway, virtuella nätverk och lokala nätverksadressprefix och VPN-anslutningsegenskaper etc. som redan har fyllts i. Du kan använda skriptet som utgångspunkt eller använda skriptet direkt på dina lokala VPN-enheter via konfigurationskonsolen.

> [!IMPORTANT]
> * Syntaxen för varje VPN-enhet konfigurationsskript är olika, och starkt beroende av modeller och firmware versioner. Var särskilt uppmärksam på din enhetsmodell och versionsinformation mot tillgängliga mallar.
> * Vissa parametervärden måste vara unika på enheten och kan inte fastställas utan åtkomst till enheten. De Azure-genererade konfigurationsskripten förfyller dessa värden, men du måste se till att de angivna värdena är giltiga på din enhet. För exempel:
>    * Gränssnittsnummer
>    * Antal i listan för åtkomstkontroll
>    * Policynamn eller siffror, etc.
> * Leta efter nyckelordet "**REPLACE**", inbäddat i skriptet för att hitta de parametrar du behöver verifiera innan du använder skriptet.
> * Vissa mallar innehåller avsnittet "**RENSNING**" som du kan använda för att ta bort konfigurationerna. Rensningsavsnitten kommenteras som standard.

## <a name="download-the-configuration-script-from-azure-portal"></a>Hämta konfigurationsskriptet från Azure Portal

Skapa en Azure VPN-gateway, lokal nätverksgateway och en anslutningsresurs som ansluter de två. Följande sida guidar dig genom stegen:

* [Skapa en plats-till-plats-anslutning på Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

När anslutningsresursen har skapats följer du instruktionerna nedan för att hämta konfigurationsskripten för VPN-enheten:

1. Från en webbläsare navigerar du till [Azure-portalen](https://portal.azure.com) och loggar vid behov in med ditt Azure-konto
2. Gå till den anslutningsresurs som du skapade. Du hittar listan över alla anslutningsresurser genom att klicka på "Alla tjänster", sedan "NÄTVERK" och "Anslutningar".

    ![anslutningslista](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klicka på den anslutning du vill konfigurera.

    ![anslutningsöversikt](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klicka på länken "Hämta konfiguration" som markerats i rött på sidan Anslutningsöversikt; Detta öppnar sidan "Hämta konfiguration".

    ![ladda ner-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Välj modellfamilj och firmware-version för din VPN-enhet och klicka sedan på knappen "Ladda ned konfiguration".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Du uppmanas att spara det nedladdade skriptet (en textfil) från webbläsaren.
7. När du har hämtat konfigurationsskriptet öppnar du det med en textredigerare och söker efter nyckelordet "ERSÄTT" för att identifiera och undersöka de parametrar som kan behöva bytas ut.

    ![redigera-skript](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Hämta konfigurationsskriptet med Azure PowerShell



Du kan också hämta konfigurationsskriptet med Azure PowerShell, som visas i följande exempel:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Använda konfigurationsskriptet på din VPN-enhet

När du har hämtat och validerat konfigurationsskriptet är nästa steg att använda skriptet på din VPN-enhet. Den faktiska proceduren varierar beroende på din VPN-enhet och modeller. Läs bruksanvisningarna eller instruktionssidorna för dina VPN-enheter.

## <a name="next-steps"></a>Nästa steg

Fortsätt konfigurera anslutningen [från plats till plats](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
