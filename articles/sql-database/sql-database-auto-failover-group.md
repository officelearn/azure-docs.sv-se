---
title: Redundansgrupper
description: Automatisk redundansgrupper är en SQL Database-funktion som gör att du kan hantera replikering och automatisk/samordnad redundans för en grupp databaser på en SQL Database-server eller alla databaser i hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269840"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Använd grupper för automatisk redundans för att aktivera genomskinlig och samordnad redundans för flera databaser

Automatisk redundansgrupper är en SQL Database-funktion som gör att du kan hantera replikering och redundans för en grupp databaser på en SQL Database-server eller alla databaser i en hanterad instans till en annan region. Det är en deklarativ abstraktion ovanpå den befintliga [aktiva georeplikeringsfunktionen,](sql-database-active-geo-replication.md) utformad för att förenkla distribution och hantering av geo-replikerade databaser i stor skala. Du kan initiera redundans manuellt eller delegera den till SQL Database-tjänsten baserat på en användardefinierad princip. Med det senare alternativet kan du automatiskt återställa flera relaterade databaser i en sekundär region efter ett oåterkalleligt fel eller annan oplanerad händelse som resulterar i fullständig eller partiell förlust av SQL Database-tjänstens tillgänglighet i den primära regionen. En redundansk grupp kan innehålla en eller flera databaser, som vanligtvis används av samma program. Dessutom kan du använda läsbara sekundära databaser för att avlasta skrivskyddade frågearbetsbelastningar. Eftersom grupper för automatisk redundans omfattar flera databaser måste dessa databaser konfigureras på den primära servern. Grupper för automatisk redundans stöder replikering av alla databaser i gruppen till endast en sekundär server i en annan region.

> [!NOTE]
> När du arbetar med en eller flera poolade databaser på en SQL Database-server och du vill ha flera sekundärfiler i samma eller olika regioner använder du [aktiv georeplikering](sql-database-active-geo-replication.md). 

När du använder automatisk redundansgrupper med automatisk redundansprincip resulterar eventuella avbrott som påverkar en eller flera av databaserna i gruppen i automatisk redundans. Vanligtvis är dessa incidenter som inte kan mildras själv av de inbyggda automatiska åtgärder med hög tillgänglighet. Exemplen på redundansutlösare är en incident som orsakas av att en SQL-klientring eller kontrollring är nere på grund av en OS-kärnminnesläcka på flera beräkningsnoder, eller en incident som orsakas av att en eller flera klientringar är nere eftersom en felaktig nätverkskabel klipptes under avveckling av rutinmässig maskinvara.  Mer information finns i [HÖG TILLGÄNGLIGHET FÖR SQL Database](sql-database-high-availability.md).

Dessutom tillhandahåller grupper för automatisk redundansläsning läs-och skrivskyddade lyssnare som förblir oförändrade under redundans. Oavsett om du använder manuell eller automatisk redundansaktivering växlar redundansen alla sekundära databaser i gruppen till primära. När databasen har redundansen har slutförts uppdateras DNS-posten automatiskt för att omdirigera slutpunkterna till den nya regionen. För specifika RPO- och RTO-data finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md).

När du använder automatisk redundansgrupper med automatisk redundansprincip resulterar alla avbrott som påverkar databaser i SQL Database-servern eller hanterade instansen i automatisk redundans. Du kan hantera gruppen för automatisk redundans med hjälp av:

- [Azure-portal](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: Redundansk grupp](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: Redundansk grupp](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Redundansgrupp](/rest/api/sql/failovergroups).

Efter redundans kontrollerar du att autentiseringskraven för servern och databasen är konfigurerade på den nya primärinställningen. Mer information finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

