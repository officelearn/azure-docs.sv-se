---
title: Azure SQL Database vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor kunder ställa om molndatabaser och Azure SQL Database, Microsofts relationella databashanteringssystem (RDBMS) och databas som en tjänst i molnet.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: 479f7df740e75ae44a5198414036ff0b0c216471
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730655"
---
# <a name="sql-database-faq"></a>Vanliga frågor om SQL Database

## <a name="what-is-the-current-version-of-sql-database"></a>Vad är den aktuella versionen av SQL-databas?
Den aktuella versionen av SQL-databas är V12. Version V11 har tagits bort.

## <a name="what-is-the-sla-for-sql-database"></a>Vad är serviceavtalet för SQL-databas?
Vi garanterar minst 99,99% av tiden, du har anslutning mellan Microsoft Azure SQL-databas och vår Internet-gateway, oavsett din tjänstnivå. 0,01% är reserverad för korrigeringar och uppgraderingar redundans. Mer information finns i [SLA](http://azure.microsoft.com/support/legal/sla/). Information om tillgänglighet arkitekturen i Azure SQL Database finns i [hög tillgänglighet och Azure SQL Database](sql-database-high-availability.md). 

## <a name="can-i-control-when-patching-downtime-occurs"></a>Jag kan styra när uppdatering driftstopp uppstår
Nej. Effekten av korrigeringar är vanligtvis inte noticable om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app.
## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Vad är den nya vCore-baserade inköpsmodellen för Azure SQL Database?

Den nya inköpsmodellen är ett tillägg till den befintliga DTU-baserade modellen. Den vCore-baserade modellen är utformad för att ge kunder flexibilitet, kontroll, transparens och ett enkelt sätt att överföra lokala arbetsbelastningskrav till molnet. Det gör också kunderna möjlighet att skala sina resurser för beräkning och lagring utifrån deras arbetsbelastningsbehov. Enkel databas och elastisk pool alternativ med vCore-modellen är också berättigade till upp till 30 procent med den [Azure Hybrid-förmånen för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Se [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) för mer information. 

## <a name="what-is-a-vcore"></a>Vad är en virtuell kärna (vCore)? 
En virtuell kärna representerar en logisk CPU med möjligheten att välja mellan av maskinvara. Gen 4 logiska CPU baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer och Gen 5 logiska processorer är baserade på Intel E5-2673 v4-processorn (Broadwell) 2.3 GHz-processorer.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Är det obligatoriskt att flytta till den vCore-baserade modellen?
Nej, introduktionen av den vCore-baserade modellen för elastisk Pool och distributionsalternativ för enkel databas återspeglar vår satsning på valfrihet och flexibilitet. Kunder kan fortsätta att använda den DTU-baserade modellen behöver de inte göra något med det här meddelandet och deras upplevelse och fakturan förblir oförändrad. 

I många fall utnyttja program enkelheten med ett förkonfigurerat paket av resurser. Därför kan fortsätter vi att erbjuda och stöd för dessa DTU-baserade alternativ till våra kunder. Om du använder dem och den uppfyller dina affärskrav, bör du fortsätta att göra detta.

Både den DTU- och den vCore-baserade modellen kommer att finnas kvar fortsättningsvis. Startar vi den vCore-baserade modellen som svar på Återkopplingen för mer transparens gällande databasresurser och möjlighet att skala sina resurser för beräkning och lagring separat. Den vCore-baserade modellen medger också ytterligare besparingar för kunder med aktiv Software Assurance tack vare Azure Hybrid-förmånen för SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>Hur ska jag välja mellan vs DTU-baserade inköpschef modellen den vCore-baserade inköpsmodellen? 
Dataöverföringsenheten (DTU, Database Transaction Unit) baseras på ett blandat mått av CPU, minne, läsningar och skrivningar. DTU-baserade storlekar motsvarar förkonfigurerade paket av resurser för att driva olika nivåer av programprestanda. Kunder som inte vill bekymra dig om de underliggande resurserna och föredrar enkelheten med ett förkonfigurerat paket samtidigt och betala en fast mängd varje månad kanske den DTU-baserade modellen passar deras behov. För kunder som behöver mer information om de underliggande resurserna eller behöver skala dem oberoende av varandra för att uppnå optimala prestanda, kommer dock den vCore-baserade modellen vara det bästa valet.  Om en kund har en aktiv Software Assurance (SA) för SQL Server kan de dessutom utnyttja sina befintliga investering och spara upp till 30% med [Azure Hybrid-förmånen för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Alternativen i var och en inköpsmodellerna ger dig fördelarna med en fullständigt hanterad tjänst som automatiska säkerhetskopieringar, uppdateringar och korrigeringar. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Vad är Azure Hybrid-förmånen för SQL Server? 
Den [Azure Hybrid-förmånen för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) hjälper dig att maximera värdet från dina nuvarande licensinvesteringar och snabba migreringen till molnet. Azure Hybrid-förmånen för SQL Server är en Azure-baserad förmån som gör att du kan använda SQL Server-licenser med Software Assurance för att betala en reducerad avgift (”grundpris”) i SQL-databas. Azure Hybrid-förmånen för SQL Server är tillgänglig i den offentliga förhandsversionen av den vCore-baserade inköpsmodellen för SQL Database enskilda databaser och elastiska pooler. Du kan använda den här förmånen även om SKU är aktiv, men Observera baspriset tillämpas från den tidpunkt som du väljer i Azure-portalen. Ingen kredit utfärdas retroaktivt.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användningsrättigheter med Azure Hybrid-förmånen för SQL Server?
Du har dubbel användningsrättigheter för att säkerställa migreringar kör smidigt 180 dagar. Efter den 180 dagar, SQL Server-licens kan bara användas i molnet i SQL-databas och inte har dubbel användning rights lokalt och i molnet.

## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmånen för SQL Server från licensmobilitet?
Idag erbjuder vi license mobility-förmånerna för SQL Server-kunder med Software Assurance som tillåter ny tilldelning av sina licenser för delade-servrar från tredje part. Den här förmånen kan användas på Azure IaaS och AWS EC2.
Azure Hybrid-förmånen för SQL Server skiljer sig från licensmobilitet inom två viktiga områden:
- Det ger ekonomiska fördelar för att flytta mycket virtualiserade arbetsbelastningar till Azure. SQL EE kunder får 4 kärnor i Azure i SKU för generell användning för varje kärna de äger lokalt för mycket virtualiserade program. Licensmobilitet tillåter inte någon särskild kostnadsfördelar för att flytta virtualiserade arbetsbelastningar till molnet.
- Det ger en PaaS-mål på Azure (SQL Database Managed Instance) som är mycket kompatibelt med SQL Server on-premises

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de specifika rättigheterna för Azure Hybrid-förmånen för SQL Server?
SQL-databaskunder har följande rättigheter som är associerade med Azure Hybrid-förmånen för SQL Server:

|Licens fotavtryck|Vad kostar Azure Hybrid-förmånen för SQL Server får du?|
|---|---|
|SQL Server Enterprise Edition core kunder med SA|<li>Kan betala grundpris på generell användning eller SKU affärskritisk</li><br><li>1 kärna på lokala = 4 kärnor i SKU generell användning</li><br><li>1 kärna på lokala = 1 kärna i SKU affärskritisk</li>|
|SQL Server Standard Edition core kunder med SA|<li>Endast kan grundpris betalar SKU generell användning</li><br><li>1 kärna på lokala = 1 kärna i SKU generell användning</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Finns den vCore-baserade modellen till SQL Database Managed Instance?
[Hanterad instans](sql-database-managed-instance.md) är endast tillgängligt med den vCore-baserade modellen. Mer information finns också i den [SQL Database-sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Kostnaden för beräkning och lagring beror på tjänstenivå som jag välja?
Beräkningskostnaden Visar total beräknings-kapacitet som tillhandahålls för programmet. På tjänstnivån affärskritisk allokera vi automatiskt minst 3 Alwayson-repliker. För att återspegla den här ytterligare tilldelning av beräkningsresurser är vCore priset ungefär 2.7 x högre upp i affärskritisk. Av samma anledning visar högre lagringspris per GB i nivån affärskritisk hög i/o och låg fördröjning av SSD-lagring. På samma gång skiljer kostnaden för lagring av säkerhetskopior sig inte eftersom i båda fallen använder vi en klass standardlagring.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Hur betalar jag för lagring – baserat på vad jag konfigurera förskott eller om databasen använder?
Olika typer av lagring faktureras på olika sätt. För lagring av data debiteras du för den etablerade lagring baserat på den maximala storleken för databas eller pool du väljer. Kostnaden ändras inte om inte du minska eller öka den högsta. Lagringsenhet för säkerhetskopior är associerad till automatiska säkerhetskopior av din instans och allokeras dynamiskt. När du ökar din kvarhållningsperiod för lagringsenhet för säkerhetskopiering ökar lagringsenheten för säkerhetskopiering som förbrukas av instansen. Det tillkommer ingen ytterligare avgift för lagringsenhet för säkerhetskopior för upp till 100 procent av ditt totala etablerade serverutrymme. Ytterligare förbrukning av lagringsenhet för säkerhetskopior debiteras i GB per månad. Om du till exempel har en databaslagringsstorlek på 100 GB får du 100 GB säkerhetskopiering utan extra kostnad. Men om säkerhetskopieringen är på 110 GB får du betala för de extra 10 GB. 

Du debiteras för lagring av säkerhetskopior för en enkel databas, på en proportionell beräkning för den lagring som har tilldelats till säkerhetskopior av databasen minus på databasens storlek. Du debiteras för lagring av säkerhetskopior för en elastisk pool, på en proportionell beräkning för den lagring som har tilldelats till säkerhetskopior av databasen på alla databaser i poolen minus den maximala datastorleken för den elastiska poolen. Varje ökning av databasens storlek eller elastisk pool eller ökning av transaktioner, kräver mer lagringsutrymme och därför ökar din faktura för lagring av säkerhetskopior.  Om du ökar den maximala datastorleken kan den här nya är dras av från Faktureras säkerhetskopieringslagring storlek.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Hur väljer jag rätt SKU vid konvertering av en befintlig databas till de nya tjänstenivåerna? 
För befintliga SQL Database-program som använder den DTU-baserade modellen, är service-nivån generell användning jämförbar med Standard-nivån. Affärskritisk tjänstnivån är jämförbar med Premium-nivån. I båda fallen bör du allokera minst 1 virtuell kärna för varje 100 DTU som programmet använder i den DTU-baserade modellen.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-compute-sizes-compatible-with-all-existing-compute-sizes"></a>Erbjuder de nya vCore-baserade tjänstnivåerna storlekar som är kompatibel med alla befintliga compute storlekar?
De nya vCore-baserade tjänstenivåerna erbjuder jämförbara prestandaval för alla elastiska pooler och databaser med 100 dtu: er eller mer.  Vi kommer att fortsätta att lägga till mer compute storlekar med tiden för sub 100 DTU-arbetsbelastningar.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Finns det någon databas skillnaderna mellan de befintliga nivåerna DTU-baserade och nya vCore-baserad tjänst? 
De nya tjänstenivåerna stöder en superuppsättning av funktionerna med de aktuella DTU-baserade erbjudandena. De ytterligare funktionerna inkluderar en uppsättning ytterligare dynamiska hanteringsvyer (DMV) och alternativ för ytterligare konfiguration. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Om min databas är CPU-bundna och använder inte mycket lagringsutrymme, kan jag öka beräkningarna utan att betala för extra lagringsutrymme?
Ja, du kan oberoende välja nivå av beräkning ditt program behöver och förhindra att lagringen har inte ändrats. Lagringen kan anges så lågt som 32GB. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Stöder de nya nivåerna för vCore-baserade punkt återställning till tidpunkt (PITR) i 35 dagar som idag? 
Du kan konfigurera den kvarhållning av säkerhetskopior för PITR mellan 7 och 35 dagar. Lagring för säkerhetskopior debiteras separat utifrån den faktiska lagringsanvändningen om det överskrider lagringsutrymmet som är lika med maxstorleken för data. I förhandsversion, är PITR kvarhållningsperioden som standard till 7 dagar. I många fall räcker maxstorleken för data för att lagra säkerhetskopiorna i 7 dagar.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Varför du för att välja den maskinvara generationen för beräkning?
Vårt mål är att aktivera maximal flexibilitet så att du kan välja en prestandakonfiguration som stämmer överens för programmet. Gen4 maskinvara erbjuder betydligt mer minne per vCore. Gen5 maskinvara kan du skala upp beräkningsresurserna som är mycket högre. Mer information finns i [vCore inköpsmodell](sql-database-service-tiers-vcore.md)

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Måste jag ta mitt program offline för att konvertera från en DTU-baserad databas till en vCore-baserad tjänstenivå? 
De nya tjänstenivåerna erbjuder en enkel konverteringsmetod online som liknar den befintliga processen att uppgradera databaser från tjänstenivån Standard till Premium och vice versa. Den här konverteringen kan inledas med Azure portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enkla databaser](sql-database-single-database-scale.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Kan jag byta en databas från en vCore-baserade tjänstnivå till en DTU-baserade? 
Ja, kan du enkelt konvertera din databas till stöds prestanda ändamålet med hjälp av Azure portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enkla databaser](sql-database-single-database-scale.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Kan jag uppgradera eller nedgradera mellan generell användning och affärskritisk-tjänstnivåer? 
Ja, med vissa begränsningar. Ditt mål-SKU måste uppfylla maximala databas eller elastisk pool-storlek som du har konfigurerat för den befintliga distributionen. Om du använder [Azure Hybrid-förmånen för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), SKU affärskritisk är endast tillgänglig för kunder med Enterprise Edition-licenser. Endast de användare som migrerats från en lokal plats till generell användning med Azure Hybrid-förmånen för SQL Server med Enterprise Edition-licenser kan uppgradera till affärskritisk. Mer information finns i [vad är Azure Hybrid Use-förmånen för SQL Server specifika rättigheter](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Den här konverteringen resulterar inte i driftstopp och kan inledas via Azure portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enkla databaser](sql-database-single-database-scale.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Jag använder en Premium RS-databas som inte är allmänt tillgängliga – kan jag uppgradera den till en ny nivå och få en liknande pris/prestanda-förmånen?
Eftersom den vCore-modellen kan oberoende kontroll över mängden etablerade beräkningen och lagringen, kan du mer effektivt hantera resulterande kostnaderna, vilket gör det till ett attraktivt mål för Premium RS-databaser. Dessutom kan den [Azure Hybrid-förmånen för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) ger en betydande rabatt när den vCore-baserade modellen används. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Hur ofta kan jag justera resurserna per pool?
Så ofta du vill. Se [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-compute-size-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Hur lång tid tar det att ändra tjänstnivå eller beräkna storleken på en enskild databas eller flytta en databas och från en elastisk pool?
När du byter tjänstenivå för en databas och flyttas till eller från en pool måste du databasen som ska kopieras på plattformen som en bakgrundsåtgärd. När du byter tjänstenivå kan ta från några minuter till flera timmar beroende på storleken på databaserna. I båda fallen är databaserna online och tillgängliga under flytten. Mer information om hur du ändrar enskilda databaser finns i [ändra tjänstnivå för en databas](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>När ska jag använda en enkel databas jämfört med elastiska databaser?
I allmänhet elastiska pooler är utformade för en typisk [programvara som tjänst (SaaS)-mönster](sql-database-design-patterns-multi-tenancy-saas-applications.md), där det finns en databas per kund eller klient. Att köpa separata databaser och skaffa stora resurser som kan möta de varierande och de högsta behoven för varje databas är sällan en kostnadseffektiv lösning. Du hantera databaspoolens sammanlagda prestanda i poolen med poolerna och databaserna skalas automatiskt upp och ned. Azures intelligent motorn rekommenderar en pool för databaser när en användningsmönstret på grund av den. Mer information finns i [vägledning databaspool](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Hur användningen av SQL-databas med den DTU-baserade inköpsmodellen visas på fakturan?
SQL Database fakturerar baserat på ett förutsägbart timpris baserat på den [köpa modellen](sql-database-service-tiers-dtu.md). Faktisk användning beräknas och debiteras per timme, så din faktura kan innehålla bråkdelar av en timme. Om det finns en databas i 12 timmar under en månad, visar din faktura användning av 0,5 dagars. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Vad händer om en enskild databas är aktiv i mindre än en timme eller använder en högre tjänstnivå i mindre än en timme?
Du debiteras för varje timme som det finns en databas med hjälp av den högsta tjänstenivå + compute storlek som gällde under den timmen, oavsett användning eller om databasen var aktiv under mindre än en timme. Om du skapar en enkel databas och raderar den fem minuter senare visar din faktura en avgift för en databastimme. 

Exempel:

* Om du skapar en Basic-databas och därefter genast uppgraderar den till Standard S1, debiteras du enligt Standard S1-priset för den första timmen.
* Om du uppgraderar en databas från Basic till Premium klockan 10:00. och uppgraderingen är klar klockan 1:35 följande dag debiteras du enligt Premium-priset startar klockan 1:00 
* Om du börjar nedgradera en databas från Premium till Basic kl. 11:00 AM och den är klar klockan 2:15 och databasen som ingår debiteras enligt Premium-priset fram till 15:00. efter det debiteras enligt priserna för Basic.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Hur elastisk pool med hjälp av den DTU-baserade inköpsmodellen visas användning på min faktura?
Elastisk pool debiterar visa upp på fakturan i elastiska dtu: er (edtu: er) eller virtuella kärnor och lagringsutrymme i steg som visas på [sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/). Det är kostnadsfritt per databas för elastiska pooler. Du debiteras för varje timme som en pool finns på den högsta eDTU eller vCores, oavsett användning eller om poolen var aktiv under mindre än en timme. 

DTU-baserade inköpschef modellen exempel:

* Om du skapar en Standard elastisk pool med 200 edtu: er kl. 11:18, debiteras att lägga till fem databaser i poolen, du för 200 edtu: er för hela timme från och med 11: 00 i resten av dagen.
* På dag 2 kl 5:05. databasen 1 börjar förbruka 50 edtu: er och innehåller stadig via dagen. Databaser 2 – 5 variera mellan 0 och 80 edtu: er. Under dagen, lägger du till fem databaser som använder olika edtu: er under hela dagen. Dag 2 är en hel dag debiteras till 200 edtu: er. 
* På dag 3, 5 klockan du lägger till en annan 15-databaser. Databasanvändningen ökar under dagen till den punkt där du vill öka antalet edtu: er för poolen från 200 till 400 vid 20:05 Avgifter på 200 eDTU-nivå gällde fram till 20: 00 och ökar till 400 edtu: er för de återstående fyra timmarna. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Hur införskaffar elastisk pool faktureras för den DTU-baserade modellen?
Elastiska pooler faktureras per följande egenskaper:

* En elastisk pool faktureras när den har skapandet, även om det finns inga databaser i poolen.
* En elastisk pool faktureras per timme. Det här är samma Avläsning av programvara frekvens som storlekar för enskilda databaser.
* Om en elastisk pool utökas sedan faktureras poolen inte enligt den nya mängden resurser tills storleksändring åtgärden har slutförts. Det följer samma mönster som ändrar storlek på beräkning för enskilda databaser.
* Priset för en elastisk pool är baserad på resurserna i poolen. Priset för en elastisk pool är oberoende av antalet och utnyttjar de elastiska databaserna i den.

Mer information finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/), [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md), och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Hur den vCore-baserade visas användningen på min faktura? 
I den vCore-baserade modellen debiteras tjänsten enligt en förutsägbar timavgift baserad på tjänstenivå, etablerad beräkning i vCores, tillhandahållen lagring i GB/månad och förbrukad lagring för säkerhetskopior. Om lagring av säkerhetskopiering överskrider den totala databasstorleken (det vill säga 100% av databasens storlek), finns det ytterligare avgifter. vCore-timmar, konfigurerad databaslagring, förbrukad I/O och lagring av säkerhetskopior är tydligt specificerade på fakturan, vilket gör det enklare för dig att se vilka resurser som du har använt. Lagring för säkerhetskopior upp till 100% av den maximala databasstorleken ingår, utöver som du faktureras i förbrukade GB per månad under en månad.

Exempel:
- Om SQL-databasen finns i 12 timmar under en månad, visar fakturan 12 timmars vCore användning. Om SQL-databasen etablerade ytterligare 100 GB lagringsutrymme, visar fakturan lagringsanvändning i enheter om GB/månad beräknat per timme samt antalet I/o som använts per månad.
- Om SQL-databasen är aktiv i mindre än en timme, debiteras du för varje timme som databasen finns baserat på den högsta tjänstenivå som väljs det allokerade lagringsutrymme och I/O som tillämpades under den timmen, oavsett användning eller om databasen var aktiv under mindre än en timme.
- Om du skapar en hanterad instans och tar bort den efter fem minuter debiteras du för en databastimme.
- Om du skapar en hanterad instans i nivån generell användning med 8 vCores och därefter genast uppgraderar den till 16 vCores debiteras du 16 vCore-kostnaden för den första timmen.

> [!NOTE]
> Under en begränsad period är säkerhetskopiering och I/O kostnadsfria.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Hur fungerar användningen av aktiv geo-replikering i en elastisk pool som visas på fakturan?
Till skillnad från enskilda databaser, med hjälp av [aktiv geo-replikering](sql-database-geo-replication-overview.md) med elastiska databaser inte har en direkt inverkan för fakturering.  Du debiteras bara för de resurser som har etablerats för var och en av pooler (primär och sekundär pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Hur påverkar användningen av granskningsfunktionen fakturan?
Granskning är inbyggd i tjänsten SQL Database utan extra kostnad och finns tillgänglig på alla tjänstnivåer. Men om du vill lagra granskningsloggar utifrån granskning funktionen används ett Azure Storage-konto, och för tabeller och köer i Azure Storage gäller storleken på din granskningslogg.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Hur jag för att återställa lösenordet för serveradministratören?
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-servrar**, Välj servern i listan och klicka sedan på **Återställ lösenord**.

## <a name="how-do-i-manage-databases-and-logins"></a>Hur hanterar jag databaser och inloggningar?
Se [hantera databaser och inloggningar](sql-database-manage-logins.md). 

> [!NOTE]
> Du kan inte ändra namnet på serveradministratörskontot när den har skapats.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Hur kan jag vara säker på att endast auktoriserade IP-adresser får tillgång till en server?
Se [så här: konfigurera brandväggsinställningar på SQL Database](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Vad är en förväntad replikeringsfördröjning vid geo-replikering av en databas mellan två regioner inom samma geografiska område Azure?
Vi stöder för närvarande ett Återställningspunktmål på fem sekunder och replikeringsfördröjning har mindre än att när geo-secondary finns i Azure rekommenderas parad region och på samma tjänstenivå.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Vad är en förväntad replikeringsfördröjning när geo-secondary skapas i samma region som den primära databasen?
Baserat på den empiriska data, finns det inte för mycket skillnaden mellan regioner inom och mellan regioner replikeringsfördröjning när Azure rekommenderas parad region som ska användas. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Om det finns ett nätverksfel mellan två regioner, hur omprövningslogiken fungerar när geo-replikering har ställts in?
Om det finns ett frånkoppling, gör vi var tionde sekund för att återupprätta anslutningarna.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Vad kan jag göra för att garantera att viktiga förändringar i den primära databasen replikeras?
Geo-secondary är en asynkron replikering och vi gör inga försök att förvara den på fullständig synkronisering med primärt. Men vi ger en metod för att tvinga synkroniseringen att se till att replikeringen av viktiga ändringar (till exempel, lösenordsuppdateringar). Framtvingad synkronisering påverkar prestanda eftersom blockeras anropstråden tills alla genomförda transaktioner replikeras. Mer information finns i [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Vilka verktyg är tillgängliga för att övervaka replikeringsfördröjning mellan den primära databasen och geo-secondary?
Vi exponerar i realtid replikeringsfördröjning mellan den primära databasen och geo-secondary via en DMV. Mer information finns i [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Du flyttar en databas till en annan server i samma prenumeration
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, Välj en databas i listan och klicka sedan på **kopiera**. Se [kopiera en Azure SQL database](sql-database-copy.md) för mer information.

## <a name="to-move-a-database-between-subscriptions"></a>Du flyttar en databas mellan prenumerationer
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-servrar** och välj sedan den server som är värd för databasen i listan. Klicka på **flytta**, och sedan välja resurser att flytta och prenumeration för att flytta till.

