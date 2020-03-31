---
title: Privat länk - Azure-databas för MySQL
description: Lär dig hur privat länk fungerar för Azure Database för MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371298"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Privat länk för Azure-databas för MySQL

Med Private Link kan du ansluta till olika PaaS-tjänster i Azure via en privat slutpunkt. Azure Private Link ger i huvudsak Azure-tjänster i ditt privata virtuella nätverk (VNet). PaaS-resurserna kan nås med hjälp av den privata IP-adressen precis som alla andra resurser i det virtuella nätverket.

En lista till PaaS-tjänster som stöder funktionen Privat länk finns i [dokumentationen](https://docs.microsoft.com/azure/private-link/index)för Privat länk . En privat slutpunkt är en privat IP-adress i ett visst [virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och undernät.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for MySQL stöder prisnivåer för allmänt ändamål och minne.

## <a name="data-exfiltration-prevention"></a>Förebyggande av dataexfiltration

Data ex-filtrering i Azure Database för MySQL är när en behörig användare, till exempel en databasadministratör, kan extrahera data från ett system och flytta den till en annan plats eller ett annat system utanför organisationen. Användaren flyttar till exempel data till ett lagringskonto som ägs av en tredje part.

Överväg ett scenario med en användare som kör MySQL Workbench i en virtuell Azure-dator (VM) som ansluter till en Azure-databas för MySQL-server som etablerats i västra USA. Exemplet nedan visar hur du begränsar åtkomst med offentliga slutpunkter i Azure Database för MySQL med hjälp av nätverksåtkomstkontroller.

* Inaktivera all Azure-tjänsttrafik till Azure Database för MySQL via den offentliga slutpunkten genom att ange *Tillåt Azure Services* till OFF. Kontrollera att inga IP-adresser eller intervall tillåts komma åt servern antingen via [brandväggsregler](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) eller [slutpunkter för virtuella nätverkstjänster](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Tillåt endast trafik till Azure-databasen för MySQL med den privata IP-adressen för den virtuella datorn. Mer information finns i artiklarna om [serviceslutpunkts-](concepts-data-access-and-security-vnet.md) och [VNet-brandväggsregler](howto-manage-vnet-using-portal.md).

* På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning med hjälp av NSG-grupper (Network Security Groups) och Service Tags enligt följande

    * Ange en NSG-regel för att tillåta trafik för *Service Tag = SQL. WestUs* - tillåter endast anslutning till Azure Database för MySQL i västra USA
    * Ange en NSG-regel (med högre prioritet) för att neka trafik för *Service Tag = SQL* - neka anslutningar till Uppdatera till Azure Database för MySQL i alla regioner</br></br>

I slutet av den här installationen kan Azure VM endast ansluta till Azure Database för MySQL i regionen Västra USA. Anslutningen är dock inte begränsad till en enda Azure-databas för MySQL. Den virtuella datorn kan fortfarande ansluta till valfri Azure-databas för MySQL i regionen västra USA, inklusive de databaser som inte ingår i prenumerationen. Även om vi har minskat omfattningen av dataexfiltration i ovanstående scenario till en viss region, har vi inte eliminerat det helt och hållet.</br>

Med Private Link kan du nu ställa in nätverksåtkomstkontroller som NSG:er för att begränsa åtkomsten till den privata slutpunkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slutpunkter. En skadlig insider kan bara komma åt den mappade PaaS-resursen (till exempel en Azure-databas för MySQL) och ingen annan resurs.

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När du ansluter till den offentliga slutpunkten från lokala datorer måste din IP-adress läggas till i den IP-baserade brandväggen med hjälp av en brandväggsregel på servernivå. Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utvecklings- eller testarbetsbelastningar, är det svårt att hantera i en produktionsmiljö.

Med Privat länk kan du aktivera åtkomst mellan lokala och lokala resurser till den privata slutpunkten med [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), privat peering eller [VPN-tunnel](https://docs.microsoft.com/azure/vpn-gateway/). De kan därefter inaktivera all åtkomst via offentlig slutpunkt och inte använda den IP-baserade brandväggen.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Konfigurera privat länk för Azure-databas för MySQL

### <a name="creation-process"></a>Skapandeprocess

Privata slutpunkter krävs för att aktivera Privat länk. Detta kan göras med hjälp av följande how-to guider.

* [Azure-portal](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Godkännandeprocess
När nätverksadministratören har skapat den privata slutpunkten (PE) kan MySQL-administratören hantera den privata slutpunktsanslutningen (PEC) till Azure Database for MySQL. Den här åtskillnaden mellan nätverksadministratören och DBA är användbar för hantering av Azure-databasen för MySQL-anslutning. 

* Navigera till Azure Database for MySQL-serverresursen i Azure-portalen. 
    * Markera de privata slutpunktsanslutningarna i den vänstra rutan
    * Visar en lista över alla privata slutpunktsanslutningar (PECs)
    * Motsvarande privat slutpunkt (PE) skapad

![välja den privata slutpunktsportalen](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Välj en enskild PEC i listan genom att välja den.

![välja den privata slutpunkten som väntar på godkännande](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MySQL-serveradministratören kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort textsvar.

![markera det privata slutpunktsmeddelandet](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Efter godkännande eller avslag kommer listan att återspegla rätt tillstånd tillsammans med svarstexten

![välja det privata slutpunktssluttillståndet](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Använda fall av privat länk för Azure-databas för MySQL

Klienter kan ansluta till den privata slutpunkten från samma virtuella nätverk, peered VNet i samma region eller via VNet-till-VNet-anslutning mellan regioner. Dessutom kan klienter ansluta från lokala med ExpressRoute, privat peering eller VPN-tunnel. Nedan finns ett förenklat diagram som visar de vanliga användningsfallen.

![välja den privata slutpunktsöversikten](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peered virtual network (VNet)
Konfigurera [VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) för att upprätta anslutning till Azure Database for MySQL från en Azure VM i ett peer-virtuellt virtuellt nätverk.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö
Konfigurera [VNet-till-VNet VPN-gateway-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) för att upprätta anslutning till en Azure-databas för MySQL från en Azure VM i en annan region eller prenumeration.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN
Om du vill upprätta anslutning från en lokal miljö till Azure-databasen för MySQL väljer och implementerar du ett av alternativen:

* [Anslutning mellan punkt och plats](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Plats-till-plats-anslutning via VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-krets](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brandväggsregler

Följande situationer och resultat är möjliga när du använder Private Link i kombination med brandväggsregler:

* Om du inte konfigurerar några brandväggsregler kan ingen trafik som standard komma åt Azure-databasen för MySQL.

* Om du konfigurerar offentlig trafik eller en tjänstslutpunkt och skapar privata slutpunkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brandväggsregel.

* Om du inte konfigurerar någon offentlig trafik- eller tjänstslutpunkt och du skapar privata slutpunkter är Azure-databasen för MySQL endast tillgänglig via de privata slutpunkterna. Om du inte konfigurerar offentlig trafik eller en tjänstslutpunkt, när alla godkända privata slutpunkter har avvisats eller tagits bort, kommer ingen trafik att kunna komma åt Azure-databasen för MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Neka offentlig åtkomst för Azure Database för MySQL

Om du bara vill förlita dig på privata slutpunkter för åtkomst till deras Azure-databas för MySQL kan du inaktivera inställningen av alla offentliga slutpunkter (d.v.d. [brandväggsregler](concepts-firewall-rules.md) och [slutpunkter för VNet-tjänst)](concepts-data-access-and-security-vnet.md)genom att ange konfigurationen **Neka offentligt nätverksåtkomst** på databasservern. 

När den här inställningen är inställd på *JA*tillåts endast anslutningar via privata slutpunkter till din Azure-databas för MySQL. När den här inställningen är inställd på *NO*kan klienter ansluta till din Azure-databas för MySQL baserat på dina slutpunktsinställningar för brandväggen eller VNet-tjänsten. När värdet för den privata nätverksåtkomsten har angetts kan du dessutom inte lägga till och/eller uppdatera befintliga brandväggs- och VNet-tjänstslutpunktsregler.

> [!Note]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL - Single server stöder prisnivåer för allmänt ändamål och minne.
>
> Den här inställningen påverkar inte SSL- och TLS-konfigurationerna för din Azure-databas för MySQL.

Mer information om hur du anger **neka offentlig nätverksåtkomst** för din Azure-databas för MySQL från Azure-portalen finns i [Så här konfigurerar](howto-deny-public-network-access.md)du Neka offentlig åtkomst .

## <a name="next-steps"></a>Nästa steg

Mer information om säkerhetsfunktioner i Azure Database for MySQL finns i följande artiklar:

* Information om hur du konfigurerar en brandvägg för Azure Database for MySQL finns i [Brandväggsstöd](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Mer information om hur du konfigurerar en slutpunkt för en virtuell nätverkstjänst för din Azure-databas för MySQL finns i [Konfigurera åtkomst från virtuella nätverk](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* En översikt över Azure Database för MySQL-anslutning finns i [Azure Database for MySQL Connectivity Architecture](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
