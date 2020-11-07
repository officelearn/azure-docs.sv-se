---
title: 'Självstudie: Använd Azure Virtual WAN för att skapa en punkt-till-plats-anslutning till Azure'
description: I den här självstudien förklarar vi hur du skapar en punkt-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 645d5beb19b738e2269c0ec9e5b84fb140c7deb8
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359514"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Självstudie: skapa en VPN-anslutning för användare med Azure Virtual WAN

I den här självstudien förklarar vi hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller en OpenVPN VPN-anslutning. Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuellt WAN finns i [Översikt över virtuella WAN-nätverk](virtual-wan-about.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en P2S-konfiguration
> * Skapa en hubb
> * Ange DNS-servrar
> * Ladda ned en VPN-klient profil
> * Visa virtuellt WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Skapa en P2S-konfiguration

En punkt-till-plats-konfiguration (P2S) definierar parametrar för att ansluta fjärrklienter.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Skapa hubb med punkt-till-plats-Gateway

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Ange DNS-Server

Med VPN-gatewayer för virtuella WAN-användare kan du ange upp till fem DNS-servrar. Du kan konfigurera detta när du skapar hubben eller ändrar det vid ett senare tillfälle. Det gör du genom att leta upp den virtuella hubben. Under **användar-VPN (peka på plats)** , väljer du **Konfigurera** och anger DNS-serverns IP-adress (er) i den **anpassade DNS-** serverns text ruta (er).

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="anpassad DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Konfigurera användares VPN-klienter

Använd den nedladdade profilen för att konfigurera fjärråtkomstklienterna. Proceduren för varje operativ system är annorlunda, följ instruktionerna som gäller för systemet.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
1. På sidan **Översikt** representerar varje punkt på kartan ett nav.
1. I avsnittet **hubbar och anslutningar** kan du Visa nav status, plats, region, status för VPN-anslutning och byte in och ut.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Sedan kan du läsa mer om virtuellt WAN-nätverk i:

> [!div class="nextstepaction"]
> * [Vanliga frågor om Virtual WAN](virtual-wan-faq.md)
