---
title: Så här skapar och hanterar du DHCP
description: Den här artikeln beskriver hur du hanterar DHCP i Azure VMware-lösningen.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461064"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Så här skapar och hanterar du DHCP i Azure VMware-lösning

NSX – T ger möjlighet att konfigurera DHCP för ditt privata moln. Om du använder NSX-T för att vara värd för DHCP-servern, se [skapa DHCP-server](#create-dhcp-server). Annars, om du har en extern DHCP-server från tredje part i nätverket, se [skapa DHCP Relay service](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Skapa DHCP-server

Använd följande steg för att konfigurera en DHCP-server på NSX-T.

1. I NSX Manager går du till fliken **nätverk** och väljer **DHCP**. 
1. Välj **Lägg till Server** och ange sedan Server namn och IP-adress. 
1. Välj **Spara**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Lägg till DHCP-server" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Anslut DHCP-server till nivån-1 Gateway.

1. Välj **nivå 1-gatewayer**, gatewayen i listan och välj sedan **Redigera**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Lägg till DHCP-server" border="true":::

1. Välj **ingen IP-allokerad uppsättning** för att lägga till ett undernät.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Lägg till DHCP-server" border="true":::

1. Välj den **lokala DHCP-servern** som **typ**. 
1. Välj **standard DHCP** för **DHCP-servern** och välj sedan **Spara**.


1. I fönstret **nivå-1-Gateway** väljer du **Spara**. 
1. Välj **Stäng redigering** för att slutföra.

### <a name="add-a-network-segment"></a>Lägg till ett nätverks segment

När du har skapat din DHCP-server måste du lägga till nätverks segment i den.

1. I NSX-T väljer du fliken **nätverk** och väljer **segment** under **anslutning**. 
1. Välj **Lägg till segment** och namnge segmentet och anslutningen till nivån-1 Gateway. 
1. Konfigurera ett nytt undernät genom att välja **Ange undernät** . 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Lägg till DHCP-server" border="true":::

1. I fönstret **Ange undernät** väljer du **Lägg till undernät**. 
1. Ange IP-adressen för gatewayen och DHCP-intervallet och välj **Lägg till** och sedan **tillämpa**

1. Välj **Spara** för att lägga till det nya nätverks segmentet.

## <a name="create-dhcp-relay-service"></a>Skapa DHCP Relay service

1. Välj fliken **nätverk** och välj **DHCP**under **IP-hantering**. 
1. Välj **Lägg till Server**. 
1. Välj DHCP-relä för **Server typ** och ange Server namn och IP-adress för relä servern. 
1. Välj **Spara**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Lägg till DHCP-server" border="true":::

1. Välj **nivå 1 Gateway** under **anslutning**. 
1. Välj den lodräta ellipsen på nivå 1-gatewayen och välj **Redigera**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Lägg till DHCP-server" border="true":::

1. Välj **ingen IP-allokerad uppsättning** för att definiera IP-adressallokering.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Lägg till DHCP-server" border="true":::

1. Välj **DHCP Relay server** som **typ**.
1. Välj DHCP Relay server för **DHCP-relä**. 
1. Välj **Spara**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Ange ett IP-adressintervall i ett segment

> [!NOTE]
> Den här konfigurationen krävs för att realisera DHCP-relä i DHCP-klientcertifikatet. 

1. Under **anslutning**väljer du **segment**. 
1. Markera de lodräta ellipserna och välj **Redigera**. 

   >[!TIP]
   >Om du vill lägga till ett nytt segment väljer du **Lägg till segment**.

1. Lägg till information om segmentet. 
1. Välj värdet under **Ange undernät** för att lägga till eller ändra under nätet.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Lägg till DHCP-server" border="true":::

1. Markera de lodräta ellipserna och välj **Redigera**. Om du behöver skapa ett nytt undernät väljer du **Lägg till undernät** för att skapa en gateway och konfigurera ett DHCP-intervall. 
1. Ange intervallet för IP-adresspoolen och välj **Verkställ**och välj sedan **Spara**

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Lägg till DHCP-server" border="true":::

   En DHCP-server tilldelas till segmentet.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Lägg till DHCP-server" border="true":::
