---
title: 'Ladda ned konfigurationsskript för VPN-enheten för S2S VPN-anslutningar: Azure Resource Manager | Microsoft Docs'
description: Den här artikeln beskriver hur du laddar ned konfigurationsskript för VPN-enheten för S2S VPN-anslutningar med Azure VPN gateway med Azure Resource Manager.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: 0b0a7ce63fa2d0154300dd2e8f9cf88d985a8a0a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507698"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Ladda ned konfigurationsskript för VPN-enheten för S2S VPN-anslutningar

Den här artikeln beskriver hur du laddar ned konfigurationsskript för VPN-enheten för S2S VPN-anslutningar med Azure VPN gateway med Azure Resource Manager. Följande diagram visar det övergripande arbetsflödet.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Följande enheter har tillgängliga skript:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Om konfigurationsskript för VPN-enhet

En VPN-anslutning mellan lokala består av en Azure VPN-gateway, en lokal VPN-enhet och en IPsec S2S VPN-tunnel som ansluter två. Typiskt arbetsflöde innehåller följande steg:

1. Skapa och konfigurera en Azure VPN-gatewayen (virtuella nätverksgateway)
2. Skapa och konfigurera en lokal nätverksgateway i Azure som representerar ditt lokala nätverk och VPN-enhet
3. Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gatewayen och den lokala nätverksgatewayen
4. Konfigurera lokala VPN-enhet som representeras av den lokala nätverksgatewayen för att skapa den faktiska S2S VPN-tunneln med Azure VPN-gatewayen

Du kan slutföra steg 1 till 3 med Azure [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), eller [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Det sista steget innebär att konfigurera den lokala VPN-enheter utanför Azure. Den här funktionen kan du ladda ned ett konfigurationsskript för VPN-enheten med motsvarande värden för din Azure VPN-gateway, virtuellt nätverk, och adressprefixen för lokala nätverk och VPN-anslutningens egenskaper osv har redan fyllt i. Du kan använda skript som en startpunkt eller använda skriptet direkt i dina lokala VPN-enheter via konsolen för configuration.

> [!IMPORTANT]
> * Syntaxen för varje konfigurationsskript för VPN-enheten är olika och kraftigt beroende på modeller och versioner av inbyggd programvara. Vara särskilt uppmärksam på din modell och version enhetsinformation mot de tillgängliga mallarna.
> * Vissa parametervärden måste vara unika på enheten och går inte att fastställa utan att komma åt enheten. Azure-genererade konfigurationsskript förifylld i fälten för dessa värden, men du måste se till att de angivna värdena är giltiga på din enhet. Exempel:
>    * Gränssnittet siffror
>    * Åtkomstkontroll listan siffror
>    * Principnamn eller siffror osv.
> * Leta efter nyckelord ”,**Ersätt**” inbäddade i skriptet för att hitta de parametrar som du måste verifiera innan du tillämpar skriptet.
> * Vissa mallar innehåller en ”**Rensa**” avsnitt som du kan använda för att ta bort konfigurationerna. Rensa avsnitt är kommenterade som standard.

## <a name="download-the-configuration-script-from-azure-portal"></a>Ladda ned konfigurationsskriptet från Azure-portalen

Skapa en Azure VPN-gateway och lokal nätverksgateway en anslutningsresurs ansluta två. Följande sida vägleder dig genom stegen:

* [Skapa en plats-till-plats-anslutning i Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

När anslutningsresursen har skapats följer du anvisningarna nedan för att ladda ned konfigurationsskript för VPN-enheten:

1. Från en webbläsare, navigerar du till den [Azure-portalen](http://portal.azure.com) vid behov, logga in med ditt Azure-konto
2. Gå till connection-resursen som du skapade. Du hittar listan över alla anslutningsresurser som genom att klicka på ”alla tjänster”, och sedan ”nätverk” och ”-anslutningar”.

    ![Connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klicka på den anslutning du vill konfigurera.

    ![Översikt över anslutning](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klicka på länken ”Hämta configuration” som markerats i rött i Översikt-anslutningssidan; Då öppnas sidan ”Download-konfiguration”.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Välj modellversion för familj och inbyggd programvara för VPN-enheten och klicka på knappen ”Hämta konfiguration”.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Du uppmanas att spara det hämta skriptet (en textfil) från din webbläsare.
7. När du har hämtat konfigurationsskriptet, kan du öppna den med en textredigerare och Sök efter nyckelordet ”Ersätt” för att identifiera och undersöka de parametrar som kan behöva bytas.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Ladda ned konfigurationsskriptet med Azure PowerShell

Du kan också hämta konfigurationsskript med hjälp av Azure PowerShell, som visas i följande exempel:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Gäller konfigurationsskriptet för VPN-enheten

När du har hämtat och verifierats konfigurationsskriptet, är nästa steg att använda skriptet på din VPN-enhet. Den faktiska proceduren varierar beroende på dina modeller och VPN-enhet gör. Läs åtgärden handböcker eller instruktion-sidor för dina VPN-enheter.

## <a name="next-steps"></a>Nästa steg

Fortsätta konfigurera din [plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
