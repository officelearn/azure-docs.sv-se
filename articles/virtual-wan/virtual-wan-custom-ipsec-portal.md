---
title: 'Konfigurera anpassad IPsec-princip för Azure Virtual WAN: Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar anpassad IPsec-princip för Azure Virtual WAN med portalen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 0ea4523d1558f6887e1aef344198026591dac617
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84752619"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurera en anpassad IPsec-princip för virtuellt WAN-nätverk med hjälp av portalen

Du kan konfigurera anpassad IPsec-princip för virtuellt WAN-nätverk i Azure Portal. Anpassade principer är användbara när du vill att både sidor (lokal och Azure VPN-gateway) ska använda samma inställningar för IKE fas 1 och IKE fas 2.

## <a name="working-with-custom-policies"></a>Arbeta med anpassade principer

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurera en princip

1. **Leta upp den virtuella hubben**. Öppna en webbläsare, navigera till [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) och logga in med ditt Azure-konto. Leta upp den virtuella hubben för platsen.
2. **Välj VPN-platsen**. På sidan hubb väljer du den VPN-plats som du vill konfigurera en anpassad princip för.

   ![välj](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Redigera VPN-anslutningen**. Från **snabb menyn** **...** väljer du **Redigera VPN-anslutning**.

   ![redigera](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurera inställningarna**. Konfigurera inställningarna på sidan **Redigera VPN-anslutning** . Spara inställningarna genom att välja **Spara** .

   ![Konfigurera och spara](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).