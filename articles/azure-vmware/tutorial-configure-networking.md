---
title: Självstudie – Konfigurera nätverk för ditt privata VMware-moln i Azure
description: Lär dig att skapa och konfigurera de nätverk som behövs för att distribuera ditt privata moln i Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 5a8086f78f465f52d0f9107932c09c4690f505e8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873852"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Självstudie: Konfigurera nätverk för ditt privata VMWare-moln i Azure

Ett privat moln i Azure VMware-lösningen (AVS) kräver ett virtuellt nätverk. Eftersom AVS inte stöder din lokala vCenter under för hands versionen krävs ytterligare steg för integrering med din lokala miljö. Att konfigurera en ExpressRoute-krets och en Virtual Network Gateway krävs också och kommer att åtgärdas i den här självstudien.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en Virtual Network Gateway
> * Anslut din ExpressRoute-krets till gatewayen
> * Leta upp URL: erna för vCenter och NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Navigera till resurs gruppen som du skapade i [föregående självstudie](tutorial-create-private-cloud.md)och välj **+ Lägg** till för att definiera en ny resurs.

I text rutan **Sök i Marketplace skriver du** **Virtual Network**. Hitta Virtual Network resursen och markera den.

På sidan Virtual Network väljer du **skapa** för att konfigurera ditt virtuella nätverk för ditt privata moln.

På sidan **skapa Virtual Network** anger du relevant information för ditt virtuella nätverk, en beskrivning av egenskaperna visas i följande tabell:

> [!IMPORTANT]
> Du måste använda ett adress utrymme som **inte** överlappar adress utrymmet som du använde när du skapade ditt privata moln i föregående självstudie.

På fliken **grundläggande** anger du ett namn för det virtuella nätverket och väljer lämplig region och väljer **Nästa: IP-adresser**

På fliken **IP-adresser** , under **IPv4-adress utrymme**, anger du det adress utrymme som du skapade i föregående självstudie.

Välj **+ Lägg till undernät**och ge under nätet ett namn och lämpligt adress intervall på sidan **Lägg till undernät** . Välj **Lägg till** när det är klart.

Välj **Granska + skapa**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="skapa ett virtuellt nätverk" border="true":::

Verifiera informationen och välj **skapa**. När distributionen är klar visas ditt virtuella nätverk i resurs gruppen.

## <a name="create-a-virtual-network-gateway"></a>Skapa en Virtual Network Gateway

Du har skapat ett virtuellt nätverk i föregående avsnitt. nu ska du skapa en Virtual Network Gateway.

I resurs gruppen väljer du **+ Lägg** till för att lägga till en ny resurs.

I text rutan **Sök i Marketplace anger du** **virtuell nätverksgateway**. Hitta Virtual Network resursen och markera den.

På sidan **Virtual Network Gateway** väljer du **skapa**.

På fliken grundläggande på sidan **Skapa virtuell nätverksgateway** anger du värden för fälten. beskrivningar av fälten visas i följande tabell:

| Fält | Värde |
| --- | --- |
| **Prenumeration** | Värdet är redan ifyllt med den prenumeration som resurs gruppen tillhör. |
| **Resursgrupp** | Det här värdet är redan ifyllt för den aktuella resurs gruppen. Detta bör vara den resurs grupp som du skapade i ett tidigare test. |
| **Namn** | Ange ett unikt namn för den virtuella Nätverksgatewayen. |
| **Region** | Välj den geografiska platsen för den virtuella Nätverksgatewayen. |
| **Typ av Gateway** | Välj **ExpressRoute**. |
| **VPN-typ** | Välj **Routningsbaserad**. |
| **SKU** | Låt standardvärdet vara **standard:** |
| **Virtuellt nätverk** | Välj det virtuella nätverk som du skapade tidigare. Om du inte ser det virtuella nätverket kontrollerar du att regionen för gatewayen matchar regionen för det virtuella nätverket. |
| **Adress intervall för gateway-undernät** | Det här värdet fylls i när du väljer det virtuella nätverket. Ändra inte standardvärdet. |
| **Offentlig IP-adress** | Välj **Skapa ny**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="skapa en gateway" border="true":::

Välj **Granska + skapa**på nästa sida kontrol lera att informationen är korrekt och välj **skapa** för att starta distributionen av den virtuella Nätverksgatewayen. När distributionen är klar kan du gå vidare till nästa avsnitt i den här självstudien för att ansluta din ExpressRoute-anslutning till det virtuella nätverket som innehåller ditt privata moln.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Anslut ExpressRoute till Virtual Network Gateway

Det här avsnittet vägleder dig genom att lägga till en anslutning mellan ditt moln-och den virtuella nätverksgateway som du har skapat.

Navigera till det privata moln som du skapade i föregående självstudie och välj **anslutning** under **Hantera**, Välj fliken **ExpressRoute** .

Kopiera verifierings nyckeln. Om det inte finns någon autentiseringsregel måste du skapa en, för att göra det väljer du **+ begär en nyckel för autentisering**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="begär en nyckel för autentisering" border="true":::

Navigera till Virtual Network gateway som du skapade i föregående steg och välj **anslutningar**under **Inställningar**. På sidan **anslutningar** väljer du **+ Lägg till**.

På sidan **Lägg till anslutning** anger du värden för fälten. Beskrivningar av fälten visas i följande tabell:

| Fält | Värde |
| --- | --- |
| **Namn**  | Ange ett namn för anslutningen.  |
| **Anslutnings typ**  | Välj **ExpressRoute**.  |
| **Lös in auktorisering**  | Se till att den här rutan är markerad.  |
| **Virtuell nätverksgateway** | Den virtuella Nätverksgatewayen som du skapade tidigare  |
| **Auktoriseringsregel**  | Kopiera och klistra in verifierings nyckeln från fliken ExpressRoute för din resurs grupp. |
| **Peer-krets-URI**  | Kopiera och klistra in ExpressRoute-ID: t från fliken ExpressRoute för din resurs grupp.  |

Välj **OK**. Detta skapar anslutningen mellan din ExpressRoute-krets och ditt virtuella nätverk.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Lägg till en anslutning" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Leta upp URL: erna för vCenter och NSX Manager

För att logga in på vVenter och NSX Manager behöver du URL: erna till vCenter-webbklienten och NSX-T Manager-platsen. Så här hittar du URL: erna:

Navigera till ditt moln privata moln, under **Hantera**, Välj **identitet**, här hittar du den information som behövs.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Hitta vCenter-URL: erna" border="true":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en Virtual Network Gateway
> * Anslut din ExpressRoute-krets till gatewayen
> * Leta upp URL: erna för vCenter och NSX Manager

Fortsätt till nästa självstudie och lär dig hur du skapar en hopp ruta som används för att ansluta till din miljö så att du kan hantera ditt privata moln lokalt.

> [!div class="nextstepaction"]
> [Få åtkomst till privat moln](tutorial-access-private-cloud.md)
