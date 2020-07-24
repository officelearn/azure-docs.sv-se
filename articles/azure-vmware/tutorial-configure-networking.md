---
title: Självstudie – Konfigurera nätverk för ditt privata VMware-moln i Azure
description: Lär dig att skapa och konfigurera de nätverk som behövs för att distribuera ditt privata moln i Azure
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: aa4247f60c3e1ec54bfcde336d1ae8c8f70ff7a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079439"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Självstudie: Konfigurera nätverk för ditt privata VMware-moln i Azure

En Azure VMware-lösning (AVS) ett privat moln kräver en Azure-Virtual Network. Eftersom AVS inte stöder din lokala vCenter under för hands versionen krävs ytterligare steg för integrering med din lokala miljö. Att konfigurera en ExpressRoute-krets och en virtuell nätverksgateway krävs också och beskrivs i den här självstudien.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en virtuell nätverksgateway
> * Anslut din ExpressRoute-krets till gatewayen
> * Leta upp URL: erna för vCenter och NSX Manager

## <a name="prerequisites"></a>Förutsättningar 
Innan du kan skapa ett virtuellt nätverk måste du kontrol lera att du har skapat ett [privat AVS-moln](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Navigera till den resurs grupp som du skapade i [självstudien skapa ett privat moln](tutorial-create-private-cloud.md) och välj **+ Lägg** till för att definiera en ny resurs. 

1. I text rutan **Sök i Marketplace skriver du** **Virtual Network**. Hitta Virtual Network resursen och markera den.

1. På sidan **Virtual Network** väljer du **skapa** för att konfigurera ditt virtuella nätverk för ditt privata moln.

1. På sidan **skapa Virtual Network** anger du information om det virtuella nätverket.

1. På fliken **grundläggande** anger du ett namn för det virtuella nätverket och väljer lämplig region och väljer **Nästa: IP-adresser**.

1. På fliken **IP-adresser** , under **IPv4-adress utrymme**, anger du det adress utrymme som du skapade i föregående självstudie.

   > [!IMPORTANT]
   > Du måste använda ett adress utrymme som **inte** överlappar adress utrymmet som du använde när du skapade ditt privata moln i föregående självstudie.

1. Välj **+ Lägg till undernät**och ge under nätet ett namn och lämpligt adress intervall på sidan **Lägg till undernät** . Välj **Lägg till** när det är klart.

1. Välj **Granska + skapa**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="skapa ett virtuellt nätverk" border="true":::

1. Verifiera informationen och välj **skapa**. När distributionen är klar visas ditt virtuella nätverk i resurs gruppen.

## <a name="create-a-virtual-network-gateway"></a>Skapa en virtuell nätverksgateway

Nu när du har skapat ett virtuellt nätverk skapar du en virtuell nätverksgateway.

1. I resurs gruppen väljer du **+ Lägg** till för att lägga till en ny resurs.

1. I text rutan **Sök i Marketplace anger du** **virtuell nätverksgateway**. Hitta Virtual Network resursen och markera den.

1. På sidan **Virtual Network Gateway** väljer du **skapa**.

1. På fliken grundläggande på sidan **Skapa virtuell nätverksgateway** anger du värden för fälten och väljer sedan **Granska + skapa**. 

   | Fält | Värde |
   | --- | --- |
   | **Prenumeration** | Värdet är redan ifyllt med den prenumeration som resurs gruppen tillhör. |
   | **Resursgrupp** | Det här värdet är redan ifyllt för den aktuella resurs gruppen. Detta bör vara den resurs grupp som du skapade i ett tidigare test. |
   | **Namn** | Ange ett unikt namn för den virtuella Nätverksgatewayen. |
   | **Region** | Välj den geografiska platsen för den virtuella Nätverksgatewayen. |
   | **Typ av Gateway** | Välj **ExpressRoute**. |
   | **SKU** | Låt standardvärdet vara **standard:** |
   | **Virtuellt nätverk** | Välj det virtuella nätverk som du skapade tidigare. Om du inte ser det virtuella nätverket kontrollerar du att regionen för gatewayen matchar regionen för det virtuella nätverket. |
   | **Adress intervall för gateway-undernät** | Det här värdet fylls i när du väljer det virtuella nätverket. Ändra inte standardvärdet. |
   | **Offentlig IP-adress** | Välj **Skapa ny**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="skapa en gateway" border="true":::

1. Kontrol lera att informationen är korrekt och välj **skapa** för att starta distributionen av den virtuella Nätverksgatewayen. 
1. När distributionen är klar går du vidare till nästa avsnitt för att ansluta din ExpressRoute-anslutning till den virtuella Nätverksgatewayen som innehåller ditt molns privata moln.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Anslut ExpressRoute till den virtuella Nätverksgatewayen

Nu när du har distribuerat en virtuell nätverksgateway lägger du till en anslutning mellan den och ditt moln i molnet.

1. Navigera till det privata moln som du skapade i föregående självstudie och välj **anslutning** under **Hantera**, Välj fliken **ExpressRoute** .

1. Kopiera verifierings nyckeln. Om det inte finns någon autentiseringsregel måste du skapa en, för att göra det väljer du **+ begär en nyckel för autentisering**

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="begär en nyckel för autentisering" border="true":::

1. Navigera till Virtual Network gateway som du skapade i föregående steg och välj **anslutningar**under **Inställningar**. På sidan **anslutningar** väljer du **+ Lägg till**.

1. På sidan **Lägg till anslutning** anger du värden för fälten och väljer **OK**. 

   | Fält | Värde |
   | --- | --- |
   | **Namn**  | Ange ett namn för anslutningen.  |
   | **Anslutnings typ**  | Välj **ExpressRoute**.  |
   | **Lös in auktorisering**  | Se till att den här rutan är markerad.  |
   | **Virtuell nätverksgateway** | Virtual Network gateway som du skapade tidigare.  |
   | **Auktoriseringsregel**  | Kopiera och klistra in verifierings nyckeln från fliken ExpressRoute för din resurs grupp. |
   | **Peer-krets-URI**  | Kopiera och klistra in ExpressRoute-ID: t från fliken ExpressRoute för din resurs grupp.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Lägg till en anslutning" border="true":::

Anslutningen mellan din ExpressRoute-krets och din Virtual Network skapas.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Leta upp URL: erna för vCenter och NSX Manager

För att logga in på vCenter och NSX Manager behöver du URL: erna till vCenter-webbklienten och NSX-T Manager-platsen. 

Navigera till ditt moln privata moln, under **Hantera**, Välj **identitet**, här hittar du den information som behövs.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Hitta vCenter-URL: erna" border="true":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en virtuell nätverksgateway
> * Anslut din ExpressRoute-krets till gatewayen
> * Leta upp URL: erna för vCenter och NSX Manager

Fortsätt till nästa självstudie och lär dig hur du skapar en hopp ruta som används för att ansluta till din miljö så att du kan hantera ditt privata moln lokalt.

> [!div class="nextstepaction"]
> [Få åtkomst till privat moln](tutorial-access-private-cloud.md)
