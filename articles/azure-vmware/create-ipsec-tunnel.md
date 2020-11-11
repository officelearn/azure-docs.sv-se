---
title: Skapa en IPSec-tunnel i Azure VMware-lösningen
description: Lär dig hur du skapar en virtuell WAN-hubb för att upprätta en IPSec-tunnel till Azure VMware-lösningar.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 9f869f04bf165f4791f13c626b63257ea98a7ca9
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506455"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Skapa en IPSec-tunnel i Azure VMware-lösningen

I den här artikeln går vi igenom stegen för att upprätta en VPN-anslutning (IPsec IKEv1 och IKEv2) från plats-till-plats-tunneln som avslutas i Microsoft Azure virtuella WAN-hubben. Vi ska skapa en virtuell Azure-hubb och en VPN-gateway med en offentlig IP-adress som är kopplad till den. Sedan skapar vi en Azure ExpressRoute-gateway och upprättar en Azure VMware-lösnings slut punkt. Vi går också igenom detaljerna för att aktivera en principbaserad VPN-installation på plats. 

## <a name="topology"></a>Topologi

![Diagram över VPN för plats-till-plats-tunnel.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Den virtuella Azure-hubben innehåller Azure VMware-lösningen ExpressRoute-gateway och plats-till-plats-VPN-gateway. Den ansluter en lokal VPN-enhet med en Azure VMware-lösnings slut punkt.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill skapa en plats-till-plats-VPN-tunnel måste du skapa en offentlig IP-adress som avslutas på en lokal VPN-enhet.

## <a name="create-a-virtual-wan-hub"></a>Skapa en virtuell WAN-hubb

1. Sök på **virtuell WAN** i Azure Portal. Välj **+Lägg till**. Sidan skapa WAN öppnas.  

2. Ange de obligatoriska fälten på sidan **skapa WAN** och välj sedan **Granska + skapa**.
   
   | Fält | Värde |
   | --- | --- |
   | **Prenumeration** | Värdet är i förväg ifyllt med prenumerationen som tillhör resurs gruppen. |
   | **Resursgrupp** | Det virtuella WAN-nätverket är en global resurs som inte är begränsad till en speciell region.  |
   | **Resursgruppsplats** | Om du vill skapa den virtuella WAN-hubben måste du ange en plats för resurs gruppen.  |
   | **Namn** |   |
   | **Typ** | Välj **standard** , vilket ger mer än bara VPN-gatewayens trafik.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Skärm bild som visar sidan Skapa WAN i Azure Portal.":::

3. I Azure Portal väljer du det virtuella WAN som du skapade i föregående steg, väljer **Skapa virtuell hubb** , anger de obligatoriska fälten och väljer sedan **Nästa: webbplats till plats**. 

   | Fält | Värde |
   | --- | --- |
   | **Region** | Det krävs ett hanterings perspektiv för att välja en region.  |
   | **Namn** |    |
   | **Privat adressutrymme för hubb** | Ange under nätet med ett `/24` (minst).  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Skärm bild som visar sidan Skapa virtuellt nav.":::

4. På fliken **plats-till-plats** definierar du plats-till-plats-gatewayen genom att ställa in det sammanställda data flödet från List rutan för **Gateway-Scale-enheter** . 

   >[!TIP]
   >En skalnings enhet = 500 Mbit/s. Skalnings enheterna finns i par för redundans, varje stödd 500 Mbit/s.
  
5. På fliken **ExpressRoute** skapar du en ExpressRoute-Gateway. 

   >[!TIP]
   >Ett värde för skalnings enhet är 2 Gbit/s. 

    Det tar cirka 30 minuter att skapa varje hubb. 

## <a name="create-a-vpn-site"></a>Skapa en VPN-webbplats 

1. I de **senaste resurserna** i Azure Portal väljer du det virtuella WAN-nätverket som du skapade i föregående avsnitt.

2. I **Översikt** över den virtuella hubben väljer du VPN för **anslutning**  >  **(plats-till-plats)** och väljer sedan **Skapa ny VPN-webbplats**.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Skärm bild av översikts sidan för den virtuella hubben med VPN (plats-till-plats) och skapa en ny VPN-plats vald.":::  
 
3. På fliken **grundläggande** anger du de obligatoriska fälten och väljer sedan **Nästa: länkar**. 

   | Fält | Värde |
   | --- | --- |
   | **Region** | Samma region som du angav i föregående avsnitt.  |
   | **Namn** |  |
   | **Enhets leverantör** |  |
   | **Border Gateway Protocol** | Ställ in på **Aktivera** för att säkerställa att både Azure VMware-lösningen och de lokala servrarna annonserar sina vägar över tunneln. Om inaktive ras måste de undernät som måste annonseras manuellt underhållas. Om undernät saknas kommer HCX inte att kunna bilda tjänst nätet. Mer information finns i  [om BGP med Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Privat adress utrymme**  | Ange det lokala CIDR-blocket.  Den används för att dirigera all trafik som är bindning för lokal över tunneln.  CIDR-blocket krävs endast om du inte aktiverar BGP. |
   | **Anslut till** |   |

4. Fyll i de obligatoriska fälten på fliken **länkar** och välj **Granska + skapa**. Genom att ange namn på länkar och providers kan du skilja mellan hur många gatewayer som kan ha skapats som en del av hubben. BGP och autonomt system nummer (ASN) måste vara unikt i din organisation.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Valfritt Definiera en VPN-plats för principbaserad VPN plats-till-plats-tunnlar

Det här avsnittet gäller endast för principbaserade VPN-nätverk. Principbaserad (eller statiska, vägbaserade) VPN-inställningar drivs av lokala VPN-enheter i de flesta fall. De kräver lokala och Azure VMware-lösnings nätverk som ska anges. För Azure VMware-lösningen med en virtuell Azure-hubb kan du inte välja *något* nätverk. I stället måste du ange alla relevanta lokala och Azure VMware-lösningar virtuella WAN Hub-intervall. Dessa nav intervall används för att ange krypterings domänen för princip bas-slutpunktens lokala VPN-tunnel. Azure VMware-lösnings sidan kräver bara att indikatorn för principbaserad trafik väljs aktive ras. 

1. Gå till den virtuella WAN Hub-platsen i Azure Portal. under **anslutning** väljer du **VPN (plats till plats)**.

2. Välj VPN-platsens namn och sedan tre punkter (...) längst till höger. Välj sedan **Redigera VPN-anslutning till den här hubben**.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Skärm bild av sidan i Azure för den virtuella WAN Hub-platsen som visar att tre punkter har valts för att få åtkomst till redigera VPN-anslutning till hubben." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Redigera anslutningen mellan VPN-platsen och hubben och välj sedan **Spara**.
   - Internet Protocol säkerhet (IPSec) väljer du **anpassad**.
   - Använd principbaserade trafik väljare väljer du **Aktivera**
   - Ange information om **IKE fas 1** och **IKE fas 2 (IPSec)**. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Skärm bild av sidan Redigera VPN-anslutning."::: 
 
    Dina trafik väljare eller undernät som ingår i den principbaserad krypterings domänen ska vara:
    
    - Den virtuella WAN-hubben/24
    - Azure VMware-lösningen privat moln/22
    - Det anslutna virtuella Azure-nätverket (om det finns)

## <a name="connect-your-vpn-site-to-the-hub"></a>Anslut VPN-platsen till hubben

1. Markera kryss rutan bredvid VPN-webbplatsens namn (se föregående **VPN-webbplats till plats** skärm bild) och välj sedan **Anslut VPN-platser**. I fältet **I förväg delad nyckel** anger du den nyckel som tidigare definierats för den lokala slut punkten. Om du inte har en tidigare definierad nyckel kan du lämna fältet tomt och en nyckel skapas automatiskt åt dig. 
 
    Aktivera endast **standard väg för spridning** om du distribuerar en brand vägg i hubben och är nästa hopp för anslutningar via tunneln.

    Välj **Anslut**. På skärmen anslutnings status visas statusen för skapandet av tunneln.

2. Gå till översikten över virtuella WAN-nätverk. Öppna sidan VPN-webbplats och ladda ned VPN-konfigurationsfilen för att tillämpa den på den lokala slut punkten.  

3. Nu ska vi uppdatera Azure VMware-ExpressRoute till den virtuella WAN-hubben. (Det här steget kräver att du först skapar ditt privata moln.)

    Gå till avsnittet om **anslutning** i Azure VMware-lösningen privat moln. På fliken **ExpressRoute** väljer du **+ begär en nyckel för autentisering**. Ge den namnet och välj **skapa**. (Det kan ta cirka 30 sekunder att skapa nyckeln.) Kopiera ExpressRoute-ID och verifierings nyckel. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="Skärm bild av anslutnings sidan för det privata molnet, med begär en auktoriseringspost som valts på fliken ExpressRoute.":::

    > [!NOTE]
    > Verifierings nyckeln försvinner efter en stund, så kopiera den så snart den visas.

4. Nu ska vi länka Azure VMware-lösningen och VPN-gatewayen tillsammans i den virtuella WAN-hubben. I Azure Portal öppnar du det virtuella WAN-nätverket som du skapade tidigare. Välj den skapade virtuella WAN-hubben och välj sedan **ExpressRoute** i det vänstra fönstret. Välj **+ Lös in nyckel för autentisering**.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Skärm bild av ExpressRoute-sidan för det privata molnet med en upplösande nyckel för auktorisering valt.":::

    Klistra in auktoriseringsregeln i fältet auktoriseringskod och ExpressRoute-ID i **peer-kretsens URI** -fält. Se till att välja **automatiskt associera den här ExpressRoute-kretsen med hubben.** Välj **Lägg till** för att skapa länken. 

5. Om du vill testa anslutningen [skapar du ett NSX-T-segment](./tutorial-nsx-t-network-segment.md) och etablerar en virtuell dator i nätverket. Testa genom att pinga både lokala och Azure VMware-lösningens slut punkter.
