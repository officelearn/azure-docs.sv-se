---
title: Hög tillgänglighet – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om Azure SQL Database tjänstfunktioner för hög tillgänglighet och funktioner
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 7a60d800ce76f8ff9a903cc068fa7bc87cd33f3f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700643"
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL-databas

Azure SQL Database är en databas med hög tillgänglighet plattform som en tjänst som garanterar att din databas är igång och körs 99,99% av tiden, utan att behöva bekymra dig om underhåll och stillestånd. Det här är en fullständigt hanterad SQL Server Database Engine-processen i Azure-molnet som ser till att SQL Server-databasen är alltid uppgraderas/korrigerade utan att påverka din arbetsbelastning. När en instans är uppdaterad eller växlar över, avbrottstiden är vanligtvis inte noticable om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app. Om tid att slutföra redundans är längre än 60 sekunder, ska du öppna ett supportärende. Azure SQL Database kan snabbt återställa även i de mest kritiska omständigheter som säkerställer att dina data alltid är tillgänglig.

Azure-plattformen fullständigt hanterar varje Azure SQL-databas och garanterar att inga data går förlorade och en hög andel datatillgänglighet. Azure hanterar automatiskt korrigeringar, säkerhetskopieringar, replikering, felidentifiering, underliggande potentiella maskinvara, program- eller fel, distribuera felkorrigeringar, redundans, databasen uppgraderingar och andra underhållsuppgifter. SQL Server-tekniker har implementerat MES praxis att säkerställa att alla underhållsåtgärder för har utförts i mindre än 0,01% tidpunkten för livet databas. Den här arkitekturen är utformad för att se till att allokerade data aldrig går förlorad och att underhåll utförs utan att påverka arbetsbelastning. Det finns inga underhållsperioder eller stilleståndstider som bör kräver att du stoppar arbetsbelastningen när databasen har uppgraderats eller bibehålls. Inbyggd hög tillgänglighet i Azure SQL Database garanterar att databasen blir aldrig felkritisk systemdel i din programvaruarkitektur.

Azure SQL Database är baserad på SQL Server Database Engine-arkitektur som justeras för molnmiljön för att säkerställa 99,99% tillgänglighet även i fall av infrastrukturfel. Det finns två hög tillgänglighet arkitekturmodeller som används i Azure SQL Database (lagringsintensiva säkerställer tillgänglighet på 99,99%):
- Standard/generell användning-modell som baseras på en åtskillnad mellan beräkning och lagring. Den här arkitekturen modellen förlitar sig på hög tillgänglighet och tillförlitlighet för lagringsnivån, men det kan ha vissa potentiella prestandaförsämring medan underhålls.
- Premium/kritiska affärsmodell som baseras på ett kluster med databasen sökmotor-processer. Den här arkitekturen modellen är beroende av ett faktum att det är alltid ett nodkvorum tillgängliga database engine och har minimal prestandapåverkan på din arbetsbelastning även under underhållsaktiviteter.

Azure uppgraderar och korrigerar underliggande operativsystemet, drivrutiner och SQL Server Database Engine transparent med minimalt driftstopp för slutanvändare. Azure SQL-databas som körs på den senaste stabila versionen av SQL Server Database Engine och Windows OS och de flesta användarna skulle inte lägger märke till att uppgraderingen utförs kontinuerligt.

## <a name="standardgeneral-purpose-availability"></a>Standard/generell användning tillgänglighet

Standard tillgänglighet syftar på 99,99% serviceavtal (SLA) som används för nivåerna Standard/Basic/generell användning. Hög tillgänglighet i den här arkitekturen modellen uppnås genom uppdelning av beräknings- och lager och replikeringen av data på lagringsnivå.

Följande bild visar fyra noder i arkitekturen standardmodell med avgränsas lagren för beräkning och lagring.

![Uppdelning av beräkning och lagring](media/sql-database-managed-instance/general-purpose-service-tier.png)

Det finns två nivåer i modellen standard tillgänglighet:

- En tillståndslös beräkningslager som kör sqlserver.exe processen och innehåller bara tillfälliga och cachelagrade data (till exempel – plancachen, buffertpoolen, kolumnen store pool). Den här tillståndslösa SQL Server-noden drivs av Azure Service Fabric som initierar processen, kontrollerar hälsotillståndet för noden och utför redundans till en annan plats om det behövs.
- En tillståndskänslig datalager med databasfiler (.mdf/.ldf) som lagras i Azure Premium Storage. Azure Storage garanterar att inga data går förlorade i poster som placeras i en databasfil. Azure Storage har inbyggda tillgänglighet/redundans som säkerställer att varje post i loggfilen eller sida i datafilen kommer att bevaras även om SQL Server-processen kraschar.

När databasmotorn eller operativsystemet uppgraderas, en del av underliggande den infrastrukturen misslyckas eller om vissa viktiga problem har identifierats i Sql Server-processen, tillståndslösa SQL Server-processen Azure Service Fabric flyttas till en annan tillståndslösa compute-nod. Det finns en uppsättning ledig noder som väntar på att köra nya beräkningstjänst vid redundans för att minimera tiden för växling vid fel. Data i Azure Storage-skiktet påverkas inte och data/loggfiler är kopplade till nyligen initierad SQL Server-processen. Den här processen garanterar 99,99% tillgänglighet, men det kan ha vissa prestandaeffekter på arbetsbelastning som körs på grund av övergångstid och faktumet den nya SQL Server-noden börjar med kalla cache.

## <a name="premiumbusiness-critical-availability"></a>Premium/affärskritisk tillgänglighet

Premium-tillgänglighet är aktiverat i Premium-nivån av Azure SQL Database och den är utformad för intensiva arbetsbelastningar som inte tolererar en prestandaförsämring på grund av pågående underhållsåtgärder.

