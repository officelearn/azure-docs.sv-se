---
title: Översikt över Azure Database for MariaDB Server VNet Services-slutpunkt | Microsoft Docs
description: Beskriver hur VNet-tjänstens slut punkter fungerar för din Azure Database for MariaDB-Server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 5ca7a62ed6b9cd132e0cae226c2123043c833ffa
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610414"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Använd Virtual Network tjänst slut punkter och regler för Azure Database for MariaDB

*Regler för virtuella nätverk* är en brand Väggs säkerhetsfunktion som styr om din Azure Database for MariaDB Server accepterar kommunikation som skickas från vissa undernät i virtuella nätverk. I den här artikeln förklaras varför funktionen för regel för virtuella nätverk ibland det bästa alternativet för att på ett säkert sätt tillåta kommunikation till din Azure Database for MariaDB-Server.

Om du vill skapa en regel för virtuellt nätverk måste du först vara ett [virtuellt nätverk][vm-virtual-network-overview] (VNet) och en [tjänst slut punkt för virtuellt nätverk][vm-virtual-network-service-endpoints-overview-649d] för regeln som ska refereras. Följande bild illustrerar hur en Virtual Network tjänst slut punkt fungerar med Azure Database for MariaDB:

![Exempel på hur en VNet-tjänst slut punkt fungerar](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner i Azure där Azure Database for MariaDB distribueras för Generell användning och minnesoptimerade servrar.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** Du kan ha virtuella nätverk kopplade till din Azure-prenumeration.

**Delnät** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella datorer i Azure (VM) som du har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra Compute-noder. Compute-noder utanför det virtuella nätverket kan inte komma åt ditt virtuella nätverk om du inte konfigurerar din säkerhet att tillåta åtkomst.

**Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt][vm-virtual-network-service-endpoints-overview-649d] är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I den här artikeln är vi intresserade av typ namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänst tag gen gäller även för Azure Database for MariaDB-, MySQL-och PostgreSQL-tjänster. Det är viktigt att du noterar när du använder service tag-koden för **Microsoft. SQL** på en slut punkt för VNet-tjänsten som används för att konfigurera tjänst slut punkts trafik för alla Azure SQL Database, Azure Database for MariaDB, Azure Database for MySQL och Azure Database for PostgreSQL servrar i under nätet.

**Regel för virtuellt nätverk:** En regel för virtuella nätverk för din Azure Database for MariaDB-Server är ett undernät som listas i åtkomst kontrol listan (ACL) för din Azure Database for MariaDB-Server. För att finnas i ACL: en för din Azure Database for MariaDB-Server måste under nätet innehålla namnet **Microsoft. SQL** -typ.

En regel för virtuella nätverk instruerar Azure Database for MariaDB servern att acceptera kommunikation från varje nod som finns på under nätet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelar med en virtuell nätverks regel

De virtuella datorerna i under näten kan inte kommunicera med din Azure Database for MariaDB-Server förrän du vidtar åtgärder. En åtgärd som upprättar kommunikationen är att skapa en regel för virtuella nätverk. Anledningen till att du väljer regel metoden för VNet kräver en jämförelse-och-kontrast-diskussion som involverar de konkurrerande säkerhets alternativ som erbjuds av brand väggen.

### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Fönstret anslutnings säkerhet har en **på/av-** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Inställningen **on** tillåter kommunikation från alla Azure IP-adresser och alla Azure-undernät. Dessa Azure IP-adresser eller undernät kanske inte ägs av dig. Den **här** inställningen är förmodligen mer öppen än du vill att din Azure Database for MariaDB-databas ska vara. Funktionen för regel för virtuella nätverk ger en mycket noggrannare detaljerad kontroll.

### <a name="b-ip-rules"></a>B. IP-regler

Med Azure Database for MariaDB brand väggen kan du ange IP-adressintervall från vilka kommunikationen godkänns i Azure Database for MariaDB-servern. Den här metoden är bra för stabila IP-adresser som ligger utanför Azures privata nätverk. Men många noder i det privata Azure-nätverket har kon figurer ATS med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras, till exempel när den virtuella datorn startas om. Det skulle vara Folly att ange en dynamisk IP-adress i en brand Väggs regel i en produktions miljö.

Du kan hämta IP-alternativet genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Den statiska IP-metoden kan dock bli svår att hantera, och den är kostsam när den görs i stor skala. Det är enklare att upprätta och hantera virtuella nätverks regler.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. Det går inte att ha Azure Database for MariaDB i ett undernät ännu utan att definiera en tjänst slut punkt

Om din **Microsoft. SQL** -Server var en nod i ett undernät i det virtuella nätverket kan alla noder i det virtuella nätverket kommunicera med din Azure Database for MariaDB-Server. I det här fallet kan de virtuella datorerna kommunicera med Azure Database for MariaDB utan att behöva några regler för virtuella nätverk eller IP-regler.

Men från och med augusti 2018 är Azure Database for MariaDB tjänsten ännu inte bland de tjänster som kan tilldelas direkt till ett undernät.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera Detaljer om regler för virtuella nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje Virtual Network tjänst slut punkt gäller endast en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet.

