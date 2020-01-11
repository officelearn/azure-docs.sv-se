---
title: Privat länk för Azure Database for MySQL (förhands granskning)
description: Lär dig hur en privat länk fungerar för Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d9738a1dca39e1b43f690bd65ff05d20b6a94fa1
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897512"
---
# <a name="private-link-for-azure-database-for-mysql-preview"></a>Privat länk för Azure Database for MySQL (förhands granskning)

Med privat länk kan du ansluta till olika PaaS-tjänster i Azure via en privat slut punkt. Azures privata länk placerar Azure-tjänster i ditt privata Virtual Network (VNet). PaaS-resurser kan nås med hjälp av den privata IP-adressen precis som vilken annan resurs som helst i VNet.

Om du vill ha en lista över PaaS Services som stöder funktionen för privat länk läser du [dokumentationen](https://docs.microsoft.com/azure/private-link/index)om den privata länken. En privat slut punkt är en privat IP-adress inom ett särskilt [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och undernät.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for MySQL stöder Generell användning och minnesoptimerade pris nivåer.

## <a name="data-exfiltration-prevention"></a>Data exfiltrering skydd

Data vid filtrering i Azure Database for MySQL är när en behörig användare, till exempel en databas administratör, kan extrahera data från ett system och flytta det till en annan plats eller ett system utanför organisationen. Användaren kan till exempel flytta data till ett lagrings konto som ägs av en tredje part.

Överväg ett scenario med en användare som kör MySQL Workbench i en virtuell Azure-dator (VM) som ansluter till en Azure Database for MySQL server som har skapats i västra USA. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slut punkter på Azure Database for MySQL med hjälp av nätverks åtkomst kontroller.

* Inaktivera all Azure Service-trafik för att Azure Database for MySQL via den offentliga slut punkten genom att ställa in *Tillåt att Azure-tjänster* stängs av. Se till att inga IP-adresser eller intervall får åtkomst till servern antingen via [brand Väggs regler](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) eller [tjänst slut punkter för virtuella nätverk](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Tillåt endast trafik till Azure Database for MySQL med den virtuella datorns privata IP-adress. Mer information finns i artikeln om [service Endpoint](concepts-data-access-and-security-vnet.md) och [VNet brand Väggs regler](howto-manage-vnet-using-portal.md).

* På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning genom att använda nätverks säkerhets grupper (NSG: er) och service märken enligt följande

    * Ange en NSG-regel för att tillåta trafik för *service tag = SQL. Västra* USA – endast tillåta anslutning till Azure Database for mysql i västra USA
    * Ange en NSG-regel (med en högre prioritet) för att neka trafik för *service tag = SQL* -neka anslutningar att uppdatera till Azure Database for mysql i alla regioner</br></br>

I slutet av den här installationen kan den virtuella Azure-datorn bara ansluta till Azure Database for MySQL i regionen USA, västra. Anslutningen är dock inte begränsad till en enda Azure Database for MySQL. Den virtuella datorn kan fortfarande ansluta till en Azure Database for MySQL i regionen USA, västra, inklusive de databaser som inte ingår i prenumerationen. Vi har minskat omfattningen av data exfiltrering i scenariot ovan till en speciell region, men vi har inte eliminerat det helt.</br>

Med privat länk kan du nu konfigurera nätverks åtkomst kontroller som NSG: er för att begränsa åtkomsten till den privata slut punkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slut punkter. En skadlig Insider kan bara komma åt den mappade PaaS-resursen (till exempel en Azure Database for MySQL) och ingen annan resurs.

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När du ansluter till den offentliga slut punkten från lokala datorer måste du lägga till din IP-adress i den IP-baserade brand väggen med hjälp av en brand Väggs regel på server nivå. Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utveckling eller testning av arbets belastningar, är det svårt att hantera i en produktions miljö.

Med privat länk kan du aktivera åtkomst mellan platser till den privata slut punkten med hjälp av [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privat peering eller [VPN-tunnel](https://docs.microsoft.com/azure/vpn-gateway/). De kan sedan inaktivera all åtkomst via offentlig slut punkt och inte använda den IP-baserade brand väggen.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Konfigurera privat länk för Azure Database for MySQL

### <a name="creation-process"></a>Skapande process

Privata slut punkter krävs för att aktivera privat länk. Detta kan göras med hjälp av följande instruktions guider.

* [Azure-portalen](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Godkännande process
När nätverks administratören skapar den privata slut punkten (PE) kan MySQL-administratören hantera den privata slut punkts anslutningen (PEC) för att Azure Database for MySQL.

> [!NOTE]
> Azure Database for MySQL stöder för närvarande automatiskt godkännande för den privata slut punkten.

* Navigera till Azure Database for MySQL server-resursen i Azure Portal. 
    * Välj anslutningar för privata slut punkter i det vänstra fönstret
    * Visar en lista över alla anslutningar för privata slut punkter (PECs)
    * Motsvarande privata slut punkt (PE) har skapats

![Välj den privata slut punkts portalen](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Välj en enskild PEC från listan genom att markera den.

![Välj den privata slut punkten som väntar på godkännande](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MySQL-serverns administratör kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort text svar.

![Välj meddelandet privat slut punkt](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Efter godkännande eller avvisande visar listan lämpligt tillstånd tillsammans med svars texten

![Välj slut tillstånd för privat slut punkt](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Använd fall av privat länk för Azure Database for MySQL

Klienter kan ansluta till den privata slut punkten från samma VNet, peer-kopplat VNet i samma region eller via VNet-till-VNet-anslutning mellan regioner. Dessutom kan klienter ansluta lokalt med ExpressRoute, privat peering eller VPN-tunnlar. Nedan visas ett förenklat diagram som visar vanliga användnings fall.

![Välj översikt över privat slut punkt](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peer-Virtual Network (VNet)
Konfigurera [VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) för att upprätta anslutning till Azure Database for MySQL från en virtuell Azure-dator i ett peer-kopplat VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö
Konfigurera [VPN gateway-anslutning mellan virtuella](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) nätverk för att upprätta anslutning till en Azure Database for MySQL från en virtuell Azure-dator i en annan region eller prenumeration.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN
Om du vill upprätta en anslutning från en lokal miljö till Azure Database for MySQL väljer du och implementerar något av alternativen:

* [Punkt-till-plats-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Plats-till-plats-VPN-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-krets](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brand Väggs regler

Följande situationer och resultat är möjliga när du använder en privat länk i kombination med brand Väggs regler:

* Om du inte konfigurerar några brand Väggs regler kommer ingen trafik att kunna komma åt Azure Database for MySQL.

* Om du konfigurerar offentlig trafik eller en tjänst slut punkt och skapar privata slut punkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel.

* Om du inte konfigurerar någon offentlig trafik eller tjänst slut punkt och du skapar privata slut punkter kan Azure Database for MySQL endast nås via de privata slut punkterna. Om du inte konfigurerar offentlig trafik eller en tjänst slut punkt efter att alla godkända privata slut punkter har avvisats eller tagits bort, kommer ingen trafik att kunna komma åt Azure Database for MySQL.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Database for MySQL säkerhetsfunktioner finns i följande artiklar:

* Information om hur du konfigurerar en brand vägg för Azure Database for MySQL finns i [brand Väggs stöd](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Information om hur du konfigurerar en tjänst slut punkt för ett virtuellt nätverk för Azure Database for MySQL finns i [Konfigurera åtkomst från virtuella nätverk](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* En översikt över Azure Database for MySQL-anslutningen finns i [Azure Database for MySQL anslutnings arkitektur](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
