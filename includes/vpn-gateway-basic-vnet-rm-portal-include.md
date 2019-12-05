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
ms.openlocfilehash: 5bf93980a8be86c77240ab981eb812a738a96204
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828800"
---
Du kan skapa en VNet i Resource Manager-distributionsmodellen och Azure-portalen genom att följa stegen nedan. Mer information om virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>För att det här virtuella nätverket ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras trafiken på ett oväntat sätt. Om du dessutom vill ansluta detta VNet till ett annat VNet kan inte adressutrymmet överlappa med andra VNet. Planera din nätverkskonfiguration på lämpligt sätt.
>
>

1. Logga in på [Azure-portalen](https://portal.azure.com).  På Azure Portal-menyn eller på **Start** sidan och välj **skapa en resurs**. Sidan **Nytt** öppnas.

2. I **Sök på Marketplace**anger du *virtuellt nätverk* och väljer **Virtual Network** i resultaten.

   ![Hitta Virtual Network resurs Sidan](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Hitta resurs sidan för virtuella nätverk")

   Sidan **Virtuellt nätverk** öppnas.

3. I listan **Välj en distributionsmodell** nästan längst ned på sidan Virtuellt nätverk väljer du **Resource Manager** och klickar sedan på **Skapa**. Sidan **Skapa virtuellt nätverk** öppnas.

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Sidan Skapa virtuellt nätverk")

4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Vissa värden är automatiskt ifyllda, och du kan ersätta med dina egna värden:

   - **Namn**: Namnge ditt virtuella nätverk.

   - **Adressutrymme**: Ange adressutrymmet. Om du vill lägga till flera adressutrymme anger du ditt första adressutrymme här. Du kan lägga till ytterligare adressutrymmen senare när du har skapat det virtuella nätverket.

   - **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.

   - **Resurs grupp**: Välj en befintlig resurs grupp eller skapa en ny genom att ange ett namn för din nya resurs grupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Plats**: Välj plats för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att vara.

   - **Undernät**: Lägg till under nätets **namn** och under nätets **adress intervall**. Du kan lägga till ytterligare undernät senare när du har skapat det virtuella nätverket. 
     
5. Välj **Skapa**.