I premium-modellen integreras Azure SQL-databas beräkning och lagring på en enda nod. Hög tillgänglighet i den här arkitekturen modellen uppnås genom att replikeringen av beräkning (SQL Server Database Engine-processen) och lagring (lokalt anslutna SSD) som distribuerats i 4-noders [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) kluster.

![Kluster på database engine-noder](media/sql-database-managed-instance/business-critical-service-tier.png)

Både SQL Server Database Engine-processen och underliggande mdf/ldf-filerna är placerade på samma nod med lokalt anslutna SSD-lagring som ger låg fördröjning till din arbetsbelastning. Hög tillgänglighet implementeras med hjälp av standard [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster av databasnoder med en primär databas som är tillgänglig för kunds arbetsbelastning och en tre sekundära processer som innehåller kopior av data. Den primära noden skickar ständigt ändringarna till sekundära noder för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden kraschar av någon anledning. Redundansväxling hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en ny sekundär replik skapas för att se till att tillräckligt många noder i klustret. Arbetsbelastningen omdirigeras automatiskt till den nya primära noden.

Affärskritisk kluster tillhandahåller dessutom inbyggda skrivskyddad nod som kan användas för att köra skrivskyddade frågor (till exempel rapporter) som inte påverkar prestanda hos din primära arbetsbelastning. 

## <a name="zone-redundant-configuration-preview"></a>Zonen redundant konfiguration (förhandsversion)

Som standard skapas kvorum-set-repliker för lokal lagringskonfigurationer i samma datacenter. Med introduktionen av [Azure Availability Zones](../availability-zones/az-overview.md), du har möjlighet att placera olika repliker i kvorum-aktiverar till olika tillgänglighetszoner i samma region. För att ta bort en enskild felpunkt dupliceras också ringen kontroll över flera zoner som tre gateway-ringar (GW). Routning till en specifik gateway-ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonen redundant konfiguration inte skapa ytterligare databasredundans, användningen av Tillgänglighetszoner i Premium eller affärskritisk (förhandsversion) tjänstnivåer är tillgänglig utan extra kostnad. Du kan göra din Premium genom att välja en zonen redundant databas eller affärskritisk (förhandsversion) databaser motståndskraftig mot ett mycket större antal fel, inklusive oåterkalleligt datacenter-avbrott utan ändringar av programlogiken. Du kan också konvertera alla befintliga Premium eller affärskritiska databaser eller pooler (förhandsversion) till zonen redundant konfiguration.

Eftersom zonen redundant kvorum-set har repliker i olika datacenter med vissa avståndet mellan dem, kan ökad Nätverksfördröjningen öka tid som commit och därmed påverka prestandan för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till den enskilda zon konfigurationen genom att inaktivera inställningen zon redundans. Den här processen är en storlek på data igen och liknar vanliga mål för servicenivå (SLO) tjänstuppdateringen. I slutet av processen har databas eller pool migrerats från en zonen redundant ring en enskild zon ringer eller vice versa.

> [!IMPORTANT]
> Zonen redundant databaser och elastiska pooler finns för närvarande stöds endast i Premium-tjänstnivån. Under förhandsversionen, säkerhetskopior och granska poster lagras i RA-GRS-lagring och därför inte automatiskt tillgängliga i händelse av ett avbrott på hela zonen. 

Zonen redundant versionen av arkitektur med hög tillgänglighet är illustreras med följande diagram:
 
![hög tillgänglighet arkitektur zonredundant](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Lässkalbarhet
Enligt beskrivningen, Premium och affärskritisk (förhandsversion) tjänsten nivåerna utnyttjar kvorum-uppsättningar och Always On-teknik för hög tillgänglighet för både i samma zon och zonen redundant konfigurationer. En av fördelarna med AlwaysOn är att replikerna alltid är i ett konsekvent tillstånd. Eftersom replikerna har samma prestandanivå som primärt, programmet kan dra nytta av den extra kapaciteten för att underhålla de skrivskyddade arbetsbelastningarna utan extra kostnad (Läs skala ut). Det här sättet skrivskyddade frågor isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Läs uppskalningsfunktionen är avsedd för de program som är logiskt avgränsade skrivskyddade arbetsbelastningar som analyser och därför kan nu använda den här ytterligare kapacitet utan att ansluta till primärt. 

Om du vill använda funktionen Lässkalning med en viss databas, måste du uttryckligen aktivera den när du skapar databasen eller efteråt genom att ändra konfigurationen med hjälp av PowerShell genom att aktivera den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdletar eller Azure Resource Manager REST API med hjälp av den [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate) metod.

När Lässkalning är aktiverade för en databas, program som ansluter till den här databasen kommer att dirigeras till Läs-och-repliken eller till en skrivskyddad replik av databasen enligt den `ApplicationIntent` egenskapen som konfigurerats i programmets anslutningssträng. Information om den `ApplicationIntent` egenskap, finns i [att ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Om Lässkalning är inaktiverad eller egenskapen ReadScale på en nivå i tjänsten stöds inte, alla anslutningar dirigeras till den skrivskyddade repliken, oberoende av den `ApplicationIntent` egenskapen.  

## <a name="conclusion"></a>Sammanfattning
Azure SQL Database är djupt integrerad med Azure-plattformen och är mycket beroende på Service Fabric för identifiering och återställning på Azure-Lagringsblobar för dataskydd och Tillgänglighetszoner för högre feltolerans. På samma gång använder Azure SQL-databas fullständigt Always On Availability Group-teknik från SQL Server-box-produkt för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att fullständigt nytta av fördelarna med en blandad lagringsmodell och stöd för de mest krävande serviceavtal. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Tillgänglighetszoner i Azure](../availability-zones/az-overview.md)
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Lär dig mer om [med Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
