---
title: 'Konfigurera anpassad IPsec-princip för Azure Virtual WAN: Portal | Microsoft-dokument'
description: Lär dig hur du konfigurerar anpassad IPsec-princip för Azure Virtual WAN med hjälp av portalen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515751"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurera en anpassad IPsec-princip för Virtuellt WAN med hjälp av portalen

Du kan konfigurera anpassad IPsec-princip för Virtuellt WAN i Azure-portalen. Anpassade principer är användbara när du vill att båda sidor (lokalt och Azure VPN-gateway) ska använda samma inställningar för IKE fas 1 och IKE fas 2.

## <a name="working-with-custom-policies"></a>Arbeta med anpassade principer

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurera en princip

1. **Leta reda på den virtuella hubben**. Öppna en webbläsare, navigera till [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) och logga in med ditt Azure-konto. Leta reda på den virtuella hubben för din webbplats.
2. **Välj VPN-plats**. På hubbsidan väljer du den VPN-plats som du vill ställa in en anpassad princip för.

   ![välj](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Redigera VPN-anslutningen**. Välj **Redigera VPN-anslutning**på **Snabbmenyn** **...**.

   ![redigera](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurera inställningarna**. Konfigurera inställningarna på sidan **Redigera VPN-anslutning.** Välj **Spara** om du vill spara inställningarna.

   ![konfigurera och spara](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).