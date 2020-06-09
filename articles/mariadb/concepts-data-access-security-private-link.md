---
title: Privat länk – Azure Database for MariaDB
description: Lär dig hur en privat länk fungerar för Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6f2043b91f8345a638d6fc773230cd182fb0fead
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84508853"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Privat länk för Azure Database for MariaDB

Med privat länk kan du skapa privata slut punkter för Azure Database for MariaDB och sedan flytta Azure-tjänster i ditt privata Virtual Network (VNet). Den privata slut punkten visar en privat IP-adress som du kan använda för att ansluta till din Azure Database for MariaDB databas server precis som vilken annan resurs som helst i VNet.

Om du vill ha en lista över PaaS Services som stöder funktionen för privat länk läser du [dokumentationen](https://docs.microsoft.com/azure/private-link/index)om den privata länken. En privat slut punkt är en privat IP-adress inom ett särskilt [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och undernät.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for MariaDB stöder Generell användning och minnesoptimerade pris nivåer.

## <a name="data-exfiltration-prevention"></a>Data exfiltrering skydd

Data vid filtrering i Azure Database for MariaDB är när en behörig användare, till exempel en databas administratör, kan extrahera data från ett system och flytta det till en annan plats eller ett system utanför organisationen. Användaren kan till exempel flytta data till ett lagrings konto som ägs av en tredje part.

Överväg ett scenario med en användare som kör MariaDB Workbench i en virtuell Azure-dator som ansluter till en Azure Database for MariaDB-instans. Den här MariaDB-instansen är i data centret västra USA. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slut punkter på Azure Database for MariaDB med hjälp av nätverks åtkomst kontroller.

* Inaktivera all Azure Service-trafik för att Azure Database for MariaDB via den offentliga slut punkten genom att ställa in Tillåt att Azure-tjänster STÄNGs av. Se till att inga IP-adresser eller intervall får åtkomst till servern antingen via [brand Väggs regler](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) eller [tjänst slut punkter för virtuella nätverk](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet).

* Tillåt endast trafik till Azure Database for MariaDB med den virtuella datorns privata IP-adress. Mer information finns i artikeln om [service Endpoint](concepts-data-access-security-vnet.md) och [VNet brand Väggs regler](howto-manage-vnet-portal.md).

* På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning genom att använda nätverks säkerhets grupper (NSG: er) och service märken enligt följande:

    * Ange en NSG-regel för att tillåta trafik för service tag = SQL. Västra USA – endast tillåta anslutning till Azure Database for MariaDB i västra USA
    * Ange en NSG-regel (med en högre prioritet) för att neka trafik för service tag = SQL-neka anslutningar till MariaDB Database i alla regioner</br></br>

I slutet av den här installationen kan den virtuella Azure-datorn bara ansluta till Azure Database for MariaDB i regionen USA, västra. Anslutningen är dock inte begränsad till en enda Azure Database for MariaDB. Den virtuella datorn kan fortfarande ansluta till en Azure Database for MariaDB i regionen USA, västra, inklusive de databaser som inte ingår i prenumerationen. Vi har minskat omfattningen av data exfiltrering i scenariot ovan till en speciell region, men vi har inte eliminerat det helt.</br>

Med privat länk kan du nu konfigurera nätverks åtkomst kontroller som NSG: er för att begränsa åtkomsten till den privata slut punkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slut punkter. En skadlig Insider kan bara komma åt den mappade PaaS-resursen (till exempel en Azure Database for MariaDB) och ingen annan resurs.

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När du ansluter till den offentliga slut punkten från lokala datorer måste du lägga till din IP-adress i den IP-baserade brand väggen med hjälp av en brand Väggs regel på server nivå. Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utveckling eller testning av arbets belastningar, är det svårt att hantera i en produktions miljö.

Med privat länk kan du aktivera åtkomst mellan platser till den privata slut punkten med hjälp av [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privat peering eller [VPN-tunnel](https://docs.microsoft.com/azure/vpn-gateway/). De kan sedan inaktivera all åtkomst via offentlig slut punkt och inte använda den IP-baserade brand väggen.

> [!NOTE]
> I vissa fall finns Azure Database for MariaDB och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Se till att båda prenumerations resurs leverantören för **Microsoft. DBforMariaDB** är registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Konfigurera privat länk för Azure Database for MariaDB

### <a name="creation-process"></a>Skapande process

Privata slut punkter krävs för att aktivera privat länk. Detta kan göras med hjälp av följande instruktions guider.

* [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Godkännande process

När nätverks administratören skapar den privata slut punkten (PE) kan administratören hantera privat slut punkts anslutning (PEC) för att Azure Database for MariaDB. Denna uppdelning av uppgifter mellan nätverks administratören och DBA är användbar för hantering av den Azure Database for MariaDB anslutningen. 

* Navigera till Azure Database for MariaDB Server-resursen i Azure Portal. 
    * Välj anslutningar för privata slut punkter i det vänstra fönstret
    * Visar en lista över alla anslutningar för privata slut punkter (PECs)
    * Motsvarande privata slut punkt (PE) har skapats

![Välj den privata slut punkts portalen](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Välj en enskild PEC från listan genom att markera den.

![Välj den privata slut punkten som väntar på godkännande](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MariaDB-Server administratören kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort text svar.

![Välj meddelandet privat slut punkt](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Efter godkännande eller avvisande visar listan lämpligt tillstånd tillsammans med svars texten

![Välj slut tillstånd för privat slut punkt](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Använd fall av privat länk för Azure Database for MariaDB

Klienter kan ansluta till den privata slut punkten från samma VNet, peer-kopplat VNet i samma region eller via VNet-till-VNet-anslutning mellan regioner. Dessutom kan klienter ansluta lokalt med ExpressRoute, privat peering eller VPN-tunnlar. Nedan visas ett förenklat diagram som visar vanliga användnings fall.

![Välj översikt över privat slut punkt](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peer-Virtual Network (VNet)
Konfigurera [VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) för att upprätta anslutning till Azure Database for MariaDB från en virtuell Azure-dator i ett peer-kopplat VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö
Konfigurera [VPN gateway-anslutning mellan virtuella](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) nätverk för att upprätta anslutning till en Azure Database for MariaDB från en virtuell Azure-dator i en annan region eller prenumeration.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN
Om du vill upprätta en anslutning från en lokal miljö till Azure Database for MariaDB väljer du och implementerar något av alternativen:

* [Punkt-till-plats-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Plats-till-plats-anslutning via VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-krets](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brand Väggs regler

Följande situationer och resultat är möjliga när du använder en privat länk i kombination med brand Väggs regler:

* Om du inte konfigurerar några brand Väggs regler kommer ingen trafik att kunna komma åt Azure Database for MariaDB.

* Om du konfigurerar offentlig trafik eller en tjänst slut punkt och skapar privata slut punkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel.

* Om du inte konfigurerar någon offentlig trafik eller tjänst slut punkt och du skapar privata slut punkter kan Azure Database for MariaDB endast nås via de privata slut punkterna. Om du inte konfigurerar offentlig trafik eller en tjänst slut punkt efter att alla godkända privata slut punkter har avvisats eller tagits bort, kommer ingen trafik att kunna komma åt Azure Database for MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Neka offentlig åtkomst för Azure Database for MariaDB

Om du bara vill använda privata slut punkter för att komma åt sina Azure Database for MariaDB, kan du inaktivera inställningen alla offentliga slut punkter ([brand Väggs regler](concepts-firewall-rules.md) och [VNet-tjänstens slut punkter](concepts-data-access-security-vnet.md)) genom att ange inställningen **neka offentlig nätverks åtkomst** på databas servern. 

När den här inställningen är inställd på *Ja*, tillåts bara anslutningar via privata slut punkter till din Azure Database for MariaDB. När den här inställningen är inställd på *Nej*kan klienter ansluta till din Azure Database for MariaDB baserat på inställningarna för brand väggen eller VNet-tjänstens slut punkt. När värdet för åtkomst till privat nätverk har angetts kan kunder dessutom inte lägga till och/eller uppdatera befintliga brand Väggs regler och VNet-tjänstens slut punkts regler.

> [!Note]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL-enskild server stöder Generell användning och minnesoptimerade pris nivåer.
>
> Den här inställningen påverkar inte SSL-och TLS-konfigurationer för Azure Database for MariaDB.

Information om hur du ställer in **neka offentlig nätverks åtkomst** för din Azure Database for MariaDB från Azure Portal finns i [så här konfigurerar du neka offentlig nätverks åtkomst](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Database for MariaDB säkerhetsfunktioner finns i följande artiklar:

* Information om hur du konfigurerar en brand vägg för Azure Database for MariaDB finns i [brand Väggs stöd](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules).

* Information om hur du konfigurerar en tjänst slut punkt för ett virtuellt nätverk för Azure Database for MariaDB finns i [Konfigurera åtkomst från virtuella nätverk](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet).

* En översikt över Azure Database for MariaDB-anslutningen finns i [Azure Database for MariaDB anslutnings arkitektur](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md