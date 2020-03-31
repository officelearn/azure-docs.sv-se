---
title: Regler för virtuella nätverk – Azure Database för PostgreSQL - Single Server
description: Lär dig hur du använder slutpunkter för virtuella nätverk (vnet) för att ansluta till Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 512ad8f93da53afb618491cd1769645d8edb0b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965832"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Använda slutpunkter och regler för tjänsten Virtuellt nätverk för Azure Database för PostgreSQL - Single Server

*Virtuella nätverksregler* är en brandväggssäkerhetsfunktion som styr om din Azure-databas för PostgreSQL-server accepterar kommunikation som skickas från vissa undernät i virtuella nätverk. I den här artikeln förklarar vi varför den virtuella nätverksregelfunktionen ibland är det bästa alternativet för att på ett säkert sätt tillåta kommunikation till din Azure-databas för PostgreSQL-server.

Om du vill skapa en virtuell nätverksregel måste det först finnas ett [virtuellt nätverk][vm-virtual-network-overview] (VNet) och en slutpunkt för [virtuella nätverkstjänster][vm-virtual-network-service-endpoints-overview-649d] som regeln ska referera till. Följande bild illustrerar hur en slutpunkt för tjänsten Virtuellt nätverk fungerar med Azure Database for PostgreSQL:

![Exempel på hur en slutpunkt för VNet-tjänst fungerar](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner i Det offentliga Azure-molnet där Azure Database for PostgreSQL distribueras för servrar med allmänt syfte och minne.
> Om trafiken flödar via en gemensam VNet-gateway med tjänstslutpunkter och ska flöda till peer-enheten, skapar du en ACL/VNet-regel som tillåter virtuella Azure-datorer i gateway-nätverket att komma åt Azure-databasen för PostgreSQL-servern.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** Du kan ha virtuella nätverk som är associerade med din Azure-prenumeration.

**Undernät:** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella Azure-datorer (VIRTUELLA datorer) som du har tilldelats undernät. Ett undernät kan innehålla flera virtuella datorer eller andra beräkningsnoder. Beräkningsnoder som finns utanför det virtuella nätverket kan inte komma åt det virtuella nätverket om du inte konfigurerar säkerheten så att åtkomst tillåts.

**Slutpunkt för tjänsten Virtuellt nätverk:** Slutpunkten [för tjänsten Virtuellt nätverk][vm-virtual-network-service-endpoints-overview-649d] är ett undernät vars egenskapsvärden innehåller ett eller flera formella Azure-tjänsttypsnamn. I den här artikeln är vi intresserade av typnamnet för **Microsoft.Sql**, som refererar till Azure-tjänsten SOM heter SQL Database. Det här tjänstmärket gäller även för Azure Database för PostgreSQL- och MySQL-tjänster. Det är viktigt att notera när du använder **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt konfigurerar tjänstslutpunktstrafik för alla Azure SQL Database, Azure Database for PostgreSQL och Azure Database for MySQL-servrar i undernätet. 

**Regel för virtuellt nätverk:** En virtuell nätverksregel för din Azure Database for PostgreSQL-server är ett undernät som visas i åtkomstkontrollistan (ACL) på din Azure-databas för PostgreSQL-server. För att vara i ACL för din Azure-databas för PostgreSQL-server måste undernätet innehålla typnamnet **för Microsoft.Sql.**

En virtuell nätverksregel talar om för din Azure-databas för PostgreSQL-server att acceptera kommunikation från alla nodar som finns i undernätet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelar med en regel för virtuellt nätverk

Tills du vidtar åtgärder kan de virtuella datorerna i dina undernät inte kommunicera med din Azure-databas för PostgreSQL-server. En åtgärd som upprättar kommunikationen är att skapa en regel för virtuellt nätverk. Grunden för att välja VNet-regelmetoden kräver en diskussion om jämförelse och kontrast som involverar de konkurrerande säkerhetsalternativ som brandväggen erbjuder.

### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Säkerhetsfönstret Anslutning har en **ON/OFF-knapp** med etiketten **Tillåt åtkomst till Azure-tjänster**. **ON-inställningen** tillåter kommunikation från alla Azure IP-adresser och alla Azure-undernät. Dessa Azure-IPs eller undernät kanske inte ägs av dig. Den här **ON-inställningen** är förmodligen mer öppen än du vill att din Azure-databas för PostgreSQL-databas ska vara. Den virtuella nätverksregelfunktionen erbjuder mycket finare detaljerad kontroll.

### <a name="b-ip-rules"></a>B. IP-regler

Med Azure-databasen för PostgreSQL-brandväggen kan du ange IP-adressintervall från vilka kommunikation accepteras i Azure-databasen för PostgreSQL-databas. Den här metoden är bra för stabila IP-adresser som ligger utanför Azure privata nätverk. Men många noder i Azure privata nätverk är konfigurerade med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras, till exempel när den virtuella datorn startas om. Det skulle vara dårskap att ange en dynamisk IP-adress i en brandväggsregel, i en produktionsmiljö.

Du kan rädda IP-alternativet genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure-portalen][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Den statiska IP-metoden kan dock bli svår att hantera, och den är kostsam när den görs i stor skala. Virtuella nätverksregler är enklare att upprätta och hantera.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Det går ännu inte att ha Azure Database for PostgreSQL i ett undernät utan att definiera en tjänstslutpunkt

Om **Microsoft.Sql-servern** var en nod i ett undernät i det virtuella nätverket kunde alla noder i det virtuella nätverket kommunicera med din Azure Database för PostgreSQL-server. I det här fallet kan dina virtuella datorer kommunicera med Azure Database för PostgreSQL utan att behöva några virtuella nätverksregler eller IP-regler.

Från och med augusti 2018 är dock Azure Database for PostgreSQL-tjänsten ännu inte bland de tjänster som kan tilldelas direkt till ett undernät.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera detaljer om virtuella nätverksregler.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje slutpunkt för tjänsten För virtuellt nätverk gäller endast en Azure-region. Slutpunkten gör det inte möjligt för andra regioner att acceptera kommunikation från undernätet.

Alla virtuella nätverksregel är begränsade till den region som den underliggande slutpunkten gäller för.

### <a name="server-level-not-database-level"></a>Servernivå, inte databasnivå

Varje virtuell nätverksregel gäller för hela Azure Database för PostgreSQL-server, inte bara för en viss databas på servern. Med andra ord gäller regeln för virtuellt nätverk på servernivå, inte på databasnivå.

#### <a name="security-administration-roles"></a>Roller för säkerhetsadministration

Det finns en separation av säkerhetsroller i administrationen av slutpunkter för tjänsten Virtuellt nätverk. Åtgärder krävs från var och en av följande roller:

- **Nätverksadministratör:** &nbsp; Aktivera slutpunkten.
- **Databasadministratör:** &nbsp; Uppdatera åtkomstkontrollistan (ACL) för att lägga till det angivna undernätet i Azure Database för PostgreSQL-server.

*RBAC alternativ:*

Rollerna för nätverksadministratör och databasadministratör har fler funktioner än vad som behövs för att hantera virtuella nätverksregler. Endast en delmängd av deras kapacitet behövs.

Du har möjlighet att använda [rollbaserad åtkomstkontroll (RBAC)][rbac-what-is-813s] i Azure för att skapa en enda anpassad roll som bara har den nödvändiga delmängden av funktioner. Den anpassade rollen kan användas i stället för att involvera nätverksadministratören eller databasadministratören. Ytan på säkerhetsexponeringen är lägre om du lägger till en användare i en anpassad roll, jämfört med att lägga till användaren i de andra två stora administratörsrollerna.

> [!NOTE]
> I vissa fall finns Azure-databasen för PostgreSQL och VNet-undernätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Båda prenumerationerna måste finnas i samma Azure Active Directory-klientorganisation.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel aktivera tjänstslutpunkter och lägga till ett VNet-undernät till den angivna servern.
> - Kontrollera att båda prenumerationerna har **Microsoft.Sql-resursprovidern** registrerad. Mer information finns i [resurs-manager-registrering][resource-manager-portal]

## <a name="limitations"></a>Begränsningar

För Azure Database for PostgreSQL har funktionen för virtuella nätverksregler följande begränsningar:

- En webbapp kan mappas till en privat IP i ett virtuella nätverk/undernät. Även om tjänstslutpunkter är aktiverade från det angivna virtuella nätverket/undernätet, kommer anslutningar från Webbappen till servern att ha en offentlig Azure-IP-källa, inte en VNet/undernätskälla. Om du vill aktivera anslutning från en webbapp till en server med VNet-brandväggsregler måste du tillåta Azure-tjänster att komma åt servern på servern.

- I brandväggen för din Azure-databas för PostgreSQL refererar varje virtuell nätverksregel till ett undernät. Alla dessa refererade undernät måste vara värd i samma geografiska region som är värd för Azure-databasen för PostgreSQL.

- Varje Azure-databas för PostgreSQL-server kan ha upp till 128 ACL-poster för ett visst virtuellt nätverk.

- Virtuella nätverksregler gäller endast för virtuella Azure Resource Manager-nätverk. och inte till [klassiska distributionsmodellnätverk.][arm-deployment-model-568f]

- Om du aktiverar slutpunkter för virtuella nätverkstjänster till Azure Database för PostgreSQL med hjälp av **tjänsttaggen Microsoft.Sql** möjliggör du även slutpunkter för alla Azure Database-tjänster: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database och Azure SQL Data Warehouse.

- Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.

- I brandväggen gäller IP-adressintervall för följande nätverksobjekt, men regler för virtuella nätverk gäller inte:
    - [Virtuellt privat nätverk (Plats till plats) (VPN)][vpn-gateway-indexmd-608y]
    - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till Azure-nätverket med hjälp av [ExpressRoute][expressroute-indexmd-744v]konfigureras varje krets med två offentliga IP-adresser på Microsoft Edge. De två IP-adresserna används för att ansluta till Microsoft Services, till exempel till Azure Storage, med hjälp av Azure Public Peering.

Om du vill tillåta kommunikation från din krets till Azure Database för PostgreSQL måste du skapa IP-nätverksregler för de offentliga IP-adresserna för dina kretsar. Öppna en supportbiljett med ExpressRoute med Hjälp av Azure-portalen för att hitta de offentliga IP-adresserna för din ExpressRoute-krets.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en VNET-brandväggsregel på servern utan att aktivera VNET-tjänstslutpunkter

Att bara ange en brandväggsregel hjälper inte till att skydda servern till det virtuella nätverket. Du måste också aktivera slutpunkter **för** VNet-tjänsten för att säkerheten ska börja gälla. När du aktiverar tjänstslutpunkter **på**upplever ditt VNet-undernät driftstopp tills övergången är klar från **Av** till **På**. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingServiceEndpoint** för att minska eller eliminera stilleståndstiden under övergången.

Du kan ange flaggan **IgnoreMissingServiceEndpoint** med hjälp av Azure CLI eller portalen.

## <a name="related-articles"></a>Relaterade artiklar
- [Virtuella Azure-nätverk][vm-virtual-network-overview]
- [Tjänstslutpunkter för virtuellt nätverk i Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar VNet-regler finns i:
- [Skapa och hantera Azure Database för PostgreSQL VNet-regler med Hjälp av Azure-portalen](howto-manage-vnet-using-portal.md)
- [Skapa och hantera Azure Database för PostgreSQL VNet-regler med Azure CLI](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