Alla virtuella nätverks regler är begränsade till den region som den underliggande slut punkten gäller för.

### <a name="server-level-not-database-level"></a>Server nivå, inte databas nivå

Varje virtuell nätverks regel gäller hela Azure Database for MariaDB-servern, inte bara till en viss databas på servern. Med andra ord gäller regel för virtuella nätverk på server nivå, inte på databas nivå.

### <a name="security-administration-roles"></a>Säkerhets administrations roller

Det finns en separation av säkerhets roller i administration av Virtual Network tjänstens slut punkter. Åtgärd krävs från var och en av följande roller:

- **Nätverks administratör:** &nbsp;Aktivera slut punkten.
- **Databas administratör:** &nbsp;Uppdatera åtkomst kontrol listan (ACL) för att lägga till angivet undernät till Azure Database for MariaDB servern.

*RBAC-alternativ:*

Rollerna för nätverks administratör och databas administratörer har fler funktioner än vad som krävs för att hantera virtuella nätverks regler. Endast en delmängd av deras funktioner krävs.

Du kan välja att använda [rollbaserad åtkomst kontroll (RBAC)][rbac-what-is-813s] i Azure för att skapa en enskild anpassad roll som bara har de funktioner som krävs. Den anpassade rollen kan användas i stället för att involvera antingen nätverks administratören eller databas administratören. Arean av din säkerhets exponering är lägre om du lägger till en användare i en anpassad roll och lägger till användaren till de andra två större administratörs rollerna.

> [!NOTE]
> I vissa fall finns Azure Database for MariaDB och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Båda prenumerationerna måste vara i samma Azure Active Directory-klient.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel aktivera tjänst slut punkter och lägga till ett VNet-undernät till den aktuella servern.
> - Se till att båda prenumerationerna har **Microsoft. SQL** -lagringsprovidern registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

## <a name="limitations"></a>Begränsningar

För Azure Database for MariaDB har funktionen regler för virtuellt nätverk följande begränsningar:

- En webbapp kan mappas till en privat IP-adress i ett VNet/undernät. Även om tjänstens slut punkter är påslagna från det virtuella nätverket/under nätet kommer anslutningar från webbappen till-servern att ha en offentlig Azure-IP-källa, inte en källa för VNet/undernät. Om du vill aktivera anslutning från en webbapp till en server som har VNet brand Väggs regler måste du ge Azure-tjänster åtkomst till servern på servern.

- I brand väggen för din Azure Database for MariaDB refererar varje virtuell nätverks regel till ett undernät. Alla dessa refererade undernät måste ligga inom samma geografiska region som är värd för Azure Database for MariaDB.

- Varje Azure Database for MariaDB Server kan ha upp till 128 ACL-poster för ett angivet virtuellt nätverk.

- Regler för virtuella nätverk gäller endast för Azure Resource Manager virtuella nätverk; och inte till [klassiska][resource-manager-deployment-model-568f] nätverk för distributions modeller.

- Att aktivera tjänst slut punkter för virtuella nätverk för att Azure Database for MariaDB med hjälp av service tag-koden för **Microsoft. SQL** aktiverar även slut punkterna för alla Azure Database-tjänster: Azure Database for MariaDB, Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database och Azure SQL Data Warehouse.

- Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

- I brand väggen gäller IP-adressintervall för följande nätverks objekt, men regler för virtuella nätverk gör inte följande:
    - [Virtuellt privat nätverk (VPN) för plats-till-plats (S2S)][vpn-gateway-indexmd-608y]
    - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till Azure-nätverket med hjälp av [ExpressRoute][expressroute-indexmd-744v]konfigureras varje krets med två offentliga IP-adresser på Microsoft Edge. De två IP-adresserna används för att ansluta till Microsoft-tjänster, till exempel för att Azure Storage med hjälp av Azures offentliga peering.

Om du vill tillåta kommunikation från din krets till Azure Database for MariaDB måste du skapa IP-nätverksnummer för dina kretsars offentliga IP-adresser. För att hitta den offentliga IP-adressen för din ExpressRoute-krets öppnar du ett support ärende med ExpressRoute med hjälp av Azure Portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en brand Väggs regel för VNET på servern utan att aktivera VNET-tjänstens slut punkter

Att bara ange en brand Väggs regel skyddar inte servern mot VNet. Du måste också aktivera VNet-tjänstens slut **punkter för att** säkerheten ska börja gälla. När du aktiverar tjänstens slutpunkter aktive ras stillestånds tiden för VNet-undernät tills den slutför över **gången från till** **på**. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingServiceEndpoint** för att minska eller eliminera stillestånds tiden under över gången.

Du kan ställa in flaggan **IgnoreMissingServiceEndpoint** med hjälp av Azure CLI eller portalen.

## <a name="related-articles"></a>Relaterade artiklar
- [Virtuella Azure-nätverk][vm-virtual-network-overview]
- [Tjänst slut punkter för Azure Virtual Network][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar VNet-regler finns i:
- [Skapa och hantera Azure Database for MariaDB VNet-regler med hjälp av Azure Portal](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md
