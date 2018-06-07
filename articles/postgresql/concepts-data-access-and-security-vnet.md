---
title: Azure-databas för PostgreSQL Server vnet services endpoint översikt | Microsoft Docs
description: Beskriver hur vnet Tjänsteslutpunkter fungerar för din Azure-databas för PostgreSQL-servern.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2018
ms.openlocfilehash: a832f45027fc5337d9e76ec9cc4898286121278c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655661"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Använda slutpunkter för virtuellt nätverk och regler för Azure-databas för PostgreSQL

*Virtuellt Nätverksregler* är en säkerhetsfunktion i brandväggen som styr om din Azure-databas för PostgreSQL servern accepterar kommunikation som skickas från särskilt undernät i virtuella nätverk. Den här artikeln förklarar varför funktionen virtuellt nätverk regeln ibland är det bästa alternativet för att tillåta kommunikation till din Azure-databas för PostgreSQL-server på ett säkert sätt.

Att skapa en regel för virtuellt nätverk, det måste först vara en [virtuellt nätverk] [ vm-virtual-network-overview] (VNet) och en [virtuellt nätverk tjänstslutpunkten] [ vm-virtual-network-service-endpoints-overview-649d] för den regeln till referens. Följande bild illustrerar hur en tjänstslutpunkt för virtuellt nätverk fungerar med Azure-databas för PostgreSQL:

