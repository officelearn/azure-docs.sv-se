---
title: 'Scenario: dirigera till delade tjänster virtuella nätverk'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – konfigurera vägar för att få åtkomst till en delad tjänst-VNet med en arbets belastning som du vill att varje VNet och gren ska få åtkomst till.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569259"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenario: dirigera till delade tjänster virtuella nätverk

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot är målet att ställa in vägar för att få åtkomst till en **delad tjänst-** VNet med arbets belastningar som du vill att varje VNet och gren (VPN/er/P2s) ska ha åtkomst till. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

För att konfigurera det här scenariot vidtar du följande steg för att överväga:

1. Identifiera de **delade tjänsternas** VNet.
2. Skapa en anpassad routningstabell. I exemplet refererar vi till den här routningstabellen som **RT_SHARED**. Anvisningar för hur du skapar en routningstabell finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md). Använd följande värden som en rikt linje:

   * **Föreningar**
     * För **virtuella nätverk *utom* de delade tjänsterna VNet**väljer du den virtuella nätverk som ska isoleras. Detta innebär att alla dessa virtuella nätverk (förutom det virtuella nätverket för delade tjänster) kan komma åt målet baserat på vägar i RT_SHARED Route-tabellen.

   * **Spridning**
      * För **grenar**sprider du vägar till den här routningstabellen, förutom andra routningstabeller som du kanske redan har valt. På grund av det här steget kommer RT_SHARED Route-tabellen att lära sig vägar från alla förgrenings anslutningar (VPN/ER/användare VPN).
      * För **virtuella nätverk**väljer du **delade tjänster VNet**. På grund av det här steget kommer RT_SHARED Route-tabellen att lära sig vägar från VNet-anslutningen för delade tjänster.

     Detta leder till att konfigurations konfigurationen ändras så som visas i figuren nedan

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="VNet för delade tjänster":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
