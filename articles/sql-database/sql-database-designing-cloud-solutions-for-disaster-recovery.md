---
title: Utforma tjänst med hög tillgänglighet med Azure SQL Database | Microsoft Docs
description: Läs mer om programmets design för hög tillgänglighet med Azure SQL Database-tjänster.
keywords: molnbaserad haveriberedskap, lösningar för haveriberedskap, säkerhetskopiering av data för appen, geo-replikering, business continuity planering
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f1c228802bd0a2e65321a3abe47b87845f5f86a0
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092621"
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Utforma tjänster med hög tillgänglighet med Azure SQL Database

När du skapar och distribuerar tjänster med hög tillgänglighet på Azure SQL Database, som du använder [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md) att tillhandahålla återhämtning till regionalt avbrott och kritiska fel. Dessutom kan snabb återställning till den sekundära databasen. Den här artikeln fokuserar på vanliga mönster och beskriver fördelarna och nackdelarna med varje alternativ. Information om aktiv geo-replikering med elastiska pooler finns i [elastisk Pool strategier för haveriberedskap](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

> [!NOTE]
> Om du använder Premium- eller affärskritiska databaser och elastiska pooler kan du dem elastiska regionala avbrott genom att konvertera dem till zonen redundant distributionskonfiguration. Se [redundantzonen databaser](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: Använda två Azure-regioner för affärskontinuitet med minimal avbrottstid
I det här scenariot har program som följande egenskaper: 
*   Programmet är aktiv i en Azure-region
*   Alla Databassessioner kräver Läs- och skrivbehörighet (RW) till data
*   Webbnivå och datanivå måste vara samordnad för att minska kostnaden för svarstid och trafik 
*   Grunden, avbrottstid är en högre affärsrisk för dessa program än dataförlust

I det här fallet är topologi för distribution av program optimerad för hantering av regionala katastrofer när alla programkomponenter behöver växling vid fel tillsammans. Diagrammet nedan visar den här topologin. För geografisk redundans distribueras programmets resurser till Region A och B. Resurserna i regionen B används dock inte förrän Region A inte. En redundansgrupp konfigureras mellan två regioner för att hantera databasanslutning, replikering och redundans. Webbtjänsten i båda regionerna är konfigurerad för att få åtkomst till databasen via skrivskyddad lyssnaren  **&lt;redundansgruppsnamnet-&gt;. database.windows.net** (1). Traffic manager har konfigurerats att använda [prioriterad routningsmetod](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure traffic manager](../traffic-manager/traffic-manager-overview.md) används i den här artikeln för tydlighetens skull endast. Du kan använda valfri belastningsutjämningslösning som har stöd för prioriterad routningsmetod.    
>

Följande diagram visar denna konfiguration innan ett avbrott:

![Scenario 1. Konfiguration innan avbrottet.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

SQL Database-tjänsten upptäcker att den primära databasen inte är tillgänglig och utlöser redundans till den sekundära regionen baserat på parametrarna för automatisk redundans principen (1) efter ett avbrott i den primära regionen. Du kan konfigurera en Respitperiod som styr tiden mellan identifiering av driftstörningarna och växling vid fel själva beroende på serviceavtalet för programmet. Det är möjligt att traffic manager startar slutpunkten för växling vid fel innan redundansgruppen utlöser redundans för databasen. I så fall ansluta inte webbprogrammet direkt till databasen. Men återanslutningar lyckas automatiskt så fort databasen redundansväxlingen är klar. När misslyckade regionen har återställts och är online igen, återansluter den gamla primärt automatiskt som en ny sekundär. Diagrammet nedan illustrerar konfigurationen efter en redundansväxling.
 
> [!NOTE]
> Alla transaktioner som utförs efter redundansväxlingen går förlorade vid återanslutning. När redundansväxlingen är klar kan kan programmet i regionen B återansluta och starta om bearbetning av begäranden för användare. Både det webbaserade programmet och den primära databasen är nu i regionen B och samordnas. n>

![Scenario 1. Konfigurationen efter redundans](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Om ett avbrott inträffar i regionen B kan replikeringen mellan primärt och sekundära databasen hämtar pausats men länken mellan två förblir intakta (1). Trafiken hanteras upptäcker att anslutningen till Region B är bruten och markerar slutpunkten webbappen 2 som degraderad (2). Programmets prestanda påverkas inte i det här fallet, men databasen blir exponeras och därför högre risk för dataförlust om region A misslyckas i följd.

> [!NOTE]
> För haveriberedskap rekommenderar vi konfigurationen med programdistribution som är begränsad till två områden. Det beror på att de flesta av de geografiska Azure-områden har bara två regioner. Den här konfigurationen skyddar inte ditt program från en samtidig katastrofalt fel på båda regionerna. I ett ytterst osannolikt av ett sådant fel, kan du återställa dina databaser i en tredje region med [geo-återställning åtgärden](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 När avbrottet har mildras, synkroniserar automatiskt den sekundära databasen med primärt. Prestanda för den primära servern kan påverkas under synkroniseringen. Specifika effekten beror på mängden data som den nya primära hämtades sedan redundansväxlingen. Följande diagram illustrerar ett avbrott i den sekundära regionen:

![Scenario 1. Konfiguration efter ett avbrott i den sekundära regionen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Nyckeln **fördelar** av det här designmönstret är:

* Samma webbprogram har distribuerats till båda regionerna utan regionspecifika konfiguration och kräver inte ytterligare logik för att hantera redundans. 
* Programmets prestanda påverkas inte av redundans som webbprogrammet och databasen finns alltid på samma plats.

Huvudsakliga **kompromiss** är att programresurser i regionen B är underutnyttjade i de flesta fall.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Azure-regioner för affärskontinuitet med maximal konservering
Det här alternativet är bäst lämpade för program med följande egenskaper:

* Dataförlust är viktiga för verksamheten risk. Databasredundans kan bara användas som en sista utväg om avbrottet orsakas av ett oåterkalleligt fel.
* Programmet har stöd för skrivskyddade och läs-och lägen för åtgärder och kan fungera i ”skrivskyddad” för en viss tidsperiod.

I det här mönstret växlar programmet till skrivskyddat läge när skrivskyddade anslutningar startar timeout-fel uppstår. Webbprogrammet har distribuerats till båda regionerna och innehåller en anslutning till skrivskyddad lyssnarslutpunkt och annan anslutning till skrivskyddad lyssnarslutpunkt (1). Traffic manager-profilen ska använda [prioritet routning](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Övervakning av slutpunkt](../traffic-manager/traffic-manager-monitoring.md) ska aktiveras för programslutpunkt i varje region (2).

Följande diagram illustrerar den här konfigurationen innan ett avbrott:

![Scenario 2. Konfiguration innan avbrottet.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

När traffic manager upptäcker ett anslutningsfel till region A, växlar det automatiskt användartrafik till programinstans i regionen B. Med det här mönstret är det viktigt att du ställer in respittiden med förlust av data till ett tillräckligt högt värde, till exempel 24 timmar. Det innebär att data går förlorade förhindras om avbrottet har minimerats inom den tidsperioden. När webbprogrammet i regionen B aktiveras börjar Läs-och skrivåtgärder misslyckas. I det här läget bör det växla till skrivskyddat läge (1). I det här läget dirigeras automatiskt begäranden till den sekundära databasen. Om avbrottet orsakas av ett oåterkalleligt fel, kan troligen den inte hanteras i tid. När den upphör att gälla redundans grupp utlösare växling vid fel. Efter som Läs-och lyssnaren blir tillgänglig och anslutningar till den stoppar misslyckas (2). Följande diagram illustrerar de två olika återställningsprocessen.

> [!NOTE]
> Om du minimera avbrott i den primära regionen inom respitperioden, traffic manager identifierar att återställa anslutningen i den primära regionen och växlar trafiken tillbaka till programmet-instans i regionen A. Den programinstansen återupptar och körs i skrivskyddad läge med hjälp av den primära databasen i regionen A som du ser i diagrammet ovan.
>

![Scenario 2. Disaster recovery faser.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Om det sker ett avbrott i regionen B, identifierar traffic manager fel på slutpunkten web-app-2 i regionen B och markerar den försämrad (1). Under tiden kan växlar redundansgruppen skrivskyddad lyssnaren till region A (2). Avbrottet påverkar inte slutanvändarens upplevelse, men den primära databasen exponeras under avbrottet. Följande diagram illustrerar ett fel i den sekundära regionen:

![Scenario 2. Avbrott i den sekundära regionen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

När avbrottet har mildras, den sekundära databasen omedelbart är synkroniserad med primärt och den skrivskyddade lyssnaren växlas tillbaka till den sekundära databasen i regionen B. Under synkroniseringen gick prestanda för primärt något påverkas beroende på mängden data som ska synkroniseras.

Det här designmönstret har flera **fördelar**:

* Det förhindrar dataförlust vid tillfälliga avbrott.
* Nedtid beror bara på hur snabbt traffic manager identifierar anslutningsfel, men kan ändras.

Den **kompromiss** är att programmet måste kunna köras i skrivskyddat läge.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: Programmet flytt till ett annat geografiskt område utan dataförlust och nästan utan stilleståndstid 
I det här scenariot har programmet följande egenskaper: 
* Slutanvändarna åtkomst till programmet från olika geografiska områden
* Programmet innehåller skrivskyddade arbetsbelastningar som inte beror på fullständig synkronisering med de senaste uppdateringarna
* Skrivåtkomst till data bör stödas i samma geografiska område för majoriteten av användarna 
* Läs fördröjning är avgörande för slutanvändarens upplevelse 


För att uppfylla dessa krav som du behöver att garantera att användarenheten **alltid** ansluter till programmet distribueras i samma geografiska område för skrivskyddade åtgärder, till exempel webbdata, analytics osv. Medan OLTP-åtgärder bearbetas i samma geografiska område **oftast**. Till exempel under tid på dagen OLTP åtgärder bearbetas i samma geografiska område, men under arbetstid som av de kunde bearbetas i ett annat geografiskt område. Om aktiviteten slutanvändaren sker huvudsakligen under arbetstid, kan du garantera optimala prestanda för de flesta användarna de flesta av tiden. Följande diagram visar den här topologin. 
 
Programmets resurser ska distribueras i varje geografisk plats där du har betydande användning begäran. Till exempel om ditt program används aktivt i USA, ska EU och Sydostasien programmet distribueras till alla dessa områden. Den primära databasen bör dynamiskt stängas från en geografisk plats till nästa i slutet av arbetstiden. Den här metoden anropas ”följa solen”. Arbetsbelastningen för OLTP som ansluter alltid till databasen via skrivskyddad lyssnaren  **&lt;redundansgruppsnamnet-&gt;. database.windows.net** (1). Skrivskyddad arbetsbelastning som ansluter till den lokala databasen direkt med hjälp av Serverslutpunkten databaser  **&lt;servernamn&gt;. database.windows.net** (2). Traffic manager konfigureras med den [routningsmetod för prestanda](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Det säkerställer att slutanvändarens enhet är ansluten till webbtjänsten i den närmaste regionen. Traffic manager ska ställas in med slutpunkt övervakning har aktiverats för varje web service-slutpunkt (3).

> [!NOTE]
> Redundanskonfiguration för grupp definierar vilken region som ska användas för redundans. Eftersom den nya primärt är i ett annat geografiskt område redundansen resulterar i längre svarstid för både OLTP och skrivskyddade arbetsbelastningar fram till är den berörda regionen online igen.
>

![Scenario 3. Konfiguration med primärt i USA, östra.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Aktiva databaser bör växlas till nästa region (Europa, norra) i slutet av dagen (till exempel på 23: 00 lokal tid). Den här uppgiften kan automatiseras helt med hjälp av [Azure schemaläggning service](../scheduler/scheduler-intro.md).  Uppgiften omfattar följande steg:
* Växla primära servern i redundansgruppen till Norra Europa med egna redundans (1)
* Ta bort redundansgrupp mellan USA, östra och Europa, Norra
* Skapa en ny redundansgrupp med samma namn men mellan Nordeuropa och Östasien (2). 
* Lägga till primärt i Norra Europa och sekundära i Östasien i den här redundansgruppen (3).


Följande diagram illustrerar den nya konfigurationen efter den planerade redundansväxlingen:

![Scenario 3. Överför primärt till Norra Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Om ett avbrott inträffar till exempel i Norra Europa, skickar den automatiska databasredundans i redundansgruppen, vilket effektivt resulterar i att flytta program till nästa region efter schemat (1).  I så fall är den enda återstående sekundära regionen i USA, Öst tills Norra Europa är online igen. De återstående två regionerna fungera kunder i alla tre områden genom att växla mellan roller. Azure scheduler måste justeras på motsvarande sätt. Eftersom de återstående regionerna får ytterligare användartrafik från Europa, påverkas programmets prestanda inte bara av ytterligare fördröjning utan även av ett ökat antal anslutningar för slutanvändaren. När avbrottet har mildras i Norra Europa, synkroniseras den sekundära databasen direkt med den aktuella primärt. Följande diagram illustrerar ett avbrott i Norra Europa:

![Scenario 3. Avbrott i Norra Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Du kan minska den tid när slutanvändarens upplevelse i Europa försämras lång svarstid. Om du vill göra som du bör proaktivt distribuera en kopia av programmet och skapa de sekundära databaserna i en annan lokala region (Europa, västra) som ersättning för programmet för offline-instans i Norra Europa. När denna är online igen kan du bestämma om du vill fortsätta att använda Västeuropa eller att ta bort kopian av program där och växla tillbaka till Norra Europa,
>

Nyckeln **fördelar** av den här designen är:
* Skrivskyddad programbelastningen har åtkomst till data i regionen garderober hela tiden. 
* Läs-och programbelastningen har åtkomst till data i den närmaste regionen under perioden för den högsta aktiviteten i varje geografisk plats
* Eftersom programmet distribueras till flera regioner, kan den överleva en förlust av en av regionerna utan betydande driftavbrott. 

Men det finns några **kompromisser**:
* Ett regionalt strömavbrott resulterar i område för att påverkas av längre svarstider. Både läs-och skrivbara och skrivskyddade arbetsbelastningar hanteras av programmet i ett annat geografiskt område. 
* Skrivskyddade arbetsbelastningar måste ansluta till en annan slutpunkt i varje region. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Affärskontinuitet planering: Välj en programutformning för katastrofåterställning i molnet
Din strategi för katastrofåterställning specifika molnet kan kombinera eller utöka dessa designmönster för att de passar bäst för ditt program.  Såsom nämnts tidigare baseras vilken strategi du väljer på serviceavtalet som du vill att erbjuda dina kunder och topologi för distribution av programmet. I följande tabell jämförs alternativen baserat på mål för återställningspunkt (RPO) och uppskattad återställningstid (ERT) för att hjälpa ditt beslut.

| Mönster | RPO-MÅL | ERT |
|:--- |:--- |:--- |
| Aktivt-passivt distribution för haveriberedskap med samordnade databasåtkomst |Läs-/ skrivåtkomst < 5 SEK |Fel vid tidpunkten för identifieringen + TTL för DNS |
| Aktiv-aktiv distribution för belastningsutjämning för programmet |Läs-/ skrivåtkomst < 5 SEK |Fel vid tidpunkten för identifieringen + TTL för DNS |
| Aktivt-passivt distribution för förvaring av data |Skrivskyddad åtkomst < 5 SEK | Skrivskyddad åtkomst = 0 |
||Läs-/ skrivåtkomst = noll | Läs-/ skrivåtkomst = fel vid tidpunkten för identifieringen + respitperiod med dataförlust |
|||

## <a name="next-steps"></a>Nästa steg
* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md)
* Läs om geo-replikering och redundans-grupper i [aktiv geo-replikering](sql-database-geo-replication-overview.md)  
* Information om aktiv geo-replikering med elastiska pooler finns i [elastisk Pool strategier för haveriberedskap](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