![Exempel på hur en tjänstslutpunkt VNet fungerar](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Den här funktionen är tillgänglig som förhandsversion i alla områden av offentliga Azure-molnet där Azure-databas för PostgreSQL distribueras för Azure-databas för PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** du kan ha virtuella nätverk som är associerade med din Azure-prenumeration.

**Undernät:** ett virtuellt nätverk innehåller **undernät**. Alla virtuella Azure-datorer (VM) som du har har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra compute-noder. Compute-noder som är utanför det virtuella nätverket inte kan komma åt ditt virtuella nätverk såvida du inte konfigurerar din säkerhet för att tillåta åtkomst.

**Virtuella nätverk tjänstslutpunkten:** A [virtuellt nätverk tjänstslutpunkten] [ vm-virtual-network-service-endpoints-overview-649d] är ett undernät vars egenskapsvärden innehåller en eller flera formella Azure-tjänst-typnamn. I den här artikeln vi vill ange namnet på **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsten taggen gäller även för Azure-databasen för PostgreSQL och MySQL-tjänster. Det är viktigt att känna till när du använder den **Microsoft.Sql** tjänsten taggen till VNet-tjänsteslutpunkt konfigurerar den tjänstens slutpunkt trafik för alla Azure SQL Database, Azure-databas för PostgreSQL och Azure-databas för MySQL-servrar i undernätet. 

**Virtuellt nätverk regel:** en regel för virtuellt nätverk för din Azure-databas för PostgreSQL-server är ett undernät som anges i åtkomstkontrollistan (ACL) för din Azure-databas för PostgreSQL-servern. För att vara i Åtkomstkontrollistan för din Azure-databas för PostgreSQL server undernätet måste innehålla den **Microsoft.Sql** typnamn.

En regel för virtuellt nätverk talar om för din Azure-databas för PostgreSQL-servern att acceptera kommunikation från varje nod i undernätet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelarna med en regel för virtuellt nätverk

Tills du vidta åtgärder kommunicera de virtuella datorerna på dina undernät inte med din Azure-databas för PostgreSQL-servern. En åtgärd som upprättar kommunikation är att skapa en regel för virtuellt nätverk. Grund för att välja metod för VNet-regeln kräver en Jämför och kontrast diskussion som involverar konkurrerande säkerhetsalternativ som erbjuds av brandväggen.

### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Fönstret anslutning säkerhet har en **på/av** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Den **ON** inställningen tillåter kommunikation från alla Azure-IP-adresser och undernät för alla Azure. Dessa Azure IP-adresser eller undernät kan inte ägas av du. Detta **ON** inställningen är förmodligen mer öppna än du vill att din Azure-databas för PostgreSQL-databasen ska vara. Funktionen virtuellt nätverk regeln erbjuder mycket avancerat hjälpmedel granulär kontroll.

### <a name="b-ip-rules"></a>B. IP-regler

Azure-databasen för PostgreSQL-brandväggen kan du ange IP-adressintervall som accepteras kommunikation till Azure-databas för PostgreSQL-databas. Den här metoden fungerar bra för stabil IP-adresser som ligger utanför Azure privata nätverk. Men många noder i Azure privata nätverk har konfigurerats med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras, till exempel när den virtuella datorn startas om. Det vore fluga att ange en dynamisk IP-adress i en brandväggsregel i en produktionsmiljö.

Du kan rädda IP-alternativet genom att hämta en *Statiska* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privat IP-adresser för en virtuell dator med hjälp av Azure portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Men den statiska IP-metoden kan bli svårt att hantera och det är kostsamma när du är klar i större skala. Virtuella nätverk är enklare att skapa och hantera.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Kan inte ännu har Azure-databas för PostgreSQL i ett undernät utan att definiera en tjänstslutpunkt

Om din **Microsoft.Sql** servern var en nod i ett undernät i det virtuella nätverket, alla noder i det virtuella nätverket kan kommunicera med din Azure-databas för PostgreSQL-servern. I det här fallet kan dina virtuella datorer kommunicera med Azure-databas för PostgreSQL utan några regler för virtuellt nätverk eller IP-regler.

Men från och med kan 2018 Azure-databasen för PostgreSQL-tjänsten är ännu inte bland de tjänster som kan tilldelas direkt till ett undernät.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuellt nätverk

Det här avsnittet beskrivs flera detaljer om regler för virtuellt nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje virtuellt nätverk tjänstslutpunkten gäller endast en Azure-region. Slutpunkten kan inte andra regioner att godta kommunikation från undernätet.

Alla virtuella nätverk regeln är begränsad till den region som sin underliggande slutpunkt gäller.

### <a name="server-level-not-database-level"></a>Servernivå kan inte databasnivå

Varje virtuellt nätverk regel tillämpas på Azure hela databasen för PostgreSQL-server, inte bara en viss databas på servern. Med andra ord regeln virtuellt nätverk på servernivå inte på databas-nivå.

#### <a name="security-administration-roles"></a>Säkerhetsroller för administration

Det finns en uppdelning av säkerhetsroller i administrationen av slutpunkter för virtuellt nätverk. Åtgärd krävs från var och en av följande roller:

- **Nätverket Admin:** &nbsp; aktivera slutpunkten.
- **Databasen Admin:** &nbsp; uppdatera åtkomstkontrollistan (ACL) för att lägga till det angivna undernätet Azure-databasen för PostgreSQL-servern.

*RBAC alternativ:*

Roller i nätverket Admin och databasen Admin har mer än vad som behövs för att hantera virtuella Nätverksregler. Endast en del av deras funktioner krävs.

Du har möjlighet att använda [rollbaserad åtkomstkontroll (RBAC)] [ rbac-what-is-813s] i Azure för att skapa en anpassad roll som har den nödvändiga delmängden av funktioner. Den anpassade rollen som kan användas i stället för som omfattar nätverk Admin eller administratören för databasen. Ytan på din sårbart är lägre om du lägger till en användare till en anpassad roll, jämfört med att lägga till användaren i de andra två huvudsakliga administratörsroller.

> [!NOTE]
> I vissa fall är Azure-databasen för PostgreSQL och VNet-undernät för olika prenumerationer. I dessa fall måste du kontrollera följande konfigurationer:
> - Båda prenumerationer måste vara i samma Azure Active Directory-klienten.
> - Användaren har behörigheterna som krävs för att initiera åtgärder, till exempel aktivera slutpunkter och lägga till ett VNet-undernät i den angivna servern.

## <a name="limitations"></a>Begränsningar

Azure-databas för PostgreSQL har funktionen virtuellt nätverk regler följande begränsningar:

- Refererar till ett undernät i brandväggen för din Azure-databas för PostgreSQL varje regel för virtuellt nätverk. Dessa refererade undernät måste finnas i samma geografiska region där den Azure-databas för PostgreSQL.

- Varje Azure-databas för PostgreSQL-server kan ha upp till 128 ACL-posterna för alla angivna virtuella nätverket.

- Virtuellt Nätverksregler gäller bara för Azure Resource Manager virtuella nätverk. och inte [klassiska distributionsmodellen] [ arm-deployment-model-568f] nätverk.

- Aktivering av virtuella nätverksslutpunkter till Azure-databas för PostgreSQL med hjälp av den **Microsoft.Sql** service taggen gör det också möjligt för slutpunkter för alla Azure-databastjänster: Azure-databas för MySQL, Azure-databas för PostgreSQL , Azure SQL Database och Azure SQL Data Warehouse.

- Vid tidpunkten för förhandsversion finns det inget stöd för virtuella nätverk flytta åtgärder. Om du vill flytta en regel för virtuellt nätverk, släppa och återskapa den.

- Stöd för VNet slutpunkter stöds endast för generella och Minnesoptimerade servrar.

- IP-adressintervall gäller följande nätverk i brandväggen, men gör inte det virtuella Nätverksregler:
    - [Plats-till-plats (S2S) virtuellt privat nätverk (VPN)][vpn-gateway-indexmd-608y]
    - Lokalt [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till Azure-nätverk med hjälp av [ExpressRoute][expressroute-indexmd-744v], varje kretsen har konfigurerats med två offentliga IP-adresser i Microsoft Edge. Två IP-adresser används för att ansluta till Microsoft Services, som till Azure Storage med hjälp av Azure offentlig Peering.

Om du vill tillåta kommunikation från kretsen till Azure-databas för PostgreSQL, måste du skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar. Öppna ett supportärende för att hitta de offentliga IP-adresserna för ExpressRoute-kretsen med ExpressRoute med hjälp av Azure portal.

## <a name="related-articles"></a>Relaterade artiklar
- [Virtuella Azure-nätverk][vm-virtual-network-overview]
- [Slutpunkter för virtuella Azure-nätverket][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar virtuella nätverk regler finns:
- [Skapa och hantera Azure-databas för PostgreSQL VNet-regler med hjälp av Azure portal](howto-manage-vnet-using-portal.md)
- [Skapa och hantera Azure-databas för PostgreSQL VNet-regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same Github repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml