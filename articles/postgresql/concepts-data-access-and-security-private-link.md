---
title: Privat länk – Azure Database for PostgreSQL-enskild server
description: Lär dig hur en privat länk fungerar för Azure Database for PostgreSQL-enskild server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371689"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Privat länk för Azure Database for PostgreSQL-enskild server

Med privat länk kan du skapa privata slut punkter för Azure Database for PostgreSQL-enskild server och sedan flytta Azure-tjänster i ditt privata Virtual Network (VNet). Den privata slut punkten visar en privat IP-adress som du kan använda för att ansluta till din databas server precis som vilken annan resurs som helst i VNet.

Om du vill ha en lista över PaaS Services som stöder funktionen för privat länk läser du [dokumentationen](https://docs.microsoft.com/azure/private-link/index)om den privata länken. En privat slut punkt är en privat IP-adress inom ett särskilt [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och undernät.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL enskild server stöder Generell användning och minnesoptimerade pris nivåer.

## <a name="data-exfiltration-prevention"></a>Data exfiltrering skydd

Data ex filtrering i Azure Database for PostgreSQL enskild server är när en auktoriserad användare, till exempel en databas administratör, kan extrahera data från ett system och flytta det till en annan plats eller till ett annat system utanför organisationen. Användaren kan till exempel flytta data till ett lagrings konto som ägs av en tredje part.

Tänk dig ett scenario med en användare som kör PGAdmin i en virtuell Azure-dator (VM) som ansluter till en Azure Database for PostgreSQL enskild server som tillhandahålls i västra USA. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slut punkter på Azure Database for PostgreSQL enskild server med hjälp av nätverks åtkomst kontroller.

* Inaktivera all Azure Service-trafik till Azure Database for PostgreSQL enskild server via den offentliga slut punkten genom att ställa in *Tillåt att Azure-tjänsterna* är av. Se till att inga IP-adresser eller intervall får åtkomst till servern antingen via [brand Väggs regler](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) eller [tjänst slut punkter för virtuella nätverk](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Tillåt endast trafik till Azure Database for PostgreSQL enskild server med den virtuella datorns privata IP-adress. Mer information finns i artikeln om [service Endpoint](concepts-data-access-and-security-vnet.md) och [VNet brand Väggs regler.](howto-manage-vnet-using-portal.md)

* På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning genom att använda nätverks säkerhets grupper (NSG: er) och service märken enligt följande

    * Ange en NSG-regel för att tillåta trafik för *service tag = SQL. Västra* USA – endast tillåta anslutning till Azure Database for PostgreSQL enskild server i USA, västra
    * Ange en NSG-regel (med en högre prioritet) för att neka trafik för *service tag = SQL* -neka anslutningar till PostgreSQL Database i alla regioner</br></br>

I slutet av den här installationen kan den virtuella Azure-datorn bara ansluta till Azure Database for PostgreSQL enskild server i regionen USA, västra. Anslutningen är dock inte begränsad till en enda Azure Database for PostgreSQL enskild server. Den virtuella datorn kan fortfarande ansluta till en Azure Database for PostgreSQL enskild server i regionen USA, västra, inklusive de databaser som inte ingår i prenumerationen. Vi har minskat omfattningen av data exfiltrering i scenariot ovan till en speciell region, men vi har inte eliminerat det helt.</br>

Med privat länk kan du nu konfigurera nätverks åtkomst kontroller som NSG: er för att begränsa åtkomsten till den privata slut punkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slut punkter. En skadlig Insider kan bara komma åt den mappade PaaS-resursen (till exempel en Azure Database for PostgreSQL enskild server) och ingen annan resurs.

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När du ansluter till den offentliga slut punkten från lokala datorer måste du lägga till din IP-adress i den IP-baserade brand väggen med hjälp av en brand Väggs regel på server nivå. Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utveckling eller testning av arbets belastningar, är det svårt att hantera i en produktions miljö.

Med privat länk kan du aktivera åtkomst mellan platser till den privata slut punkten med hjälp av [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privat peering eller [VPN-tunnel](https://docs.microsoft.com/azure/vpn-gateway/). De kan sedan inaktivera all åtkomst via offentlig slut punkt och inte använda den IP-baserade brand väggen.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Konfigurera privat länk för Azure Database for PostgreSQL enskild server

### <a name="creation-process"></a>Skapande process

Privata slut punkter krävs för att aktivera privat länk. Detta kan göras med hjälp av följande instruktions guider.

* [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Godkännande process
När nätverks administratören skapar den privata slut punkten (PE) kan PostgreSQL-administratören hantera den privata slut punkts anslutningen (PEC) för att Azure Database for PostgreSQL. Denna uppdelning av uppgifter mellan nätverks administratören och DBA är användbar för hantering av den Azure Database for PostgreSQL anslutningen. 

* Navigera till Azure Database for PostgreSQL Server-resursen i Azure Portal. 
    * Välj anslutningar för privata slut punkter i det vänstra fönstret
    * Visar en lista över alla anslutningar för privata slut punkter (PECs)
    * Motsvarande privata slut punkt (PE) har skapats

![Välj den privata slut punkts portalen](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Välj en enskild PEC från listan genom att markera den.

![Välj den privata slut punkten som väntar på godkännande](media/concepts-data-access-and-security-private-link/select-private-link.png)

* PostgreSQL-Server administratören kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort text svar.

![Välj meddelandet privat slut punkt](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Efter godkännande eller avvisande visar listan lämpligt tillstånd tillsammans med svars texten

![Välj slut tillstånd för privat slut punkt](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Använd fall av privat länk för Azure Database for PostgreSQL

Klienter kan ansluta till den privata slut punkten från samma VNet, peer-kopplat VNet i samma region eller via VNet-till-VNet-anslutning mellan regioner. Dessutom kan klienter ansluta lokalt med ExpressRoute, privat peering eller VPN-tunnlar. Nedan visas ett förenklat diagram som visar vanliga användnings fall.

![Välj översikt över privat slut punkt](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peer-Virtual Network (VNet)
Konfigurera [VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) för att upprätta anslutning till den Azure Database for PostgreSQL-enskilda servern från en virtuell Azure-dator i ett peer-kopplat VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö
Konfigurera [VPN gateway-anslutning mellan virtuella](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) nätverk för att upprätta anslutning till en Azure Database for PostgreSQL-enskild server från en virtuell Azure-dator i en annan region eller prenumeration.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN
Om du vill upprätta en anslutning från en lokal miljö till den Azure Database for PostgreSQL-enskilda servern väljer du och implementerar något av alternativen:

* [Punkt-till-plats-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Plats-till-plats-anslutning via VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-krets](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brand Väggs regler

Följande situationer och resultat är möjliga när du använder en privat länk i kombination med brand Väggs regler:

* Om du inte konfigurerar några brand Väggs regler kommer ingen trafik att kunna komma åt den Azure Database for PostgreSQL enskilda servern som standard.

* Om du konfigurerar offentlig trafik eller en tjänst slut punkt och skapar privata slut punkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel.

* Om du inte konfigurerar någon offentlig trafik eller tjänst slut punkt och du skapar privata slut punkter är Azure Database for PostgreSQL enskild server bara tillgänglig via de privata slut punkterna. Om du inte konfigurerar offentlig trafik eller en tjänst slut punkt efter att alla godkända privata slut punkter har avvisats eller tagits bort, kommer ingen trafik att kunna komma åt Azure Database for PostgreSQL enskild server.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Neka offentlig åtkomst för Azure Database for PostgreSQL enskild server

Om du bara vill använda privata slut punkter för att komma åt sin Azure Database for PostgreSQL enskild server, kan du inaktivera inställningen alla offentliga slut punkter ([brand Väggs regler](concepts-firewall-rules.md) och virtuella nätverkets [slut punkter](concepts-data-access-and-security-vnet.md)) genom att ange den **nekade konfigurationen för offentlig nätverks åtkomst** på databas servern. 

När den här inställningen är inställd på *Ja* tillåts bara anslutningar via privata slut punkter till din Azure Database for PostgreSQL. När den här inställningen är inställd på att *inga* klienter ska kunna ansluta till din Azure Database for PostgreSQL baserat på inställningen för brand väggen eller VNet-tjänstens slut punkt. När värdet för åtkomst till det privata nätverket är inställt på kunder kan de dessutom inte lägga till och/eller uppdatera befintliga brand Väggs regler och VNet-tjänstens slut punkts regel

> [!Note]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL-enskild server stöder Generell användning och minnesoptimerade pris nivåer.
>
> Den här inställningen påverkar inte SSL-och TLS-konfigurationer för din Azure Database for PostgreSQL enskild server.

Information om hur du ställer in **neka offentlig nätverks åtkomst** för Azure Database for PostgreSQL enskild server från Azure Portal finns i [så här konfigurerar du neka offentlig nätverks åtkomst](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Database for PostgreSQL funktioner för enskild server-säkerhet finns i följande artiklar:

* Information om hur du konfigurerar en brand vägg för Azure Database for PostgreSQL enskild server finns i [brand Väggs stöd](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules).

* Information om hur du konfigurerar en tjänst slut punkt för virtuella nätverk för din Azure Database for PostgreSQL enskild server finns i [Konfigurera åtkomst från virtuella nätverk](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* En översikt över Azure Database for PostgreSQL enskild server-anslutning finns i [Azure Database for PostgreSQL anslutnings arkitektur](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture)