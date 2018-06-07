---
title: Utforma tjänst med hög tillgänglighet med hjälp av Azure SQL Database | Microsoft Docs
description: Läs mer om programmet design för hög tillgänglighet med hjälp av Azure SQL Database-tjänster.
keywords: molnet katastrofåterställning, katastrofåterställning, app-säkerhetskopiering, geo-replikering, kontinuerlig planering
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ebe6d2b4d3210ad7c02ec2d26a311645660aeab8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647076"
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Designa tjänster med hög tillgänglighet med hjälp av Azure SQL Database

När du skapar och distribuerar tjänster med hög tillgänglighet på Azure SQL Database, använder du [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md) att tillhandahålla återhämtning i regionala avbrott och oåterkalleligt fel. Snabb återställning till de sekundära databaserna kan också. Den här artikeln fokuserar på vanliga program mönster och beskriver fördelar och avvägningarna med varje alternativ. Information om aktiv geo-replikering med elastiska pooler finns [elastisk Pool strategi för katastrofåterställning](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

> [!NOTE]
> Om du använder Premium eller Business (förhandsgranskning) databaser och elastiska pooler, kan du göra dem motståndskraftiga mot regionala avbrott genom att konvertera dem till zonen redundant distributionskonfiguration (för närvarande under förhandsgranskning). Se [redundantzonen databaser](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: Använda två Azure-regioner för företagskontinuitet med minimal avbrottstid
I det här scenariot har programmen följande egenskaper: 
*   Programmet är aktiv i en Azure-region
*   Alla Databassessioner med kräver Läs- och skrivbehörighet (RW) till data
*   Webbnivå och datanivå måste vara samordnad för att minska kostnaden för fördröjning och trafik 
*   Grunden, avbrottstid är en större företag risk för dessa program än dataförlust

I det här fallet är topologi för distribution av program optimerad för hantering av regionala katastrofer när alla programkomponenter behöver växling vid fel tillsammans. Diagrammet nedan visar den här topologin. För geografisk redundans har programmets resurser distribuerats till Region A och b Resurserna i Region B används dock inte förrän Region A inte. En redundansväxlingsgrupp har konfigurerats mellan de två regionerna att hantera databasanslutning, replikering och redundans. Webbtjänsten i båda regioner är konfigurerad för att komma åt databasen via skrivskyddad lyssnaren  **&lt;redundans gruppnamn&gt;. database.windows.net** (1). Trafikhanterarprofilen har konfigurerats att använda [prioritet routningsmetoden](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure traffic manager](../traffic-manager/traffic-manager-overview.md) används endast för illustration i den här artikeln. Du kan använda belastningsutjämning lösning som har stöd för routningsmetoden för prioritet.    
>

Följande diagram visar den här konfigurationen innan ett avbrott:

![Scenario 1. Konfiguration innan avbrottet.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Efter ett avbrott i den primära regionen identifierar SQL Database-tjänsten att den primära databasen inte är tillgänglig och utlöser redundans till den sekundära regionen baserat på parametrarna för automatisk redundans principen (1). Du kan konfigurera Respitperiod som styr tiden mellan identifiering av avbrottet och växling vid fel sig beroende på SERVICENIVÅAVTAL för programmet. Det är möjligt att traffic manager initierar slutpunkten för växling vid fel innan gruppen redundans utlöser redundans för databasen. I så fall att webbprogrammet omedelbart inte ansluta till databasen. Men återanslutningar slutförs automatiskt så snart databasen redundansväxlingen är klar. När den misslyckade regionen är återställda och online igen, återansluter den gamla primärt automatiskt som en ny sekundär. Diagrammet nedan visar konfigurationen efter växling vid fel.
 
> [!NOTE]
> Alla transaktioner efter redundansväxlingen går förlorade vid återanslutning. När redundansväxlingen är klar kan programmet i region B återansluta och starta om bearbetningen av användarbegäranden. Både webbprogrammet och den primära databasen är nu i region B och samordnas. n>

![Scenario 1. Konfigurationen efter växling vid fel](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Om ett avbrott inträffar i region B replikeringen mellan primärt och sekundära databasen hämtar pausats men länken mellan två förblir intakta (1). Trafiken hanteras upptäcker att anslutningen till Region B har brutits och markerar webbprogrammet slutpunkt 2 som degraderad (2). Programmets prestanda påverkas inte i det här fallet men databasen blir exponeras och därför högre risk för dataförlust om region A misslyckas i följd.

> [!NOTE]
> För katastrofåterställning rekommenderar vi konfigurationen med programdistributionen som är begränsad till två regioner. Det beror på att de flesta av Azure geografiska områden har bara två regioner. Den här konfigurationen skyddar inte programmet från ett samtidiga oåterkalleligt fel på båda regioner. En osannolika av ett sådant fel kan du återställa databaserna i en tredje region med [geo-återställning åtgärden](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 När avbrottet minskas synkroniserar den sekundära databasen automatiskt med den primära servern. Prestanda för den primära servern kan påverkas under synkroniseringen. Specifika konsekvenserna beror på mängden data som den nya primära som hämtades sedan växling vid fel. Följande diagram illustrerar ett avbrott i den sekundära regionen:

![Scenario 1. Konfiguration efter ett avbrott i den sekundära regionen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Nyckeln **fördelar** av det här designmönstret är:

* På samma webbprogram har distribuerats till båda regioner utan någon regionspecifika konfiguration och kräver inte ytterligare logik för att hantera redundans. 
* Programmets prestanda påverkas inte av redundans som webbprogrammet och databasen finns alltid på samma plats.

Huvudsakliga **förhållandet** är att programresurser i Region B är underutnyttjade i de flesta fall.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Azure-regioner för företagskontinuitet med maximalt mycket data bevaras
Det här alternativet fungerar bäst för program med följande egenskaper:

* Dataförlust är viktiga för verksamheten risk. För växling vid databasfel kan endast användas som en sista utväg om avbrottet orsakas av ett oåterkalleligt fel.
* Programmet har stöd för skrivskyddade och läsa / skriva-lägen för åtgärder och kan köras ”skrivskyddad” för en viss tidsperiod.

I det här mönstret växlar programmet till skrivskyddat läge när skrivskyddad anslutningar börja få timeout-fel. Webbprogrammet har distribuerats till båda regioner och innehåller en anslutning till slutpunkten läsa / skriva-lyssnare och annan anslutning till skrivskyddad lyssnare slutpunkten (1). Trafikhanterarprofilen ska använda [prioritet routning](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Övervakning av slutpunkten](../traffic-manager/traffic-manager-monitoring.md) måste vara aktiverat för programmet slutpunkten i varje region (2).

Följande diagram illustrerar den här konfigurationen innan ett avbrott:

![Scenario 2. Konfiguration innan avbrottet.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

När traffic manager upptäcker ett anslutningsfel till region A, växlar den automatiskt användartrafik till programinstansen i region B. Med det här mönstret är det viktigt att du anger respittiden med förlust av data till ett tillräckligt högt värde, till exempel 24 timmar. Det garanterar att data går förlorade förhindras om avbrottet minskas inom den tiden. När webbprogrammet i region B är aktiverad startar Läs-och skrivåtgärder misslyckas. Det bör då växla till skrivskyddat läge (1). I det här läget dirigeras automatiskt begäranden till den sekundära databasen. Om avbrottet orsakas av ett oåterkalleligt fel kan troligen den undvikas i tid. När den upphör att gälla redundans grupp utlösare för växling vid fel. Efter som skrivskyddad lyssnaren blir tillgänglig och anslutningar till den stoppa misslyckas (2). Följande diagram illustrerar två steg av återställningsprocessen.

> [!NOTE]
> Om avbrott i den primära regionen minskas inom respitperioden traffic manager upptäcker att återställa anslutningen i den primära regionen och användaraktiviteten växlar tillbaka till programinstansen i region A. Den programinstansen återupptar och körs i läsa / skriva-läge med hjälp av den primära databasen i region A som visas i föregående diagram.
>

![Scenario 2. Disaster recovery steg.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Om ett avbrott inträffar i region B identifierar traffic manager fel på slutpunkten webb-app-2 i region B och markerar den försämrad (1). Under tiden växlar gruppen redundans skrivskyddad lyssnaren region A (2). Den här avbrott påverkar inte användarupplevelsen, men den primära databasen utsätts under avbrottet. Följande diagram illustrerar ett fel i den sekundära regionen:

![Scenario 2. Avbrott i den sekundära regionen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

När avbrottet minskas den sekundära databasen omedelbart är synkroniserad med den primära servern och skrivskyddad lyssnaren växlas tillbaka till den sekundära databasen i region B. Under synkroniseringen gick av primära något påverkas databasprestanda beroende på mängden data som ska synkroniseras.

Det här designmönstret har flera **fördelar**:

* Den förhindrar förlust av data under de tillfälliga avbrott.
* Driftstopp beror på hur snabbt traffic manager identifierar anslutningsfel som kan konfigureras.

Den **förhållandet** är att programmet måste kunna köras i skrivskyddat läge.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: Programmet flytt till ett annorlunda geografi utan dataförlust och nära driftstopp 
I det här scenariot har programmet följande egenskaper: 
* Slutanvändarna åtkomst till programmet från olika geografiska områden
* Programmet innehåller skrivskyddade arbetsbelastningar som inte är beroende fullständig synkronisering med de senaste uppdateringarna
* Skrivåtkomst till data ska användas i samma geografiska för majoriteten av användarna 
* Läs fördröjning är avgörande för slutanvändaren 


För att uppfylla kraven som du behöver att garantera att användarenheten **alltid** ansluter till programmet distribueras i samma geografiska för skrivskyddade åtgärder, till exempel webbläsarens data analytics osv. Medan OLTP-åtgärder bearbetas i samma geografiska **oftast**. Till exempel under tid på dagen OLTP åtgärder bearbetas i samma geografiska, men av timmar kunde de bearbetas i en annorlunda geografi. Om aktiviteten slutanvändaren inträffar oftast under arbetstid, kan du garantera optimala prestanda för de flesta användarna oftast. Följande diagram visar den här topologin. 
 
Programmets resurser ska distribueras i varje geografisk plats där du har betydande användarkrav. Till exempel om programmet används aktivt i USA, ska EU och Sydostasien programmet distribueras till alla dessa geografiska områden. Den primära databasen ska vara dynamiskt växlade från en geografisk plats till nästa i slutet av arbetstid. Den här metoden kallas ”följa med i sun”. OLTP-arbetsbelastning ansluter alltid till databasen via skrivskyddad lyssnaren  **&lt;redundans gruppnamn&gt;. database.windows.net** (1). Skrivskyddad arbetsbelastning ansluter till den lokala databasen direkt med databaser serverslutpunkt  **&lt;servernamn&gt;. database.windows.net** (2). Trafikhanterarprofilen har konfigurerats med den [routningsmetoden för prestanda](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Det säkerställer att slutanvändarens enhet är ansluten till webbtjänsten i närmaste region. Traffic manager ska ställas in med slutpunkten övervakning aktiverat för varje slutpunkt webbtjänst (3).

> [!NOTE]
> Grupp redundanskonfiguration definierar vilken region som ska användas för redundans. Eftersom den nya primärt annorlunda geografi redundans resultatet i längre svarstid för både OLTP och skrivskyddade arbetsbelastningar tills den berörda regionen är online igen.
>

![Scenario 3. Konfiguration med primära i östra USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Aktiva databaserna bör växlas till nästa region (Norra Europa) i slutet av dagen (till exempel på 23: 00 lokal tid). Den här uppgiften kan automatiseras helt med hjälp av [Azure schemaläggning tjänsten](../scheduler/scheduler-intro.md).  Uppgiften omfattar följande steg:
* Växla primära servern i gruppen redundans till Norra Europa med egna växling vid fel (1)
* Ta bort gruppen redundans mellan östra USA och Norra Europa
* Skapa en ny redundansväxlingsgrupp med samma namn men mellan Norra Europa och Östasien (2). 
* Lägg till primärt i Norra Europa och sekundär i Östasien till den här gruppen för växling vid fel (3).


Följande diagram illustrerar den nya konfigurationen efter den planerade redundansväxlingen:

![Scenario 3. Övergång primär till Norra Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Om ett avbrott inträffar till exempel i Nordeuropa, initieras automatisk databasen redundans av gruppen växling vid fel, vilket ger effektivt flytta programmet till nästa område i schemat (1).  USA, Öst är i så fall den enda kvarvarande sekundära regionen tills Norra Europa är tillbaka online. De återstående två regionerna fungera kunder i alla tre geografiska områden genom att växla roller. Azure Schemaläggaren måste därför anpassas. Eftersom de återstående regionerna får ytterligare användartrafik från Europa påverkas programmets prestanda inte bara av ytterligare svarstid, utan även av ett ökat antal anslutningar för slutanvändaren. När avbrottet minskas i Nordeuropa, synkroniseras den sekundära databasen omedelbart med den aktuella primärt. Följande diagram illustrerar ett avbrott i Norra Europa:

![Scenario 3. Avbrott i Norra Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Du kan minska den tid när slutanvändarens upplevelse i Europa försämras lång svarstid. Gör att du ska proaktivt distribuera en kopia av programmet och skapa de sekundära databaserna i en annan lokal region (västra Europa) som ersättning för offline programinstansen i Norra Europa. När den är online kan bestämma om du vill fortsätta använda västra Europa eller ta bort kopia av det programmet och växla tillbaka till med hjälp av Norra Europa
>

Nyckeln **fördelar** för den här designen är:
* Skrivskyddad programmet arbetsbelastningen kommer åt data i området garderober vid alla tidpunkter. 
* Skrivskyddad programmet arbetsbelastningen kommer åt data i området närmaste under den högsta aktiviteten i varje geografisk plats
* Det kan överleva förlust av någon av regionerna utan driftavbrott betydande eftersom programmet distribueras till flera regioner. 

Men det finns några **kompromisser**:
* Ett regionalt strömavbrott resulterar i geografi att påverkas av längre svarstid. Både läs-och skrivbara och skrivskyddade arbetsbelastningar hanteras av programmet i en annorlunda geografi. 
* Skrivskyddade arbetsbelastningar måste ansluta till en annan slutpunkt i varje region. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Kontinuitet planering: Välj en design för programmet för katastrofåterställning i molnet
Din strategi för katastrofåterställning specifika molnet kan kombinera eller utöka dessa designmönster för att de passar bäst för ditt program.  Som tidigare nämnts baseras strategin som du väljer på serviceavtal som du vill att erbjuda kunderna och topologi för distribution av programmet. För att hjälpa dig jämförs i följande tabell alternativen baserat på återställningspunktmål (RPO) och uppskattade återställningstid (Infoga).

| Mönster | ÅTERSTÄLLNINGSPUNKTMÅL | INFOGA |
|:--- |:--- |:--- |
| Aktivt-passivt distribution för katastrofåterställning med samordnade databasåtkomst |Läs-/ skrivåtkomst < 5 SEK. |Fel identifieringstiden + DNS TTL |
| Aktiv-aktiv distribution för belastningsutjämning för program |Läs-/ skrivåtkomst < 5 SEK. |Fel identifieringstiden + DNS TTL |
| Aktivt-passivt distribution för förvaring av data |Skrivskyddad åtkomst < 5 SEK. | Skrivskyddad åtkomst = 0 |
||Läs-/ skrivåtkomst = noll | Läs-/ skrivåtkomst = fel identifieringstiden + respitperioden med dataförlust |
|||

## <a name="next-steps"></a>Nästa steg
* En översikt över verksamhetskontinuitet och scenarier finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md)
* Läs om geo-replikering och redundans grupper i [aktiv geo-replikering](sql-database-geo-replication-overview.md)  
* Information om aktiv geo-replikering med elastiska pooler finns [elastisk Pool strategi för katastrofåterställning](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