För att uppnå verklig affärskontinuitet är det bara en del av lösningen att lägga till databasredundans mellan datacenter. Återställa ett program (tjänst) end-to-end efter ett oåterkalleligt fel kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klientprogramvaran (till exempel en webbläsare med ett anpassat JavaScript), webbklientänd, lagring och DNS. Det är viktigt att alla komponenter är motståndskraftiga mot samma fel och blir tillgängliga inom återställningstidsmålet (RTO) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att din tjänst fungerar under redundansen av de tjänster som den är beroende av. Mer information om hur du utformar lösningar för haveriberedskap finns i [Utforma molnlösningar för haveriberedskap Med aktiv georeplikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologi och funktioner för gruppen för automatisk redundansgrupp

- **Redundansgrupp (FOG)**

  En redundansgrupp är en namngiven grupp av databaser som hanteras av en enda SQL Database-server eller inom en enda hanterad instans som kan växla över som en enhet till en annan region om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. När det skapas för hanterade instanser innehåller en redundansgrupp alla användardatabaser i instansen och därför kan endast en redundansk grupp konfigureras på en instans.
  
  > [!IMPORTANT]
  > Namnet på redundansgruppen måste vara globalt `.database.windows.net` unikt inom domänen.

- **SQL Database-servrar**

     Med SQL Database-servrar kan vissa eller alla användardatabaser på en enda SQL Database-server placeras i en redundansk grupp. Dessutom stöder en SQL Database-server flera redundansgrupper på en enda SQL Database-server.

- **Primär**

  SQL Database-servern eller den hanterade instansen som är värd för de primära databaserna i redundansgruppen.

- **Sekundär**

  SQL Database-servern eller den hanterade instansen som är värd för de sekundära databaserna i redundansgruppen. Den sekundära kan inte vara i samma region som den primära.

- **Lägga till enskilda databaser i redundansgruppen**

  Du kan placera flera enskilda databaser på samma SQL Database-server i samma redundansgrupp. Om du lägger till en enskild databas i redundansgruppen skapas automatiskt en sekundär databas med samma utgåva och beräkningsstorlek på den sekundära servern.  Du angav den servern när redundansgruppen skapades. Om du lägger till en databas som redan har en sekundär databas i den sekundära servern ärvs den georeplikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas på en server som inte ingår i redundansgruppen skapas en ny sekundär i den sekundära servern.

  > [!IMPORTANT]
  > Kontrollera att den sekundära servern inte har en databas med samma namn om det inte är en befintlig sekundär databas. I redundansgrupper för hanterade instans replikeras alla användardatabaser. Du kan inte välja en delmängd av användardatabaser för replikering i redundansgruppen.

- **Lägga till databaser i elastisk pool i redundansgruppen**

  Du kan placera alla eller flera databaser i en elastisk pool i samma redundansgrupp. Om den primära databasen finns i en elastisk pool skapas den sekundära automatiskt i den elastiska poolen med samma namn (sekundär pool). Du måste se till att den sekundära servern innehåller en elastisk pool med exakt samma namn och tillräckligt med ledig kapacitet för att vara värd för de sekundära databaser som skapas av redundansgruppen. Om du lägger till en databas i poolen som redan har en sekundär databas i den sekundära poolen ärvs den georeplikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas på en server som inte ingår i redundansgruppen skapas en ny sekundär i den sekundära poolen.
  
- **Inledande sådd** 

  När du lägger till databaser, elastiska pooler eller hanterade instanser i en redundansgrupp finns det en inledande dirigeringsfas innan datareplikering startar. Den inledande såddfasen är den längsta och dyraste operationen. När den första seedningen är klar synkroniseras data och sedan replikeras endast efterföljande dataändringar. Hur tid det tar för det första utsöndret att slutföra beror på storleken på dina data, antalet replikerade databaser och hastigheten på länken mellan entiteterna i redundansgruppen. Under normala omständigheter är typisk såddhastighet 50-500 GB i timmen för en enda databas eller elastisk pool och 18-35 GB i timmen för en hanterad instans. Seedning utförs för alla databaser parallellt. Du kan använda den angivna såddhastigheten, tillsammans med antalet databaser och den totala storleken på data för att uppskatta hur lång tid den inledande dirigeringsfasen tar innan datareplikeringen startar.

  För hanterade instanser måste hastigheten på expressruttlänken mellan de två instanserna också beaktas när man beräknar tiden för den inledande såddfasen. Om hastigheten på kopplingen mellan de två instanserna är långsammare än vad som är nödvändigt, påverkas sannolikt tiden till utsädet märkbart. Du kan använda den angivna såddhastigheten, antalet databaser, den totala storleken på data och länkhastigheten för att uppskatta hur lång tid den inledande dirigeringen tar innan datareplikeringen startar. För en enda 100 GB-databas skulle till exempel den inledande fröfasen ta allt från 2,8 till 5,5 timmar om länken kan trycka på 35 GB per timme. Om länken bara kan överföra 10 GB per timme tar det cirka 10 timmar att dirigera en 100 GB-databas. Om det finns flera databaser att replikera kommer sådd att utföras parallellt, och i kombination med en långsam länkhastighet kan den inledande såddfasen ta betydligt längre tid, särskilt om parallell sådd av data från alla databaser överstiger den tillgängliga länkbandbredd. Om nätverksbandbredden mellan två instanser är begränsad och du lägger till flera hanterade instanser i en redundanskationsgrupp kan du lägga till flera hanterade instanser i redundansgruppen sekventiellt, en efter en.

  
- **DNS-zon**

  Ett unikt ID som genereras automatiskt när en ny instans skapas. Ett SAN-certifikat (multi domain) för den här instansen har etablerats för att autentisera klientanslutningarna till alla instanser i samma DNS-zon. De två hanterade instanserna i samma redundansgrupp måste dela DNS-zonen.
  
  > [!NOTE]
  > Ett DNS-zon-ID krävs inte för redundansgrupper som skapats för SQL Database-servrar.

- **Läs-skriv-lyssnare för redundansgrupp**

  En DNS CNAME-post som pekar på den aktuella primärns URL. Den skapas automatiskt när redundansgruppen skapas och gör att läs-skriv-SQL-arbetsbelastningen kan återansluta till den primära databasen när den primära ändringarna efter redundans. När redundansgruppen skapas på en SQL Database-server bildas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.database.windows.net`. När redundansgruppen skapas på en hanterad instans bildas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.zone_id.database.windows.net`.

- **Skrivskyddad lyssnare för redundansgrupp**

  En DNS CNAME-post har skapats som pekar på den skrivskyddade lyssnaren som pekar på den sekundäras URL. Den skapas automatiskt när redundansgruppen skapas och gör att den skrivskyddade SQL-arbetsbelastningen kan anslutas transparent till den sekundära med hjälp av de angivna belastningsutjämningsreglerna. När redundansgruppen skapas på en SQL Database-server bildas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.secondary.database.windows.net`. När redundansgruppen skapas på en hanterad instans bildas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.zone_id.secondary.database.windows.net`.

- **Automatisk redundansprincip**

  Som standard konfigureras en redundansgrupp med en automatisk redundansprincip. SQL Database-tjänsten utlöser redundans efter att felet har upptäckts och respitperioden har upphört att gälla. Systemet måste kontrollera att avbrottet inte kan mildras av den inbyggda infrastrukturen för hög tillgänglighet för [SQL Database-tjänsten](sql-database-high-availability.md) på grund av effektens omfattning. Om du vill styra redundansarbetsflödet från programmet kan du inaktivera automatisk redundans.
  
  > [!NOTE]
  > Eftersom verifiering av avbrottets omfattning och hur snabbt det kan mildras innebär mänskliga åtgärder av operationsteamet, kan respitperioden inte anges under en timme.  Den här begränsningen gäller för alla databaser i redundansgruppen oavsett datasynkroniseringstillstånd. 

- **Meddssendeprincip för redundans**

  Som standard är redundansen för den skrivskyddade lyssnaren inaktiverad. Det säkerställer att prestanda för den primära inte påverkas när den sekundära är offline. Men det innebär också att skrivskyddade sessioner inte kommer att kunna ansluta förrän den sekundära återställs. Om du inte kan tolerera driftstopp för skrivskyddade sessioner och är OK att tillfälligt använda den primära för både skrivskyddad och skrivskyddad trafik på bekostnad av `AllowReadOnlyFailoverToPrimary` den potentiella prestandaförsämringen av den primära, kan du aktivera redundans för den skrivskyddade lyssnaren genom att konfigurera egenskapen. I så fall omdirigeras skrivskyddad trafik automatiskt till den primära om den sekundära inte är tillgänglig.

- **Planerad redundans**

   Planerad redundans utför fullständig synkronisering mellan primära och sekundära databaser innan den sekundära växlar till den primära rollen. Detta garanterar ingen dataförlust. Planerad redundans används i följande scenarier:

  - Utför DR-borrmaskiner (Disaster Recovery) när dataförlusten inte är acceptabel
  - Flytta databaserna till en annan region
  - Returnera databaserna till den primära regionen efter att avbrottet har mildrats (återställning efter fel).

- **Oplanerad redundans**

   Oplanerad eller påtvingad redundans växlar omedelbart sekundärt till den primära rollen utan synkronisering med den primära. Den här åtgärden resulterar i dataförlust. Oplanerad redundans används som återställningsmetod vid avbrott när den primära inte är tillgänglig. När den ursprungliga primärt är online igen återansluts den automatiskt utan synkronisering och blir en ny sekundär.

- **Manuell redundans**

  Du kan när som helst initiera redundans manuellt oavsett den automatiska redundanskonfigurationen. Om automatisk redundansprincip inte har konfigurerats krävs manuell redundans för att återställa databaser i redundansgruppen till den sekundära. Du kan initiera påtvingad eller vänskaplig redundans (med fullständig datasynkronisering). Det senare kan användas för att flytta primärt till den sekundära regionen. När redundansen är klar uppdateras DNS-posterna automatiskt för att säkerställa anslutning till den nya primära

- **Respitperiod med dataförlust**

  Eftersom de primära och sekundära databaserna synkroniseras med asynkron replikering kan redundansen leda till dataförlust. Du kan anpassa den automatiska redundansprincipen så att den återspeglar programmets tolerans mot dataförlust. Genom att `GracePeriodWithDataLossHours`konfigurera kan du styra hur länge systemet väntar innan du påbörjar redundansen som sannolikt kommer att resultera i dataförlust.

- **Flera redundansgrupper**

  Du kan konfigurera flera redundansgrupper för samma serverpar för att styra omfattningen av redundans. Varje grupp växlar över oberoende av varandra. Om ditt program med flera innehavare använder elastiska pooler kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. På så sätt kan du minska effekten av ett avbrott till endast hälften av klienterna.

  > [!NOTE]
  > Hanterad instans stöder inte flera redundansgrupper.
  
## <a name="permissions"></a>Behörigheter

Behörigheter för en redundansgrupp hanteras via [rollbaserad åtkomstkontroll (RBAC).](../role-based-access-control/overview.md) [Rollen SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) har alla nödvändiga behörigheter för att hantera redundansgrupper.

### <a name="create-failover-group"></a>Skapa redundansgrupp

Om du vill skapa en redundansgrupp behöver du RBAC-skrivåtkomst till både primära och sekundära servrar och till alla databaser i redundansgruppen. För en hanterad instans behöver du RBAC-skrivbehörighet till både den primära och sekundära hanterade instansen, men behörigheter för enskilda databaser är inte relevanta eftersom enskilda hanterade instansdatabaser inte kan läggas till eller tas bort från en redundanskialgrupp. 

### <a name="update-a-failover-group"></a>Uppdatera en redundansgrupp

Om du vill uppdatera en redundansgrupp behöver du RBAC-skrivåtkomst till redundansgruppen och alla databaser på den aktuella primära servern eller hanterade instansen.  

### <a name="failover-a-failover-group"></a>Redundans en redundansgrupp

För att växla över en redundansgrupp behöver du RBAC-skrivåtkomst till redundansgruppen på den nya primära servern eller hanterad instans.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Metodtips för att använda redundansk grupper med enskilda databaser och elastiska pooler

Gruppen automatisk redundans måste konfigureras på den primära SQL Database-servern och ansluta den till den sekundära SQL Database-servern i en annan Azure-region. Grupperna kan inkludera alla eller vissa databaser på dessa servrar. Följande diagram illustrerar en typisk konfiguration av ett geouppsagt molnprogram med hjälp av flera databaser och grupp för automatisk redundans.

![automatisk redundans](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Se [Lägga till en enskild databas i en redundansgrupp](sql-database-single-database-failover-group-tutorial.md) för en detaljerad steg-för-steg-självstudiekurs som lägger till en enskild databas i en redundanskialgrupp.

När du utformar en tjänst med affärskontinuitet i åtanke följer du dessa allmänna riktlinjer:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Använda en eller flera redundansgrupper för att hantera redundans för flera databaser

En eller flera redundansgrupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. Redundansgruppen skapar geo-sekundär databas med samma servicemål som primärt. Om du lägger till en befintlig geo-replikeringsrelation i redundansgruppen kontrollerar du att den geografiska sekundärt är konfigurerad med samma tjänstnivå och beräkningsstorlek som primär.
  
> [!IMPORTANT]
> Det stöds för närvarande inte att skapa redundanskationsgrupper mellan två servrar i olika prenumerationer för enskilda databaser och elastiska pooler. Om du flyttar den primära eller sekundära servern till en annan prenumeration efter att redundansgruppen har skapats kan det leda till fel på redundansbegäranden och andra åtgärder.

### <a name="using-read-write-listener-for-oltp-workload"></a>Använda läs-skriv-lyssnare för OLTP-arbetsbelastning

När du utför OLTP-åtgärder, använd `<fog-name>.database.windows.net` som serverns URL och anslutningarna automatiskt riktas till den primära. Den här URL:en ändras inte efter redundansen. Redundans innebär att DNS-posten uppdateras så att klientanslutningarna omdirigeras till den nya primära först efter att klient-DNS-cachen har uppdaterats.

### <a name="using-read-only-listener-for-read-only-workload"></a>Använda skrivskyddad lyssnare för skrivskyddad arbetsbelastning

Om du har en logiskt isolerad skrivskyddad arbetsbelastning som är tolerant mot vissa inaktuella data, kan du använda den sekundära databasen i programmet. För skrivskyddade sessioner `<fog-name>.secondary.database.windows.net` använder du som server-URL och anslutningen dirigeras automatiskt till den sekundära. Vi rekommenderar också att du i anslutningssträngen anger avsikt med att använda `ApplicationIntent=ReadOnly`. Om du vill vara säker på att den skrivskyddade arbetsbelastningen kan återansluta `AllowReadOnlyFailoverToPrimary` efter redundans eller om den sekundära servern kopplas från, måste du konfigurera egenskapen för redundansprincipen.

### <a name="preparing-for-performance-degradation"></a>Förberedelser för prestandaförsämring

Ett typiskt Azure-program använder flera Azure-tjänster och består av flera komponenter. Den automatiska redundansen för redundansgruppen utlöses baserat på tillståndet azure SQL-komponenterna ensam. Andra Azure-tjänster i den primära regionen kanske inte påverkas av avbrottet och deras komponenter kan fortfarande vara tillgängliga i den regionen. När de primära databaserna växlar till DR-regionen kan svarstiden mellan de beroende komponenterna öka. För att undvika att högre latens påverkar programmets prestanda, se till att alla programmets komponenter i DR-regionen redundans redundans och följa dessa [riktlinjer för nätverkssäkerhet](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Förbereda för dataförlust

Om ett avbrott upptäcks väntar SQL på den `GracePeriodWithDataLossHours`period som du angav av . Standardvärdet är 1 timme. Om du inte har råd med `GracePeriodWithDataLossHours` dataförlust, se till att ställa in på ett tillräckligt stort antal, till exempel 24 timmar. Använd manuell grupp redundans för att växla tillbaka från sekundärt till den primära.

> [!IMPORTANT]
> Elastiska pooler med 800 eller färre DTUs och mer än 250 databaser med geo-replikering kan stöta på problem, inklusive längre planerade redundans och försämrade prestanda.  Dessa problem är mer benägna att uppstå för skrivintensiva arbetsbelastningar, när geo-replikeringslutpunkter är allmänt åtskilda av geografi, eller när flera sekundära slutpunkter används för varje databas.  Symptom på dessa problem indikeras när geo-replikeringsfördröjningen ökar med tiden.  Denna fördröjning kan övervakas med [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om dessa problem uppstår, då mildrande åtgärder inkluderar att öka antalet pool DTUs, eller minska antalet geo-replikerade databaser i samma pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Ändra sekundär region i redundansgruppen

För att illustrera ändringssekvensen antar vi att server A är den primära servern, server B är den befintliga sekundära servern och server C är den nya sekundära i den tredje regionen.  Så här gör du övergången:

1.  Skapa ytterligare sekundärer för varje databas på server A till server C med hjälp av [aktiv geo-replikering](sql-database-active-geo-replication.md). Varje databas på server A kommer att ha två sekundärer, en på server B och en på server C. Detta garanterar att de primära databaserna förblir skyddade under övergången.
2.  Ta bort redundansgruppen. Vid denna punkt inloggningar kommer att misslyckas. Detta beror på att SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte kommer att känna igen redundansgruppnamnet.
3.  Återskapa redundansgruppen med samma namn mellan servrarna A och C. Vid denna punkt inloggningarna kommer att sluta misslyckas.
4.  Lägg till alla primära databaser på server A i den nya redundansgruppen.
5.  Släpp server B. Alla databaser på B kommer att raderas automatiskt. 


### <a name="changing-primary-region-of-the-failover-group"></a>Ändra primär region i redundansgruppen

För att illustrera ändringssekvensen antar vi att server A är den primära servern, server B är den befintliga sekundära servern och server C är den nya primärt i den tredje regionen.  Så här gör du övergången:

1.  Utför en planerad redundans för att växla den primära servern till B. Server A blir den nya sekundära servern. Redundansen kan resultera i flera minuters driftstopp. Den faktiska tiden beror på storleken på redundansgruppen.
2.  Skapa ytterligare sekundärer för varje databas på server B till server C med hjälp av [aktiv geo-replikering](sql-database-active-geo-replication.md). Varje databas på server B kommer att ha två sekundärer, en på server A och en på server C. Detta garanterar att de primära databaserna förblir skyddade under övergången.
3.  Ta bort redundansgruppen. Vid denna punkt inloggningar kommer att misslyckas. Detta beror på att SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte kommer att känna igen redundansgruppnamnet.
4.  Återskapa redundansgruppen med samma namn mellan servrarna A och C. Vid denna punkt inloggningarna kommer att sluta misslyckas.
5.  Lägg till alla primära databaser på B i den nya redundansgruppen. 
6.  Utför en planerad redundansväxling för redundansgruppen för att växla B och C. Nu kommer server C att bli den primära och B - den sekundära. Alla sekundära databaser på server A länkas automatiskt till primärfärgerna på C. Precis som i steg 1 kan redundansen resultera i flera minuters driftstopp.
6.  Släpp servern A. Alla databaser på A tas bort automatiskt.

> [!IMPORTANT]
> När redundansgruppen tas bort tas även DNS-posterna för lyssnarens slutpunkter bort. Då är sannolikheten att någon annan skapar en redundansgrupp eller serveralias med samma namn, vilket hindrar dig från att använda den igen. Använd inte allmänna redundansk gruppnamn för att minimera risken.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Metodtips för att använda redundansk grupper med hanterade instanser

Gruppen för automatisk redundans måste konfigureras på den primära instansen och ansluta den till den sekundära instansen i en annan Azure-region.  Alla databaser i instansen replikeras till den sekundära instansen.

Följande diagram illustrerar en typisk konfiguration av ett geouppsagt molnprogram med hanterad instans och automatisk redundansgrupp.

![automatisk redundans](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Se [Lägga till hanterad instans i en redundanskialgrupp](sql-database-managed-instance-failover-group-tutorial.md) för en detaljerad steg-för-steg-självstudiekurs som lägger till en hanterad instans för att använda redundansgrupp.

Om ditt program använder hanterad instans som datanivå följer du dessa allmänna riktlinjer när du utformar för affärskontinuitet:

### <a name="creating-the-secondary-instance"></a>Skapa den sekundära instansen 

För att säkerställa icke-avbruten anslutning till den primära instansen efter redundans måste både primära och sekundära instanser finnas i samma DNS-zon. Det garanterar att samma SAN-certifikat (multi-domain) kan användas för att autentisera klientanslutningarna till någon av de två instanserna i redundansgruppen. När programmet är klart för produktionsdistribution skapar du en sekundär instans i en annan region och kontrollerar att dns-zonen delars med den primära instansen. Du kan göra det `DNS Zone Partner` genom att ange en valfri parameter med Azure-portalen, PowerShell eller REST API.

> [!IMPORTANT]
> Första instansen som skapas i undernätet bestämmer DNS-zonen för alla efterföljande instanser i samma undernät. Det innebär att två instanser från samma undernät inte kan tillhöra olika DNS-zoner.

Mer information om hur du skapar den sekundära instansen i samma DNS-zon som den primära instansen finns i [Skapa en sekundär hanterad instans](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Aktivera replikeringstrafik mellan två instanser

Eftersom varje instans är isolerad i sitt eget virtuella nätverk måste tvåriktad trafik mellan dessa virtuella nätverk tillåtas. Se [Azure VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Skapa en redundansgrupp mellan hanterade instanser i olika prenumerationer

Du kan skapa en redundansgrupp mellan hanterade instanser i två olika prenumerationer. När du använder PowerShell API kan `PartnerSubscriptionId` du göra det genom att ange parametern för den sekundära instansen. När du använder REST API kan `properties.managedInstancePairs` varje instans-ID som ingår i parametern ha ett eget subscriptionID.
  
> [!IMPORTANT]
> Azure-portalen stöder inte skapandet av redundansgrupper för olika prenumerationer. För befintliga redundansgrupper för olika prenumerationer och/eller resursgrupper kan redundans initieras inte manuellt via portalen från den primära instansen. Initiera den från den geo-sekundära instansen i stället.

### <a name="managing-failover-to-secondary-instance"></a>Hantera redundans till sekundär instans

Redundansgruppen hanterar redundansen för alla databaser i instansen. När en grupp skapas kommer varje databas i instansen automatiskt att georeperas till den sekundära instansen. Du kan inte använda redundansklar för att initiera en partiell redundans av en delmängd av databaserna.

> [!IMPORTANT]
> Om en databas tas bort från den primära instansen tas den också bort automatiskt på den geografiska sekundära instansen.

### <a name="using-read-write-listener-for-oltp-workload"></a>Använda läs-skriv-lyssnare för OLTP-arbetsbelastning

När du utför OLTP-åtgärder, använd `<fog-name>.zone_id.database.windows.net` som serverns URL och anslutningarna automatiskt riktas till den primära. Den här URL:en ändras inte efter redundansen. Redundansen innebär att DNS-posten uppdateras, så klientanslutningarna omdirigeras till den nya primära först när klient-DNS-cachen har uppdaterats. Eftersom den sekundära instansen delar DNS-zonen med den primära, kan klientprogrammet återansluta till den med samma SAN-certifikat.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Använda skrivskyddad lyssnare för att ansluta till den sekundära instansen

Om du har en logiskt isolerad skrivskyddad arbetsbelastning som är tolerant mot vissa inaktuella data, kan du använda den sekundära databasen i programmet. Om du vill ansluta direkt till `server.secondary.zone_id.database.windows.net` den georepnadda sekundären använder du som server-URL och anslutningen görs direkt till den georep replikerade sekundäran.

> [!NOTE]
> På vissa tjänstnivåer stöder Azure SQL Database användningen av [skrivskyddade repliker](sql-database-read-scale-out.md) för att läsa upp arbetsbelastningar `ApplicationIntent=ReadOnly` med skrivskydd med hjälp av kapaciteten för en skrivskyddad replik och med parametern i anslutningssträngen. När du har konfigurerat en geo-replikerad sekundär kan du använda den här funktionen för att ansluta till antingen en skrivskyddad replik på den primära platsen eller på den georepnaderade platsen.
> - Om du vill ansluta till en skrivskyddad replik på den primära platsen använder du `<fog-name>.zone_id.database.windows.net`.
> - Om du vill ansluta till en skrivskyddad replik på den sekundära platsen använder du `<fog-name>.secondary.zone_id.database.windows.net`.

### <a name="preparing-for-performance-degradation"></a>Förberedelser för prestandaförsämring

Ett typiskt Azure-program använder flera Azure-tjänster och består av flera komponenter. Den automatiska redundansen för redundansgruppen utlöses baserat på tillståndet azure SQL-komponenterna ensam. Andra Azure-tjänster i den primära regionen kanske inte påverkas av avbrottet och deras komponenter kan fortfarande vara tillgängliga i den regionen. När de primära databaserna växlar till DR-regionen kan svarstiden mellan de beroende komponenterna öka. För att undvika att högre latens påverkar programmets prestanda, se till att alla programmets komponenter i DR-regionen redundans redundans och följa dessa [riktlinjer för nätverkssäkerhet](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Förbereda för dataförlust

Om ett avbrott upptäcks utlöser SQL automatiskt redundans om det inte finns någon dataförlust så vitt vi vet. Annars väntar den på den period `GracePeriodWithDataLossHours`som du har angett av . Om du `GracePeriodWithDataLossHours`har angett ska du förberedas för dataförlust. I allmänhet gynnar Azure tillgänglighet under avbrott. Om du inte har råd med dataförlust, se till att ställa graceperiodwithDataLossHours till ett tillräckligt stort antal, till exempel 24 timmar.

DNS-uppdateringen av läs-skriv-lyssnaren sker omedelbart efter att redundansen har initierats. Den här åtgärden resulterar inte i dataförlust. Processen att byta databasroller kan dock ta upp till 5 minuter under normala förhållanden. Tills den är klar kommer vissa databaser i den nya primära instansen fortfarande att vara skrivskyddade. Om redundans initieras med PowerShell är hela åtgärden synkron. Om det initieras med Azure-portalen anger användargränssnittet slutförandestatus. Om det initieras med REST API använder du standard Azure Resource Manager avsökningsmekanism för att övervaka för slutförande.

> [!IMPORTANT]
> Använd manuell gruppväxling för att flytta tillbaka primärfärger till den ursprungliga platsen. När avbrottet som orsakade redundansen mildras kan du flytta dina primära databaser till den ursprungliga platsen. För att göra det bör du initiera den manuella redundansen för gruppen.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Ändra sekundär region i redundansgruppen

Anta att instans A är den primära instansen, instans B är den befintliga sekundära instansen och instans C är den nya sekundära instansen i den tredje regionen.  Så här gör du övergången:

1.  Skapa instans C med samma storlek som A och i samma DNS-zon. 
2.  Ta bort redundansgruppen mellan instanserna A och B. Nu misslyckas inloggningarna eftersom SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte känner igen redundansgruppnamnet. De sekundära databaserna kopplas bort från primärfärgerna och blir lässkrivande databaser. 
3.  Skapa en redundanskialgrupp med samma namn mellan instans A och C. Följ instruktionerna i [redundansgruppen med självstudiekurs för hanterade instanser](sql-database-managed-instance-failover-group-tutorial.md). Detta är en storlek-of-data-åtgärd och slutförs när alla databaser från instans A dirigeras och synkroniseras.
4.  Ta bort instans B om det inte behövs för att undvika onödiga avgifter.

> [!NOTE]
> Efter steg 2 och tills steg 3 är klar förblir databaserna i instans A oskyddade från ett katastrofalt fel på instans A.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändra primär region i redundansgruppen

Låt oss anta att instans A är den primära instansen, instans B är den befintliga sekundära instansen och instans C är den nya primära instansen i den tredje regionen.  Så här gör du övergången:

1.  Skapa instans C med samma storlek som B och i samma DNS-zon. 
2.  Anslut till instans B och manuellt redundans för att växla den primära instansen till B. Instans A blir den nya sekundära instansen automatiskt.
3.  Ta bort redundansgruppen mellan instanserna A och B. Nu misslyckas inloggningarna eftersom SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte känner igen redundansgruppnamnet. De sekundära databaserna kopplas bort från primärfärgerna och blir lässkrivande databaser. 
4.  Skapa en redundanskialgrupp med samma namn mellan förekomst A och C. Följ instruktionerna i [redundansgruppen med självstudiekurs för hanterade instanser](sql-database-managed-instance-failover-group-tutorial.md). Detta är en storlek-of-data-åtgärd och slutförs när alla databaser från instans A dirigeras och synkroniseras.
5.  Ta bort instans A om det inte behövs för att undvika onödiga avgifter.

> [!NOTE] 
> Efter steg 3 och tills steg 4 är klar förblir databaserna i instans A oskyddade från ett katastrofalt fel på instans A.

> [!IMPORTANT]
> När redundansgruppen tas bort tas även DNS-posterna för lyssnarens slutpunkter bort. Då är sannolikheten att någon annan skapar en redundansgrupp eller serveralias med samma namn, vilket hindrar dig från att använda den igen. Använd inte allmänna redundansk gruppnamn för att minimera risken.

## <a name="failover-groups-and-network-security"></a>Redundansgrupper och nätverkssäkerhet

För vissa program kräver säkerhetsreglerna att nätverksåtkomsten till datanivån är begränsad till en eller flera specifika komponenter, till exempel en virtuell dator, webbtjänst osv. Detta krav innebär vissa utmaningar för utformningen av kontinuiteten i verksamheten och användningen av redundansgrupperna. Tänk på följande alternativ när du implementerar sådan begränsad åtkomst.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Använda redundansgrupper och virtuella nätverksregler

Om du använder slutpunkter och regler för [tjänsten Virtuellt nätverk för](sql-database-vnet-service-endpoint-rule-overview.md) att begränsa åtkomsten till SQL-databasen bör du vara medveten om att varje slutpunkt för tjänsten För virtuellt nätverk gäller endast en Azure-region. Slutpunkten gör det inte möjligt för andra regioner att acceptera kommunikation från undernätet. Därför kan endast klientprogram som distribueras i samma region ansluta till den primära databasen. Eftersom redundansresultatet resulterar i att SQL-klientsessionerna omdirigeras till en server i en annan (sekundär) region, misslyckas dessa sessioner om de kommer från en klient utanför den regionen. Därför kan den automatiska redundansprincipen inte aktiveras om de deltagande servrarna ingår i reglerna för virtuellt nätverk. Så här stöder du manuell redundans:

1. Etablera överflödiga kopior av klientkomponenterna i ditt program (webbtjänst, virtuella datorer etc.) i den sekundära regionen
2. Konfigurera [de virtuella nätverksreglerna](sql-database-vnet-service-endpoint-rule-overview.md) individuellt för primär och sekundär server
3. Aktivera [redundans för fronten med hjälp av en Traffic Manager-konfiguration](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Initiera manuell redundans när avbrottet upptäcks. Det här alternativet är optimerat för program som kräver konsekvent svarstid mellan frontend och datanivån och stöder återställning när antingen frontend, datanivå eller båda påverkas av avbrottet.

> [!NOTE]
> Om du använder **den skrivskyddade lyssnaren** för att läsa en skrivskyddad arbetsbelastning kontrollerar du att den här arbetsbelastningen körs i en virtuell dator eller annan resurs i den sekundära regionen så att den kan ansluta till den sekundära databasen.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Använda redundansgrupper och SQL-databasbrandväggsregler

Om din affärskontinuitetsplan kräver redundans med hjälp av grupper med automatisk redundans kan du begränsa åtkomsten till SQL-databasen med hjälp av de traditionella brandväggsreglerna. Så här stöder du automatisk redundans:

1. [Skapa en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Skapa en offentlig belastningsutjämnare](../load-balancer/quickstart-load-balancer-standard-public-portal.md) och tilldela den offentliga IP-adressen till den.
3. [Skapa ett virtuellt nätverk och de virtuella datorerna](../load-balancer/quickstart-load-balancer-standard-public-portal.md) för frontend-komponenter
4. [Skapa nätverkssäkerhetsgrupp](../virtual-network/security-overview.md) och konfigurera inkommande anslutningar.
5. Kontrollera att de utgående anslutningarna är öppna för Azure SQL-databas med hjälp av [Sql-tjänsttaggen](../virtual-network/security-overview.md#service-tags).
6. Skapa en [SQL-databasbrandväggsregel](sql-database-firewall-configure.md) för att tillåta inkommande trafik från den offentliga IP-adress som du skapar i steg 1.

Mer information om hur du konfigurerar utgående åtkomst och vilken IP som ska användas i brandväggsreglerna finns i Utgående anslutningar för [belastningsutjämnarutgående](../load-balancer/load-balancer-outbound-connections.md).

Ovanstående konfiguration säkerställer att den automatiska redundansen inte blockerar anslutningar från klientkomponenterna och förutsätter att programmet kan tolerera den längre svarstiden mellan klientdelen och datanivån.

> [!IMPORTANT]
> För att garantera kontinuitet i verksamheten för regionala avbrott måste du säkerställa geografisk redundans för både frontend-komponenter och databaser.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivera georeplikering mellan hanterade instanser och deras virtuella nätverk

När du ställer in en redundansgrupp mellan primära och sekundära hanterade instanser i två olika regioner isoleras varje instans med hjälp av ett oberoende virtuellt nätverk. Så här tillåter du replikeringstrafik mellan dessa virtuella nätverk att dessa förutsättningar uppfylls:

1. De två hanterade instanserna måste finnas i olika Azure-regioner.
2. De två hanterade instanserna måste vara samma tjänstnivå och ha samma lagringsstorlek.
3. Den sekundära hanterade instansen måste vara tom (inga användardatabaser).
4. De virtuella nätverk som används av hanterade instanser måste anslutas via en [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [Express Route](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). När två virtuella nätverk ansluter via ett lokalt nätverk, se till att det inte finns någon brandväggsregel som blockerar portarna 5022 och 11000-11999. Global VNet-peering stöds inte.
5. De två hanterade instansen virtuella nätverk kan inte ha överlappande IP-adresser.
6. Du måste konfigurera dina NSG (Network Security Groups) så att portarna 5022 och intervallet 11000~12000 är öppna inkommande och utgående för anslutningar från undernätet för den andra hanterade instansen. Detta för att tillåta replikeringstrafik mellan instanserna.

   > [!IMPORTANT]
   > Felkonfigurerade NSG-säkerhetsregler leder till fastnat databaskopieringsåtgärder.

7. Den sekundära instansen konfigureras med rätt DNS-zon-ID. DNS-zonen är en egenskap för en hanterad instans och virtuellt kluster och dess ID ingår i värdnamnsadressen. Zon-ID genereras som en slumpmässig sträng när den första hanterade instansen skapas i varje virtuella nätverk och samma ID tilldelas alla andra instanser i samma undernät. När DNS-zonen har tilldelats kan den inte ändras. Hanterade instanser som ingår i samma redundansgrupp måste dela DNS-zonen. Du åstadkommer detta genom att skicka den primära instansens zon-ID som värdet för Parametern DnsZonePartner när du skapar den sekundära instansen. 

   > [!NOTE]
   > En detaljerad självstudiekurs om hur du konfigurerar redundanskialgrupper med hanterad instans finns [i Lägga till en hanterad instans i en redundanskialgrupp](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräkningsstorlek (inom samma tjänstnivå, inte mellan allmänt syfte och affärskritisk) utan att koppla från sekundära databaser. Vid uppgradering rekommenderar vi att du uppgraderar alla sekundära databaser först och sedan uppgraderar den primära. När du nedgraderar, återför ordningen: nedgradera den primära först och nedgradera sedan alla sekundära databaser. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå tillämpas den här rekommendationen.

Den här sekvensen rekommenderas specifikt för att undvika problemet där sekundärt vid en lägre SKU blir överbelastad och måste dirigeras om under en uppgraderings- eller nedgraderingsprocess. Du kan också undvika problemet genom att göra den primära skrivskyddade, på bekostnad av att påverka alla läs-skriv arbetsbelastningar mot den primära.

> [!NOTE]
> Om du skapade sekundär databas som en del av redundansgruppskonfigurationen rekommenderas det inte att nedgradera den sekundära databasen. Detta för att säkerställa att din datanivå har tillräcklig kapacitet för att bearbeta din vanliga arbetsbelastning när redundans har aktiverats.

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svarstiden för stora nätverk använder kontinuerlig kopia en asynkron replikeringsmekanism. Asynkron replikering gör vissa dataförlust oundvikliga om ett fel inträffar. Vissa program kan dock inte kräva någon dataförlust. För att skydda dessa viktiga uppdateringar kan en programutvecklare anropa [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systemproceduren omedelbart efter att transaktionen har begåtts. Anropa `sp_wait_for_database_copy_sync` blockerar anropande tråden tills den senast genomförda transaktionen har överförts till den sekundära databasen. Det väntar dock inte på att de överförda transaktionerna ska spelas upp och genomföras på den sekundära. `sp_wait_for_database_copy_sync`begränsad till en specifik kontinuerlig kopieringslänk. Alla användare med anslutningsrättigheter till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> `sp_wait_for_database_copy_sync`förhindrar dataförlust efter redundans, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen som `sp_wait_for_database_copy_sync` orsakas av ett proceduranrop kan vara betydande och beror på storleken på transaktionsloggen vid tidpunkten för samtalet.

## <a name="failover-groups-and-point-in-time-restore"></a>Redundansgrupper och återställning i tid

Information om hur du använder point-in-time-återställning med redundansk grupper finns [i Pitr (Point in Time Recovery).](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="limitations-of-failover-groups"></a>Begränsningar för redundansgrupper

Var medveten om följande begränsningar:

- Redundansgrupp kan inte skapas mellan två servrar eller instanser i samma Azure-regioner.
- Redundansgruppen kan inte byta namn. Du måste ta bort gruppen och återskapa den med ett annat namn. 
- Databasbyte stöds inte för instanser i redundansgruppen. Du måste tillfälligt ta bort redundansgruppen för att kunna byta namn på en databas.

## <a name="programmatically-managing-failover-groups"></a>Programmässigt hantera redundansgrupper

Som tidigare diskuterats kan grupper med automatisk redundans och aktiv georeplikering också hanteras programmässigt med Azure PowerShell och REST API. I följande tabeller beskrivs den uppsättning kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager API:er för hantering, inklusive [AZURE SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) och Azure [PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Dessa API:er kräver användning av resursgrupper och stöd för rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomstroller finns i [Azure Role-Based Access Control](../role-based-access-control/overview.md).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Hantera växlings över SQL-databas med enskilda databaser och elastiska pooler

| Cmdlet | Beskrivning |
| --- | --- |
| [Nya-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [Ta bort-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Tar bort en redundansgrupp från servern |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar en redundansgrupps konfiguration |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändrar konfigurationen för en redundansgrupp |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Utlöser redundans för en redundansgrupp till den sekundära servern |
| [Tillägg-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Lägger till en eller flera databaser i en redundansgrupp|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Hantera växlingsgrupper för SQL-databas med hanterade instanser

| Cmdlet | Beskrivning |
| --- | --- |
| [Nya-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära instanser|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Ändrar konfigurationen för en redundansgrupp|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Hämtar en redundansgrupps konfiguration|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Utlöser redundans för en redundansgrupp till den sekundära instansen|
| [Ta bort-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Tar bort en redundansgrupp|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Hantera växlings över SQL-databas med enskilda databaser och elastiska pooler

| Kommando | Beskrivning |
| --- | --- |
| [az sql failover-grupp skapa](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [az sql failover-grupp ta bort](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Tar bort en redundansgrupp från servern |
| [az sql failover-grupp show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hämtar en redundansk gruppkonfiguration |
| [uppdatering av az sql-redundansgrupp](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändrar konfigurationen av en redundansgrupp och/eller lägger till en eller flera databaser i en redundansgrupp|
| [az sql failover-grupp set-primär](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Utlöser redundans för en redundansgrupp till den sekundära servern |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Hantera växlingsgrupper för SQL-databas med hanterade instanser

| Kommando | Beskrivning |
| --- | --- |
| [az sql instans-failover-grupp skapa](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära instanser |
| [az sql instans-redundansgruppsuppdatering](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Ändrar konfigurationen för en redundansgrupp|
| [az sql-instans-redundansgruppsshow](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Hämtar en redundansgrupps konfiguration|
| [az sql-instans-redundansgruppsuppsättning-primär](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Utlöser redundans för en redundansgrupp till den sekundära instansen|
| [az sql instans-failover-grupp ta bort](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Tar bort en redundansgrupp |

* * *

> [!IMPORTANT]
> Ett exempelskript finns i [Konfigurera och redundans en redundansgrupp för en enskild databas](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Hantera redundansgrupper för SQL-databaser med enstaka och poolade databaser

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupp |
| [Ta bort redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Tar bort en redundansgrupp från servern |
| [Redundans (planerad)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Utlöser redundans från den aktuella primära servern till den sekundära servern med fullständig datasynkronisering.|
| [Tvinga redundans tillåt dataförlust](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Utlöser redundans från den aktuella primära servern till den sekundära servern utan att synkronisera data. Den här åtgärden kan leda till dataförlust. |
| [Hämta redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Hämtar en redundansgrupps konfiguration. |
| [Lista redundansgrupper efter server](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Visar redundansgrupperna på en server. |
| [Gruppen Uppdatera redundans](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Uppdaterar konfigurationen för en redundansgrupp. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Hantera redundansgrupper med hanterade instanser

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Skapar eller uppdaterar konfigurationen av en redundansgrupp |
| [Ta bort redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Tar bort en redundansgrupp från instansen |
| [Redundans (planerad)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Utlöser redundans från den aktuella primära instansen till den här instansen med fullständig datasynkronisering. |
| [Tvinga redundans tillåt dataförlust](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Utlöser redundans från den aktuella primära instansen till den sekundära instansen utan att synkronisera data. Den här åtgärden kan leda till dataförlust. |
| [Hämta redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | hämtar en redundansgrupps konfiguration. |
| [Lista redundansgrupper - Lista efter plats](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Visar en lista över redundansgrupper på en plats. |

## <a name="next-steps"></a>Nästa steg

- Detaljerade självstudier finns i
    - [Lägga till en enskild databas i en redundansgrupp](sql-database-single-database-failover-group-tutorial.md)
    - [Lägga till en elastisk pool till en redundansgrupp](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Lägga till en hanterad instans i en redundansgrupp](sql-database-managed-instance-failover-group-tutorial.md)
- Exempel på skript finns i:
  - [Använd PowerShell för att konfigurera aktiv geo-replikering för en enkel databas i Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Använda PowerShell för att konfigurera aktiv geo-replikering för en pooldatabas i Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Använda PowerShell för att lägga till en en enskild Azure SQL Database-databas i en redundansgrupp](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- En översikt över affärskontinuitet och scenarier finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md)
- Mer information om automatiska säkerhetskopior i Azure SQL Database finns i [automatiska säkerhetskopieringar i SQL Database](sql-database-automated-backups.md).
- Mer information om hur du använder automatiska säkerhetskopior för återställning finns i [Återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
- Mer information om autentiseringskrav för en ny primär server och databas finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).
