---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301968"
---
Du kan skapa en VNet i Resource Manager-distributionsmodellen och Azure-portalen genom att följa stegen nedan. Mer information om virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>När du använder ett virtuellt nätverk som en del av en lokal arkitektur måste du samordna med den lokala nätverksadministratören för att skapa ett IP-adressintervall som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras trafiken på ett oväntat sätt. Om du vill ansluta det här virtuella nätverket till ett annat virtuellt nätverk kan adressutrymmet inte heller överlappa det andra virtuella nätverket. Planera din nätverkskonfiguration på lämpligt sätt.
>
>

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Skriv *virtuellt nätverk* **i Sökresurser, tjänst och dokument (G+/).**

   ![Hitta resurssida för virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Hitta resurssida för virtuellt nätverk")
1. Välj **Virtuellt nätverk** från **Marketplace-resultaten.**

   ![Välj virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Hitta resurssida för virtuellt nätverk")
1. På sidan **Virtuellt nätverk** väljer du **Skapa**.

   ![sida för virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Välj Skapa")
1. När du har valt **Skapa**öppnas sidan **Skapa virtuellt nätverk.**
1. Konfigurera **Vnet-inställningar** **för projektinformation** och **instansinformation** på fliken Grunderna.

   ![Fliken Grunderna](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Fliken Grundläggande") När du fyller i fälten visas en grön bock när de tecken du anger i fältet valideras. Vissa värden är automatiskt ifyllda, och du kan ersätta med dina egna värden:

   - **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.
   - **Resursgrupp**: Välj en befintlig resursgrupp eller klicka på **Skapa ny** om du vill skapa en ny. Mer information om resursgrupper finns i [översikt över Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Namn**: Ange namnet på det virtuella nätverket.
   - **Region**: Välj plats för ditt virtuella nätverk. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att vara.

1. Konfigurera värdena på fliken **IP-adresser.** De värden som visas i exemplen nedan är för demonstrationsändamål. Justera dessa värden enligt de inställningar du behöver.

   ![Fliken IP-adresser](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Fliken IP-adresser")  
   - **IPv4-adressutrymme:** Som standard skapas ett adressutrymme automatiskt. Du kan klicka på adressutrymmet för att justera det så att det återspeglar dina egna värden. Du kan också lägga till ytterligare adressutrymmen.
   - **IPv6**: Om konfigurationen kräver IPv6-adressutrymme väljer du rutan **Lägg till IPv6-adressutrymme** för att ange den informationen.
   - **Undernät**: Om du använder standardadressutrymmet skapas ett standardundernät automatiskt. Om du ändrar adressutrymmet måste du lägga till ett undernät. Välj **+ Lägg till undernät** om du vill öppna fönstret Lägg till **undernät.** Konfigurera följande inställningar och välj sedan **Lägg till** för att lägga till värdena:
      - **Undernätsnamn**: I det här exemplet döpte vi undernätet till "FrontEnd".
      - **Undernätsadressintervall**: Adressintervallet för det här undernätet.

1. Lämna standardvärdena på fliken **Säkerhet:**

   - **DDos-skydd**: Grundläggande
   - **Brandvägg:** Inaktiverad
1. Välj **Granska + skapa** för att validera inställningarna för virtuella nätverk.
1. När inställningarna har validerats väljer du **Skapa**.
