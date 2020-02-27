---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619736"
---
Du kan skapa en VNet i Resource Manager-distributionsmodellen och Azure-portalen genom att följa stegen nedan. Mer information om virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>För att det här virtuella nätverket ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras trafiken på ett oväntat sätt. Om du dessutom vill ansluta detta VNet till ett annat VNet kan inte adressutrymmet överlappa med andra VNet. Planera din nätverkskonfiguration på lämpligt sätt.
>
>

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I **Sök efter resurser, tjänst och dokument (G +/)** skriver du *virtuellt nätverk*.

   ![Hitta Virtual Network resurs Sidan](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Hitta resurs sidan för virtuella nätverk")
1. Välj **Virtual Network** från **Marketplace** -resultatet.

   ![Välj virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Hitta resurs sidan för virtuella nätverk")
1. På sidan **Virtual Network** klickar du på **skapa**.

   ![Sidan virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Klicka på Skapa")
1. När du klickar på Skapa öppnas sidan **Skapa virtuellt nätverk** .

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Sidan Skapa virtuellt nätverk")
1. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Vissa värden är automatiskt ifyllda, och du kan ersätta med dina egna värden:

   - **Namn**: Namnge ditt virtuella nätverk.
   - **Adressutrymme**: Ange adressutrymmet. Om du vill lägga till flera adressutrymme anger du ditt första adressutrymme här. Du kan lägga till ytterligare adressutrymmen senare när du har skapat det virtuella nätverket. Om konfigurationen kräver IPv6-adress utrymme markerar du kryss rutan för att ange den informationen.
   - **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.
   - **Resurs grupp**: Välj en befintlig resurs grupp eller skapa en ny genom att ange ett namn för din nya resurs grupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Plats**: Välj plats för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att vara.
   - **Undernät**: Lägg till under nätets **namn** och under nätets **adress intervall**. Du kan lägga till ytterligare undernät senare när du har skapat det virtuella nätverket.
   - **DDoS-skydd**: Välj **Basic**, om du inte vill använda standard tjänsten.
   - **Tjänst slut punkter**: du kan lämna den här inställningen **inaktive rad**, om inte konfigurationen anger den här inställningen.
   - **Brand vägg**: du kan lämna den här inställningen **inaktive rad**, om inte konfigurationen anger den här inställningen.
1. Klicka på **skapa** för att starta distributionen av det virtuella nätverket.
