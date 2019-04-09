---
title: Azure Database för PostgreSQL-Server vnet services endpoint-översikt
description: Lär dig hur VNET-tjänstslutpunkter fungerar för din Azure Database for PostgreSQL-server.
author: mbolz
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/20/2018
ms.openlocfilehash: 83d16071cbcac4199db9f3757121f9811db49727
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006332"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Använda tjänstslutpunkter i virtuella nätverk och regler för Azure Database för PostgreSQL

*Virtuella Nätverksregler* är en säkerhetsfunktion för brandväggen som styr om din Azure Database for PostgreSQL-server tar emot meddelanden som skickas från specifika undernät i virtuella nätverk. Den här artikeln förklarar varför funktionen virtuellt nätverk regeln ibland är det bästa alternativet för att tillåta kommunikation till Azure Database for PostgreSQL-server på ett säkert sätt.

Skapa en virtuell nätverksregel det måste du först ta en [virtuellt nätverk] [ vm-virtual-network-overview] (VNet) och en [tjänstslutpunkt för virtuellt nätverk] [ vm-virtual-network-service-endpoints-overview-649d] för den regel-referensen. Följande bild illustrerar hur en tjänstslutpunkt för virtuellt nätverk fungerar med Azure Database för PostgreSQL:

![Exempel på hur en tjänstslutpunkt för virtuellt nätverk fungerar](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner där Azure offentligt moln där Azure Database for PostgreSQL distribueras för generell användning och Minnesoptimerad servrar.
> VNet peering om trafik flödar genom en gemensam Gateway för virtuellt nätverk med tjänstslutpunkter och ska flöda till peer-datorn kan du skapa en ACL/VNet-regel som tillåter Azure Virtual Machines i Gateway virtuella nätverk till Azure Database for PostgreSQL-server.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** Du kan ha virtuella nätverk som är associerade med din Azure-prenumeration.

**Undernät:** Ett virtuellt nätverk innehåller **undernät**. Alla Azure virtuella datorer (VM) som du har har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra compute-noder. Compute-noder som är utanför ditt virtuella nätverk inte kan komma åt det virtuella nätverket om du inte konfigurerar din säkerhet för att tillåta åtkomst.

**Tjänstslutpunkt för virtuellt nätverk:** En [tjänstslutpunkt för virtuellt nätverk] [ vm-virtual-network-service-endpoints-overview-649d] är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. I den här artikeln vi vill att ange namnet på **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsttaggen gäller även för Azure Database för PostgreSQL och MySQL-tjänster. Det är viktigt att tänka på när du använder den **Microsoft.Sql** servicetagg till en VNet-tjänstslutpunkt konfigureras tjänstens slutpunkt trafik för alla Azure SQL Database, Azure Database för PostgreSQL och Azure Database for MySQL-servrar i undernätet. 

**Regel för virtuella nätverk:** En virtuell nätverksregel för din Azure Database for PostgreSQL-server är ett undernät som anges i åtkomstkontrollistan (ACL) för din Azure Database for PostgreSQL-server. För att i Åtkomstkontrollistan för din Azure Database for PostgreSQL-server måste undernätet måste innehålla den **Microsoft.Sql** typnamn.

En virtuell nätverksregel talar om för Azure Database for PostgreSQL-server för att acceptera kommunikation från varje nod i undernätet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelarna med en regel för virtuella nätverk

Tills du vidta åtgärder kan inte de virtuella datorerna på dina undernät kommunicera med Azure Database för PostgreSQL-server. En åtgärd som upprättar kommunikation är att skapa en regel för virtuella nätverk. Anledningen till att välja metod för VNet-regeln kräver Jämför och kontrast diskussioner som involverar konkurrerande säkerhetsalternativ som erbjuds av brandväggen.

### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Fönstret anslutning security har en **på/av** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Den **på** inställningen tillåter kommunikation från alla Azure-IP-adresser och undernät för alla Azure. De här Azure IP-adresser eller undernät kan inte ägas av dig. Detta **på** är förmodligen mer öppet än vad du vill att din Azure Database for PostgreSQL-databasen ska vara. Virtuellt nätverk regeln funktionen erbjuder mycket använda detaljerad styrning.

### <a name="b-ip-rules"></a>B. IP-regler

Azure Database for PostgreSQL-brandväggen kan du ange IP-adressintervall som accepteras kommunikation till Azure Database for PostgreSQL-databas. Den här metoden är bra för stabil IP-adresser som ligger utanför Azure privat nätverk. Men många noder i Azure privat nätverk har konfigurerats med *dynamisk* IP-adresser. Dynamiska IP-adresser ändras, till exempel när den virtuella datorn startas. Det vore fluga att ange en dynamisk IP-adress i en brandväggsregel i en produktionsmiljö.

Du kan rädda IP-alternativet genom att hämta en *Statiska* IP-adress för den virtuella datorn. Mer information finns i [konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure-portalen][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Dock kan det vara svårt att hantera den statiska IP-metoden och det är kostsamt om det görs i stor skala. Virtual network-regler är lättare att upprätta och hantera.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Inte ännu har Azure Database för PostgreSQL i ett undernät utan att definiera en tjänstslutpunkt

Om din **Microsoft.Sql** servern var en nod i ett undernät i det virtuella nätverket, alla noder i det virtuella nätverket kan kommunicera med Azure Database för PostgreSQL-server. I det här fallet kan dina virtuella datorer kommunicera med Azure Database för PostgreSQL utan några regler för virtuellt nätverk eller en IP-regler.

Men från och med augusti 2018, Azure Database for PostgreSQL-tjänsten är ännu inte mellan tjänster som kan tilldelas direkt till ett undernät.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om virtual network-regler

Det här avsnittet beskrivs flera detaljer om regler för virtuellt nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje tjänstslutpunkt för virtuellt nätverk gäller endast en Azure-region. Slutpunkten kan inte andra regioner att godta kommunikation från undernätet.

Regel för virtuella nätverk är begränsad till den region som dess underliggande slutpunkten som gäller för.

### <a name="server-level-not-database-level"></a>På servernivå, inte på databasnivå

Varje virtuellt nätverk regeln gäller för hela Azure Database för PostgreSQL-server, inte bara till en viss databas på servern. Med andra ord gäller virtuell nätverksregel på servernivå inte på databasnivå.

#### <a name="security-administration-roles"></a>Administration säkerhetsroller

Det finns en uppdelning av säkerhetsroller i administrationen av tjänstslutpunkter i virtuella nätverk. Åtgärd krävs från var och en av följande roller:

- **Administratör för nätverk:** &nbsp; Aktivera slutpunkten.
- **Administratör för databasen:** &nbsp; Uppdatera åtkomstkontrollistan (ACL) för att lägga till det angivna undernätet till Azure Database for PostgreSQL-server.

*RBAC-alternativ:*

Rollerna för nätverk och databas delad Admin har fler funktioner än vad som behövs för att hantera virtuella Nätverksregler. Endast en delmängd av deras funktioner krävs.

Har du möjlighet att använda [rollbaserad åtkomstkontroll (RBAC)] [ rbac-what-is-813s] i Azure för att skapa en enda anpassad roll som har endast nödvändiga delmängd av funktionerna. Den anpassade rollen som kan användas i stället för som involverar nätverk administratören eller administratören för databasen. Ytan på exponeringen säkerhet är lägre om du lägger till en användare till en anpassad roll, jämfört med att lägga till användaren i de andra två större administratörsroller.

> [!NOTE]
> I vissa fall är Azure Database för PostgreSQL och VNet-undernät i olika prenumerationer. I dessa fall måste du kontrollera att följande konfigurationer:
> - Båda prenumerationerna måste finnas i samma Azure Active Directory-klient.
> - Användaren har behörigheterna som krävs för att initiera åtgärder, till exempel aktiverar Tjänsteslutpunkter och lägga till ett VNet-undernät i den angivna servern.

## <a name="limitations"></a>Begränsningar

För Azure Database for PostgreSQL har funktionen för regler för virtuellt nätverk följande begränsningar:

- En Webbapp kan mappas till en privat IP-adress i ett virtuellt nätverk/undernät. Även om tjänstslutpunkter är aktiverade från de angivna VNet/undernät, har anslutningar från Webbappen till servern en Azure offentlig IP-källa, inte en virtuellt nätverk/undernät källa. Om du vill aktivera anslutningen från en Webbapp till en server med VNet-brandväggsreglerna, måste du tillåta Azure-tjänster för att komma åt servern på servern.

- Refererar till ett undernät i brandväggen för din Azure Database for PostgreSQL varje regel för virtuella nätverk. Alla dessa refererade undernät måste finnas i samma geografiska region som är värd för Azure Database för PostgreSQL.

- Varje Azure Database for PostgreSQL-server kan ha upp till 128 ACL-posterna för alla angivna virtuella nätverket.

- Virtual network-regler gäller endast för Azure Resource Manager-nätverk; och inte till [klassiska distributionsmodellen] [ arm-deployment-model-568f] nätverk.

- Att aktivera på virtuella nätverksslutpunkter till Azure Database för PostgreSQL med hjälp av den **Microsoft.Sql** tjänsttagg gör det också möjligt för slutpunkterna för alla Azure-databastjänster: Azure Database för MySQL, Azure Database för PostgreSQL, Azure SQL Database och Azure SQL Data Warehouse.

- Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

- IP-adressintervall gäller följande nätverk i brandväggen, men inte av virtuella Nätverksregler:
    - [Plats-till-plats (S2S) virtuellt privat nätverk (VPN)][vpn-gateway-indexmd-608y]
    - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till det Azure-nätverket med hjälp av [ExpressRoute][expressroute-indexmd-744v], varje krets har konfigurerats med två offentliga IP-adresser i Microsoft Edge. Två IP-adresser används för att ansluta till Microsoft Services, till exempel till Azure Storage, med hjälp av Azure offentlig Peering.

Om du vill tillåta kommunikation från din krets till Azure Database för PostgreSQL, måste du skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar. För att kunna hitta ExpressRoute-kretsen offentliga IP-adresser, öppnar du ett supportärende hos ExpressRoute med hjälp av Azure portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Att lägga till en brandväggsregel för virtuellt nätverk till din server utan att slå på VNET-tjänstslutpunkter

Endast ställa en brandväggsregel inte att skydda servern till det virtuella nätverket. Du måste också aktivera tjänstslutpunkter i virtuella nätverk **på** för att skydda ska börja gälla. När du aktiverar Tjänsteslutpunkter **på**, avbrott inträffar i ditt VNet-undernät förrän processen är klar övergången från **av** till **på**. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda den **IgnoreMissingServiceEndpoint** flagga för att minska eller eliminera nedtiden under övergång.

Du kan ange den **IgnoreMissingServiceEndpoint** flaggan med hjälp av Azure CLI eller portalen.

## <a name="related-articles"></a>Relaterade artiklar
- [Azure-nätverk][vm-virtual-network-overview]
- [Tjänstslutpunkter i virtuella Azure-nätverket][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar regler för virtuellt nätverk, finns här:
- [Skapa och hantera Azure Database för PostgreSQL-VNet-regler med hjälp av Azure portal](howto-manage-vnet-using-portal.md)
- [Skapa och hantera Azure Database för PostgreSQL-VNet-regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml
