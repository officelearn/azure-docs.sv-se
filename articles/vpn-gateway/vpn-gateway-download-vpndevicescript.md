---
title: "Hämta VPN-enhet konfigurationsskript för S2S VPN-anslutningar: Azure Resource Manager | Microsoft Docs"
description: "Den här artikeln vägleder dig genom att hämta VPN-enhet konfigurationsskript för S2S VPN-anslutningar med Azure VPN-gatewayer med Azure Resource Manager."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2018
ms.author: yushwang
ms.openlocfilehash: 2ec428bc5d2cdfb376db3c27b9899014c7ffa2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Hämta VPN-enhet konfigurationsskript för S2S VPN-anslutningar

Den här artikeln vägleder dig genom att hämta VPN-enhet konfigurationsskript för S2S VPN-anslutningar med Azure VPN-gatewayer med Azure Resource Manager. Följande diagram visar utförligt arbetsflödet.

![skript för hämtning](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

## <a name="about"></a>Om konfigurationsskript för VPN-enhet

En VPN-anslutning mellan lokala består av en Azure VPN-gateway, en lokal VPN-enhet och en IPsec S2S VPN-tunnel som ansluter två. Vanliga arbetsflöde innehåller följande steg:

1. Skapa och konfigurera en Azure VPN-gateway (virtuella nätverksgateway)
2. Skapa och konfigurera en Azure lokal nätverksgateway som representerar ditt lokala nätverk och VPN-enhet
3. Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gateway och den lokala nätverksgatewayen
4. Konfigurera lokala VPN-enhet som representeras av den lokala nätverksgatewayen för att fastställa den faktiska S2S VPN-tunneln med Azure VPN-gateway

Du kan slutföra steg 1 till 3 med Azure [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), eller [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Det sista steget omfattar konfiguration av lokal VPN-enheter utanför Azure. Den här funktionen kan du hämta ett konfigurationsskript för VPN-enheten med motsvarande värden för din Azure VPN-gateway, virtuellt nätverk och adressprefix för lokalt nätverk och VPN-anslutningens egenskaper osv ifylld. Du kan använda skriptet som en startpunkt eller Använd skriptet direkt till din lokala VPN-enheter via konfigurationskonsolen för.

> [!IMPORTANT]
> * Syntaxen för varje konfiguration för VPN-enhetsskriptet är olika och kraftigt beroende av modeller och versioner av inbyggd. Vara särskilt uppmärksam på din modell och version enhetsinformation mot tillgängliga mallar.
> * Vissa parametervärden måste vara unika på enheten och inte kan fastställas utan åtkomst till enheten. Azure-genererade konfigurationsskript fyller före dessa värden, men du behöver säkerställa att de tillhandahållna värdena är giltiga på enheten. Exempel:
>    * Gränssnittet siffror
>    * Access control lista nummer
>    * Principens namn eller siffror osv.
> * Leta efter nyckelord ”,**ERSÄTTA**” inbäddade i skript för att hitta de parametrar som du måste kontrollera innan du använder skriptet.
> * Vissa mallar innehåller en ”**Rensa**” avsnitt som du kan använda för att ta bort konfigurationerna. Rensa avsnitt är kommenterade som standard.

## <a name="download-the-configuration-script-from-azure-portal"></a>Hämta skriptet konfiguration från Azure-portalen

Skapa en Azure VPN-gateway och lokal nätverksgateway en anslutning resurs ansluta två. Följande sida vägleder dig genom stegen:

* [Skapa en plats-till-plats-anslutning i Azure-portalen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Följ anvisningarna nedan för att hämta konfigurationsskript för VPN-enhet när anslutningen resursen har skapats:

1. Från en webbläsare, navigerar du till den [Azure-portalen](http://portal.azure.com) och vid behov, logga in med ditt Azure-konto
2. Gå till den anslutning resurs du skapat. Du hittar listan över alla resurser som anslutningen genom att ”alla tjänster” och ”nätverk” och ”anslutningar”.

    ![listan med anslutningar](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klicka på den anslutning du vill konfigurera.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klicka på länken ”Hämta configuration” som är markerade i rött översiktssidan anslutning; Detta öppnar sidan ”Hämta konfiguration”.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Välj modellversion för familj och inbyggd programvara för VPN-enhet, och klicka sedan på knappen ”ladda ned-konfiguration”.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Du uppmanas att spara det hämta skriptet (en textfil) från din webbläsare.
7. När du har hämtat skriptet konfiguration, kan du öppna den med en textredigerare och Sök efter nyckelordet ”Ersätt” för att identifiera och granska de parametrar som kan behöva ersättas.

    ![Redigera skript](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Hämta konfigurationsskript med hjälp av Azure PowerShell

Du kan också hämta konfigurationsskript med hjälp av Azure PowerShell, som visas i följande exempel:

```powershell
$Sub         = "<YourSubscriptionName>"
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite5"

Login-AzureRmAccount
Set-AzureRmContext -Subscription $Sub

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Gäller konfigurationsskript för VPN-enhet

När du har hämtat och validera skriptet konfigurationen, är nästa steg att använda skriptet på din VPN-enhet. Den faktiska proceduren varierar beroende på din VPN-enhet gör och modeller. Kontakta åtgärden handböcker eller instruktion sidor för VPN-enheter.

## <a name="next-steps"></a>Nästa steg

Fortsätta konfigurera din [plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
