---
title: Hög tillgänglighet – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om Azure SQL Database tjänstfunktioner för hög tillgänglighet och funktioner
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 2283b7559bb0dc7e8333949a8e6382d562162123
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092495"
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL-databas

Azure SQL Database är en databas med hög tillgänglighet plattform som en tjänst som garanterar att din databas är igång och körs 99,99% av tiden, utan att behöva bekymra dig om underhåll och stillestånd. Det här är en fullständigt hanterad SQL Server Database Engine-processen i Azure-molnet som ser till att SQL Server-databasen är alltid uppgraderas/korrigerade utan att påverka din arbetsbelastning. Azure SQL Database kan snabbt återställa även i de mest kritiska omständigheter som säkerställer att dina data alltid är tillgänglig.

Azure-plattformen fullständigt hanterar varje Azure SQL-databas och garanterar att inga data går förlorade och en hög andel datatillgänglighet. Azure hanterar automatiskt korrigeringar, säkerhetskopieringar, replikering, felidentifiering, underliggande potentiella maskinvara, program- eller fel, distribuera felkorrigeringar, redundans, databasen uppgraderingar och andra underhållsuppgifter. SQL Server-tekniker har implementerat MES praxis att säkerställa att alla underhållsåtgärder för har utförts i mindre än 0,01% tidpunkten för livet databas. Den här arkitekturen är utformad för att se till att allokerade data aldrig går förlorad och att underhåll utförs utan att påverka arbetsbelastning. Det finns inga underhållsperioder eller stilleståndstider som bör kräver att du stoppar arbetsbelastningen när databasen har uppgraderats eller bibehålls. Inbyggd hög tillgänglighet i Azure SQL Database garanterar att databasen blir aldrig felkritisk systemdel i din programvaruarkitektur.

Det finns två modeller för hög tillgänglighet som tillämpas i Azure SQL:

- Standard/generell användning-modell som ger 99,99% tillgänglighet, men potentiella prestandan försämras under underhållsaktiviteter.
- Premium/kritiska affärsmodell som tillhandahåller dessutom 99,99% tillgänglighet med minsta möjliga prestandapåverkan på din arbetsbelastning även under underhållsaktiviteter.

Azure uppgraderar och korrigerar underliggande operativsystemet, drivrutiner och SQL Server Database Engine transparent med minimalt driftstopp för slutanvändare. Azure SQL-databas som körs på den senaste stabila versionen av SQL Server Database Engine och Windows OS och de flesta användarna skulle inte lägger märke till att uppgraderingen utförs kontinuerligt.

## <a name="standard-availability"></a>Standard tillgänglighet

Standard tillgänglighet syftar på 99,99% serviceavtal (SLA) som används för nivåerna Standard/Basic/generell användning. Tillgänglighet uppnås genom uppdelning av beräknings- och lager. I standard tillgänglighet modellen har vi två nivåer:

- En tillståndslös beräkningslager som kör sqlserver.exe processen och innehåller bara tillfälliga och cachelagrade data (till exempel – plancachen, buffertpoolen, kolumnen store pool). Den här tillståndslösa SQL Server-noden drivs av Azure Service Fabric som initierar processen, kontrollerar hälsotillståndet för noden och utför redundans till en annan plats om det behövs.
- En tillståndskänslig datalager med databasfiler (.mdf/.ldf) som lagras i Azure Premium Storage-diskar. Azure Storage garanterar att inga data går förlorade i poster som placeras i en databasfil. Azure Storage har inbyggda tillgänglighet/redundans som säkerställer att varje post i loggfilen eller sida i datafilen kommer att bevaras även om SQL Server-processen kraschar.

När databasmotorn eller operativsystemet uppgraderas eller om vissa viktiga problem har identifierats i Sql Server-processen, tillståndslösa SQL Server-processen Azure Service Fabric flyttas till en annan tillståndslösa compute-nod. Data i Azure Storage-skiktet påverkas inte och data/loggfiler är kopplade till nyligen initierad SQL Server-processen. Förväntade redundanstiden kan mätas på några sekunder. Den här processen garanterar 99,99% tillgänglighet, men det kan ha vissa prestandaeffekter på arbetsbelastning som körs på grund av övergångstid och faktumet den nya SQL Server-noden börjar med kalla cache.

## <a name="premium-availability"></a>Premium-tillgänglighet

Premium-tillgänglighet är aktiverat i Premium-nivån av Azure SQL Database och den är utformad för intensiva arbetsbelastningar som inte tolererar en prestandaförsämring på grund av pågående underhållsåtgärder.

I premium-modellen integreras Azure SQL-databas beräkning och lagring på en enda nod. Både SQL Server Database Engine-processen och underliggande mdf/ldf-filerna är placerade på samma nod med lokalt anslutna SSD-lagring som ger låg fördröjning till din arbetsbelastning.

Hög tillgänglighet implementeras med hjälp av standard [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster av databasnoderna med en primär databas som är tillgänglig för kunds arbetsbelastning och några sekundära processer som innehåller kopior av data. Den primära noden skickar ständigt ändringarna till sekundära noder för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden kraschar av någon anledning. Redundansväxling hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en ny sekundär replik skapas för att se till att tillräckligt många noder i klustret. Arbetsbelastningen omdirigeras automatiskt till den nya primära noden. Redundanstiden mäts i millisekunder och den nya primära-instansen är klar att fortsätta serva begäranden.

## <a name="zone-redundant-configuration"></a>Zonen redundant konfiguration

Som standard skapas kvorum-set-repliker för lokal lagringskonfigurationer i samma datacenter. Med introduktionen av [Azure Availability Zones](../availability-zones/az-overview.md), du har möjlighet att placera olika repliker i kvorum-aktiverar till olika tillgänglighetszoner i samma region. För att ta bort en enskild felpunkt dupliceras också ringen kontroll över flera zoner som tre gateway-ringar (GW). Routning till en specifik gateway-ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonen redundant konfiguration inte skapar ytterligare databasredundans, användningen av Tillgänglighetszoner på tjänstnivåerna Premium och affärskritiska är tillgänglig utan extra kostnad. Genom att välja en zonen redundant databas, kan du din Premium- eller affärskritiska databaser elastiska till ett mycket större antal fel, inklusive oåterkalleligt datacenter-avbrott utan ändringar av programlogiken. Du kan också konvertera alla befintliga Premium eller affärskritiska databaser eller pooler till zonen redundant konfiguration.

Eftersom zonen redundant kvorum-set har repliker i olika datacenter med vissa avståndet mellan dem, kan ökad Nätverksfördröjningen öka tid som commit och därmed påverka prestandan för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till den enskilda zon konfigurationen genom att inaktivera inställningen zon redundans. Den här processen är en storlek på data igen och liknar vanliga mål för servicenivå (SLO) tjänstuppdateringen. I slutet av processen har databas eller pool migrerats från en zonen redundant ring en enskild zon ringer eller vice versa.

Zonen redundant versionen av arkitektur med hög tillgänglighet är illustreras med följande diagram:
 
![hög tillgänglighet arkitektur zonredundant](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Lässkalbarhet
Enligt beskrivningen, utnyttja Premium och affärskritisk tjänstnivåer kvorum uppsättningar och Always On-teknik för hög tillgänglighet för både i samma zon och zonen redundant konfigurationer. En av fördelarna med AlwaysOn är att replikerna alltid är i ett konsekvent tillstånd. Eftersom replikerna har samma prestandanivå som primärt, programmet kan dra nytta av den extra kapaciteten för att underhålla de skrivskyddade arbetsbelastningarna utan extra kostnad (Läs skala ut). Det här sättet skrivskyddade frågor isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Läs uppskalningsfunktionen är avsedd för de program som är logiskt avgränsade skrivskyddade arbetsbelastningar som analyser och därför kan nu använda den här ytterligare kapacitet utan att ansluta till primärt. 

Om du vill använda funktionen Lässkalning med en viss databas, måste du uttryckligen aktivera den när du skapar databasen eller efteråt genom att ändra konfigurationen med hjälp av PowerShell genom att aktivera den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdletar eller Azure Resource Manager REST API med hjälp av den [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate) metod.

När Lässkalning är aktiverade för en databas, program som ansluter till den här databasen kommer att dirigeras till Läs-och-repliken eller till en skrivskyddad replik av databasen enligt den `ApplicationIntent` egenskapen som konfigurerats i programmets anslutningssträng. Information om den `ApplicationIntent` egenskap, finns i [att ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Om Lässkalning är inaktiverad eller egenskapen ReadScale på en nivå i tjänsten stöds inte, alla anslutningar dirigeras till den skrivskyddade repliken, oberoende av den `ApplicationIntent` egenskapen.  

> [!NOTE]
> Det är möjligt att aktivera lässkalning på Standard- eller en databas för generell användning, även om det inte leder till routning den skrivskyddade avsedd session till en separat replik. Detta görs för att stödja befintliga program som kan skalas upp och ned mellan nivåerna Standard/generell användning och Premium/affärskritisk.  

Lässkalning-funktionen stöder på sessionsnivå. Om den skrivskyddade sessionen återansluts när ett anslutningsfel orsaka av repliken otillgänglighet, kan det omdirigeras till en annan replik. Men det är osannolikt, kan det resultera i att bearbetning av datauppsättningen som är inaktuella. På samma sätt, om ett program skriver data med hjälp av en Skriv-session och läser den med hjälp av skrivskyddade session omedelbart, det är möjligt att nya data inte är omedelbart synliga.

## <a name="conclusion"></a>Sammanfattning
Azure SQL Database är djupt integrerad med Azure-plattformen och är mycket beroende på Service Fabric för identifiering och återställning på Azure-Lagringsblobar för dataskydd och Tillgänglighetszoner för högre feltolerans. På samma gång använder Azure SQL-databas fullständigt Always On Availability Group-teknik från SQL Server-box-produkt för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att fullständigt nytta av fördelarna med en blandad lagringsmodell och stöd för de mest krävande serviceavtal. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Tillgänglighetszoner i Azure](../availability-zones/az-overview.md)
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Lär dig mer om [med Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
