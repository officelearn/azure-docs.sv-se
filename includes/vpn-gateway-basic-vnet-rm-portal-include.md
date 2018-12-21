---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109236"
---
Du kan skapa en VNet i Resource Manager-distributionsmodellen och Azure-portalen genom att följa stegen nedan. Mer information om virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>För att det här virtuella nätverket ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras trafiken på ett oväntat sätt. Om du dessutom vill ansluta detta VNet till ett annat VNet kan inte adressutrymmet överlappa med andra VNet. Planera din nätverkskonfiguration på lämpligt sätt.
>
>

1. Logga in på [Azure-portalen](http://portal.azure.com) och välj **Skapa en resurs**. Sidan **Nytt** öppnas.

2. I fältet **Sök på Marketplace** anger du *virtuellt nätverk* och väljer **Virtuellt nätverk** i listan som returneras. Sidan **Virtuellt nätverk** öppnas.

   ![Sida för att leta upp virtuell nätverksresurs](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Sida för att leta upp virtuell nätverksresurs")

3. I listan **Välj en distributionsmodell** nästan längst ned på sidan Virtuellt nätverk väljer du **Resource Manager** och klickar sedan på **Skapa**. Sidan **Skapa virtuellt nätverk** öppnas.

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Sidan Skapa virtuellt nätverk")

4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Vissa värden är automatiskt ifyllda, och du kan ersätta med dina egna värden:

   - **Namn**: Namnge ditt virtuella nätverk.

   - **Adressutrymme**: Ange adressutrymmet. Om du vill lägga till flera adressutrymme anger du ditt första adressutrymme här. Du kan lägga till ytterligare adressutrymmen senare när du har skapat det virtuella nätverket.

   - **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.

   - **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny genom att skriva ett namn för en ny resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Plats**: Välj plats för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att vara.

   - **Undernät**: Lägg till **undernätsnamn** och **adressintervall** i undernätet. Du kan lägga till ytterligare undernät senare när du har skapat det virtuella nätverket. 
     
5. Välj **Skapa**.
