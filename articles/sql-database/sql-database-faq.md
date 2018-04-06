---
title: Azure SQL Database vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor kunder fråga om molnet databaser och Azure SQL Database, Microsofts relationella databashanteringssystem (RDBMS) och databasen som en tjänst i molnet.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: f98337044bdad788d2a4c9eac0c67a2031810430
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="sql-database-faq"></a>Vanliga frågor om SQL Database

## <a name="what-is-the-current-version-of-sql-database"></a>Vad är den aktuella versionen av SQL-databasen?
Den aktuella versionen av SQL-databas är V12. Version V11 har tagits bort.

## <a name="what-is-the-sla-for-sql-database"></a>Vad är SLA för SQL-databasen?
Vi garanterar minst 99,99% av tiden, du har en anslutning mellan Microsoft Azure SQL Database och våra Internet-gateway, oavsett dina tjänstnivån. Mer information finns i [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="whatis-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Whatis den nya vCore-baserade inköpsmodell för Azure SQL Database?

Nya inköpsmodell är ett tillägg till den befintliga modellen DTU-baserade. VCore-baserade-modellen är utformad att ge kunderna flexibilitet, kontroll, genomskinlighet och ett enkelt sätt att översätta arbetsbelastningskraven för lokalt till molnet. Det gör även kunder att skala bearbetning och lagring utifrån deras behov av arbetsbelastning. Enskilda databaser och elastiska poolen alternativ med hjälp av vCore modellen också är berättigad till upp till 30 procent besparingarna med den [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Se [tjänstnivåer](sql-database-service-tiers.md) information om DTU-baserade inköpsmodell såväl vCore-baserade inköpsmodell.

## <a name="what-is-a-vcore"></a>Vad är en virtuell kärna? 
En virtuell kärna representerar logiska Processorn erbjuds med möjlighet att välja mellan generationer av maskinvara. Gen 4 logiska processorer är baserade på Intel E5-2673 v3 (Haswell) 2,4 GHz processorer och Gen 5 logiska processorer är baserade på Intel E5-2673 v4 (Broadwell) 2.3 GHz-processorer.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Flyttar till vCore-baserade modellen krävs?
Nej, visar införandet av modellen vCore-baserade elastiska poolen och distributionsalternativ för enskild databas vårt arbete för kunden kan välja och flexibilitet. Om kunder vill fortsätta med den DTU-baserade modellen, de behöver inte göra något med det här meddelandet och deras erfarenhet och fakturering förblir oförändrad. 

I många fall utnyttja program enkelhet för ett förkonfigurerade paket av resurser. Därför kan fortsätter vi att erbjuda och stöd för dessa DTU-baserade val för våra kunder. Om du använder dem och att den uppfyller dina affärskrav, ska du fortsätta att göra detta.

DTU och vCore-baserade modeller fortsätter sida vid sida finns. Vi startar vCore-baserade modellen som svar på kundernas önskemål för flera genomskinlighet runt sina databasresurser och möjligheten att skala beräkning och lagring separat. Modellen vCore-baserade kan också ytterligare besparingar för kunder med aktiv Software Assurance via Azure Hybrid-förmån för SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>Hur väljer jag mellan vs DTU-baserade köp modellen vCore-baserade inköpsmodell? 
Database Transaction Unit (DTU) är baserad på ett blandat mått av CPU, minne, läser och skriver. DTU-baserade prestandanivåer motsvarar förkonfigurerade paket med resurser till enheten olika nivåer av programprestanda. Kunder som inte vill bry dig om de underliggande resurserna och föredrar enkelhet för ett förkonfigurerade paket när betalar ett fast belopp varje månad kanske DTU-baserade modell passar för deras behov. För kunder som behöver större insyn i de underliggande resurserna eller behöver för att skala dem separat om du vill uppnå bästa prestanda, kommer vCore-baserade modellen dock bäst.  Dessutom, om en kund har en aktiv Software Assurance (SA) för SQL Server, de kan utnyttja befintliga investeringar och spara upp till 30% med [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Alternativ inom varje köp modeller ger fördelarna med en helt hanterad tjänst som automatisk säkerhetskopiering, uppdateringar och korrigeringsfiler. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Vad är Azure Hybrid-förmån för SQL Server? 
Den [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) hjälper dig att maximera värdet från din aktuella licensiering investeringar och öka sina migrering till molnet. Azure Hybrid-förmån för SQL Server är en fördel med Azure som gör att du kan använda SQL Server-licenser med Software Assurance betala en minskad hastighet (”grundavgift”) på SQL-databas. Azure Hybrid-förmån för SQL Server är tillgänglig på förhandsversion av vCore-baserade inköpsmodell för SQL-databas enskilda databaser och elastiska pooler. Du kan tillämpa detta även om SKU: N är aktiv men Observera att den grundläggande hastigheten kopplas från den tidpunkt som du väljer i Azure-portalen. Ingen kredit ska utfärdas i efterhand.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användarrättigheter med Azure Hybrid förmån för SQL Server?
Du har 180 dagar efter dubbel användning rättigheter för att kontrollera migrering körs sömlöst. Efter den 180-dagarsperioden SQL Server-licens kan endast användas i molnet i SQL-databas och inte har dubbel användning rättigheter lokalt och i molnet.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmån för SQL Server från licensera mobility?
Idag är erbjuder vi SQL Server-kunder med Software Assurance licens mobility fördelar som gör att omtilldelning av sina licenser till servrar från tredje part delas. Detta kan användas på Azure IaaS och AWS EC2.
Azure Hybrid-förmån för SQL Server skiljer sig från licensmobilitet i två huvuddelar:
- Det ger ekonomiska fördelar för att flytta hög virtualiserade arbetsbelastningar till Azure. SQL EE kunder kan få 4 kärnor i Azure i allmänna ändamål SKU: N för varje core de äger lokalt för mycket virtualiserade program. Licensmobilitet tillåter inte någon särskild kostnadsfördelar för att flytta virtualiserade arbetsbelastningar till molnet.
- Det ger för PaaS mål på Azure som är kompatibel med SQL Server-lokala – hanteras SQL-databasinstansen.

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vad är Azure Hybrid-förmån för SQL Server specifika rättigheter?
SQL-databas kunder har följande rättigheter som är associerade med Azure Hybrid-förmån för SQL Server:

|Licens storleken|Vad gör Azure Hybrid-förmån för SQL Server får du?|
|---|---|
|SQL Server Enterprise Edition core kunder med SA|<li>Kan betala Base-hastighet generella eller Business kritiska SKU</li><br><li>1 kärna lokala = 4 kärnor för allmänna ändamål SKU</li><br><li>1 kärna lokala = 1 kärna i företag kritiska SKU</li>|
|SQL Server Standard Edition core kunder med SA|<li>Endast kan Base hastighet betalar SKU för allmänna ändamål</li><br><li>1 kärna lokala = 1 kärna i SKU för allmänna ändamål</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Finns vCore-baserade modellen till hanterade instans av SQL-databasen?
[Hanterade instans](sql-database-managed-instance.md) är endast tillgänglig med vCore-baserade modellen. Mer information finns också i [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Kostnaden för beräkning och lagring beror på tjänstnivå som jag välja?
Beräkna kostnaden visar den totala beräknade kapaciteten som har etablerats för programmet. I företag kritiska tjänstnivån tilldela vi automatiskt minst 3 Alwayson-repliker. För att återspegla den här ytterligare tilldelning av beräkningsresurser är vCore priset ungefär 2.7 x högre upp i företag kritiska. Av samma anledning visar högre lagringspris per GB i företag kritiska nivån hög IO och låg fördröjning av SSD-lagringen. På samma gång skiljer kostnaden för lagring för säkerhetskopiering sig inte eftersom i båda fallen använder vi en klass med standardlagring.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Hur är debiteras för lagring - baserat på vad jag konfigurera förskott eller databasen använder?
Olika typer av lagring debiteras på olika sätt. För lagring av data, att debiteras du för lagring av etablerade baserat maximala databas och poolstorleken som du väljer. Kostnaden ändras inte om du inte minska eller öka det maximalt. Lagringsenhet för säkerhetskopior är associerad till automatiska säkerhetskopior av din instans. När du ökar din kvarhållningsperiod för lagringsenhet för säkerhetskopiering ökar lagringsenheten för säkerhetskopiering som förbrukas av instansen. Det tillkommer ingen ytterligare avgift för lagringsenhet för säkerhetskopior för upp till 100 procent av ditt totala etablerade serverutrymme. Ytterligare förbrukning av säkerhetskopieringslagring debiteras i GB per månad. Om du till exempel har en databaslagringsstorlek på 100 GB får du 100 GB säkerhetskopiering utan extra kostnad. Men om säkerhetskopieringen är 110 GB, du betalar ytterligare 10 GB.

För lagring av säkerhetskopior för en enskild databas debiteras du på proportionellt för lagring som har tilldelats databassäkerhetskopieringar minus storleken på databasen. För lagring av säkerhetskopior för en elastisk pool debiteras du på proportionellt för den lagring som har allokerats till databassäkerhetskopieringar för alla databaser i poolen minus det maximala storleken på den elastiska poolen. Varje ökning av databasens storlek eller elastisk pool eller procentsatsen transaktion kräver mer lagringsutrymme och därmed ökar fakturan lagring för säkerhetskopiering.  Om du ökar den maximala storleken dras nya beloppet från fakturerade säkerhetskopieringslagring storlek.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Hur väljer jag rätt SKU vid konvertering av en befintlig databas till de nya tjänstnivåerna? 
Tjänstnivån generella är jämförbar med standardnivån för befintliga SQL-databas program med hjälp av DTU-baserade-modellen. Företag kritiska tjänstnivån är jämförbar med Premium-nivån. I båda fallen bör du allokera minst 1 vCore för varje 100 DTU som används av ditt program i DTU-baserade modellen.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>Erbjuder de nya vCore-baserade tjänstnivåerna prestandanivåer som är kompatibel med alla befintliga servicenivåmål (slo: erna)?
Nya vCore-baserade tjänstnivåer erbjuder jämförbara prestanda alternativ för alla elastiska pooler och databaser med hjälp av 100 dtu: er eller mer.  Vi kommer att fortsätta att lägga till flera servicenivåmål över tid för att hantera sub 100 DTU arbetsbelastningar.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Finns det någon databas funktionsskillnader mellan befintliga DTU-baserade och nya vCore-baserade tjänstnivåer? 
Nya tjänstnivåer stöd inbegriper funktioner finns i de aktuella DTU erbjudandena. Ytterligare funktioner innehåller en uppsättning ytterligare dynamiska hanteringsvyer (av DMV: er) och ytterligare konfigurationsalternativ. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Om databasen är CPU-bundna och använder inte mycket lagringsutrymme, kan jag öka beräkningen utan att betala för extra lagring?
Ja, du kan oberoende väljer du beräkning ditt program måste och förhindra att lagringen oförändrade. Lagringen kan anges så lågt som 32GB. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Stöder de nya vCore-baserade nivåerna punkt tidpunkt för återställning (PITR) för 35 dagar som idag? 
Du kan konfigurera säkerhetskopiering kvarhållning för PITR mellan 7 och 35 dagar. Säkerhetskopieringar lagringen debiteras separat baserat på den faktiska lagringsförbrukningen om den överskrider lagringsutrymmet som är lika med den maximala storleken. I Förhandsgranska är PITR kvarhållningsperiod som standard till 7 dagar. I många fall är den maximala storleken tillräcklig för att lagra 7 dagar för säkerhetskopior.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Varför du för att välja maskinvara-generering för beräkning?
Vårt mål är att aktivera maximal flexibilitet så att du kan välja en prestanda-konfiguration som stämmer överens med behoven för programmet. Tabellen ovan visar skillnaderna mellan Gen4 och Gen5. I synnerhet erbjuder Gen4 maskinvara betydligt mer minne per vCore. Gen5 maskinvara kan du skala upp beräkning mycket högre. Vi vill göra dessa skillnader transparent så att du kan uppnå optimal pris och prestanda förhållandet för ditt program.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Behöver jag göra min programmet offline för att konvertera från en DTU-baserad databas till en tjänstnivå med vCore? 
Nya tjänstnivåer erbjuder en enkel onlinekonvertering-metod som liknar befintliga processen med att uppgradera databaser från Standard till premiumnivån och vice versa. Den här konverteringen kan initieras med hjälp av portalen, ARM, PowerShell, Azure CLI eller T-SQL. Se [hantera enskilda databaser](sql-database-single-database-resources.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Kan jag konvertera en databas från en tjänstnivå med vCore till en DTU-baserade? 
Ja, kan du enkelt konvertera din databas till stöds prestanda ändamålet med hjälp av portalen eller programmässigt Portal, ARM, PowerShell, Azure CLI eller T-SQL. Se [hantera enskilda databaser](sql-database-single-database-resources.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Kan jag uppgradera eller nedgradera mellan tjänstnivåer generella och kritiska företag? 
Ja, med vissa begränsningar. Ditt mål-SKU måste uppfylla maximala databas eller elastisk poolstorlek som du har konfigurerat för den befintliga distributionen. Om du använder [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Business kritiska SKU är endast tillgängligt för kunder med Enterprise Edition licenser. Kunder som migrerats från lokal till generella använder Azure Hybrid-förmån för SQL Server med Enterprise Edition licenser kan uppgradera till företag kritiska. Mer information finns i [vad är Azure Hybrid Använd förmån för SQL Server specifika rättigheter](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Den här konverteringen leder inte till avbrott och kan initieras med hjälp av portalen, ARM, PowerShell, Azure CLI eller T-SQL. Se [hantera enskilda databaser](sql-database-single-database-resources.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Jag använder en Premium RS-databas som inte är normalt tillgängliga - kan jag uppgradera den till en ny nivå och få en liknande pris och prestanda fördel?
Eftersom vCore modellen tillåter oberoende kontroll över mängden etablerade beräkning och lagring, kan du effektivare hantera resulterande kostnader, vilket gör det en bra mål för RS för Premium-databaser. Dessutom kan den [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) ger en omfattande rabatt när modellen vCore-baserade används. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Hur ofta kan jag justera resurser per pool?
Så ofta du vill. Se [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Hur lång tid tar det att ändra servicenivå för nivå eller prestanda för en enskild databas eller flytta en databas till och från en elastisk pool?
Ändra tjänstnivån för en databas och flyttas till och från en pool måste databasen som ska kopieras på plattformen som bakgrunden. Ändra tjänstnivån kan ta några minuter till flera timmar beroende på storleken på databaserna. I båda fallen förblir databaserna online och tillgänglig under flytten. Mer information om hur du ändrar enskilda databaser finns [ändra tjänstnivån för en databas](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>När bör jag använda en enskild databas jämfört med elastiska databaser?
I allmänhet elastiska pooler är utformade för att en typisk [programmönster för programvara som en tjänst (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), där det finns en databas per kund eller klient. Att köpa separata databaser och skaffa stora resurser som kan möta de varierande och de högsta behoven för varje databas är sällan en kostnadseffektiv lösning. Du hanterar den samlade prestandan för poolen med pooler, och databaserna skala upp eller ned automatiskt. Azures intelligent motorn rekommenderar en pool för databaser när en användningsmönstret på grund av den. Mer information finns i [elastisk pool vägledning](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Hur användningen av SQL-databas med DTU-baserade inköpsmodell visas på min faktura?
Växlar SQL-databas på en förutsägbar timvis avgift baserat på de [köp modellen](sql-database-service-tiers.md). Faktiska användningen beräknas och proportionerligt varje timma, så fakturan kan visa delar av en timme. Om det finns en databas för 12 timmar i månaden, visar fakturan användning av 0,5 dagar. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Vad händer om en enskild databas är aktiv för mindre än en timme eller använder en högre tjänstnivå för mindre än en timme?
Du debiteras för varje timme finns en databas med högsta tjänstnivån + prestandanivå som tillämpas under den timmen, oavsett användning eller om databasen var aktiv för mindre än en timme. Om du skapar en databas och ta bort den fem minuter senare visar fakturan en avgift för en databas timme. 

Exempel:

* Om du skapar en grundläggande databas och sedan omedelbart uppgradera den till Standard S1 debiteras du med Standard S1-hastighet för den första timmen.
* Om du uppgraderar en databas från Basic till Premium klockan 10:00. och att uppgraderingen har slutförts klockan 1:35 dagen debiteras du med Premium-hastighet som börjar vid 1:00 a.m. 
* Om du nedgradera en databas från Premium till Basic klockan 11:00 och den är klar kl. 2:15 sedan databasen debiteras med Premium-hastighet fram till 15:00. Därefter debiteras den med grundläggande priser.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Hur elastisk pool användning med DTU-baserade inköpsmodell visas på min faktura?
Elastisk pool debiterar visa upp på fakturan som elastiska dtu: er (edtu: er) eller vCores plus lagringsutrymme i steg som visas på [prissättningssidan](https://azure.microsoft.com/pricing/details/sql-database/). Det kostar ingenting per databas för elastiska pooler. Du debiteras för varje timme poolen finns på den högsta eDTU eller vCores, oavsett användning eller om poolen har aktiva för mindre än en timme. 

DTU-baserade köp modellen exempel:

* Om du skapar en Standard elastisk pool med 200 edtu: er på 11:18:00, debiteras lägger till fem databaser i poolen, du för 200 edtu: er för hela timme början på 11: 00 i resten av dagen.
* På dag 2 i 05:05:00, databasen 1 börjar förbruka 50 edtu: er och innehåller konstant genom dagen. Databaser 2-5 variera mellan 0 och 80 edtu: er. Under dagen, att lägga till fem andra databaser som använder olika edtu: er under dagen. Dag 2 är en hel dag faktureras enligt 200 eDTU. 
* På dag 3, 5 klockan du lägger till en annan 15 databaser. Databasanvändningen ökar under dagen till den punkt där du vill öka edtu: er för poolen från 200 till 400 kl. 8:05 Avgifter på nivån 200 eDTU gällde till 20: 00 och ökar till 400 edtu: er för återstående fyra timmar. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Hur elastisk pool debiteras för den DTU-baserade köper modellen?
Elastiska pooler debiteras per följande egenskaper:

* En elastisk pool debiteras när skapades, även om det finns inga databaser i poolen.
* En elastisk pool faktureras timvis. Det här är samma avläsning frekvens som prestandanivåer för enskilda databaser.
* Om du ändrar storlek på en elastisk pool sedan faktureras poolen inte enligt den nya mängden resurser förrän storleksändring åtgärden har slutförts. Det följer samma mönster som ändrar prestandanivåerna för enskilda databaser.
* Priset på en elastisk pool baseras på resurser av poolen. Priset på en elastisk pool är oberoende av antalet och användning av de elastiska databaserna i den.

Mer information finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) och [tjänstnivåer](sql-database-service-tiers.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Hur vCore-baserade användningen visas i min faktura? 
I modellen vCore-baserade tjänsten faktureras på en avgift för förutsägbar, per timme baserat på tjänstnivån, etablerade beräkning i vCores, etablerats lagringsutrymme i GB/månad och förbrukad lagring för säkerhetskopiering. Om lagring av säkerhetskopiering överskrider den totala databasstorleken (det vill säga 100% av databasens storlek), finns det ytterligare avgifter. vCore timmar, konfigurerade databaslagring, förbrukade-i/o och lagring för säkerhetskopiering är tydligt specificerade i växeln, vilket gör det enklare att se detaljer för resurser som du har använt. Säkerhetskopiera lagring på upp till 100% av den maximala databasstorleken ingår, utöver som du debiteras i GB/månad som används i en månad.

Exempel:
- Om SQL-databasen finns i 12 timmar i månaden, växeln Visar användningsinformation för vCore 12 timmar. Om SQL-databasen har etablerats ytterligare 100 GB lagringsutrymme, visar växeln lagringskvoten i enheter om GB/månad proportionellt fördelad varje timme och antal IOs som används i en månad.
- Om SQL-databasen är aktiv för mindre än en timme, debiteras du för varje timme databasen finns med högsta tjänstnivån markerad etablerats lagrings- och -i/o som tillämpas under den timmen, oavsett användning eller om databasen var aktiv för mindre än en timme.
- Om du skapar en instans som hanteras och ta bort den fem minuter senare debiteras du för en databas timme.
- Om du skapar en hanterad instans i nivån generell användning med 8 vCores och därefter genast uppgraderar den till 16 vCores debiteras du 16 vCore-kostnaden för den första timmen.

> [!NOTE]
> Under en begränsad period via juni 30 2018 är säkerhetskopiering avgifter och kostnader för i/o kostnadsfria.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Hur stöder användning av aktiv geo-replikering i en elastisk pool visas på min faktura?
Till skillnad från enskilda databaser, med hjälp av [aktiv geo-replikering](sql-database-geo-replication-overview.md) med elastiska databaser inte har en direkt inverkan för fakturering.  Endast debiteras du för de resurser som etablerades för varje pooler (primär och sekundär pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Hur påverkar användningen av granskningsfunktionen fakturan?
Granskning är inbyggd i SQL Database-tjänsten utan extra kostnad och är tillgänglig på alla servicenivåer. Men om du vill lagra granskningsloggarna utifrån granskning funktionen använder ett Azure Storage-konto och priser för tabeller och köer i Azure Storage gäller storleken på din granskningslogg.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Hur jag för att återställa lösenord för serveradministratören?
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL Servers**, välja en server i listan och klicka sedan på **Återställ lösenord**.

## <a name="how-do-i-manage-databases-and-logins"></a>Hur hanterar jag databaser och inloggningar?
Se [hantera databaser och inloggningar](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Hur kan jag vara säker på att endast auktoriserade IP-adresser har tillgång till en server?
Se [så här: konfigurera brandväggsinställningar på SQL-databas](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Vad är en förväntad replikeringsfördröjning vid geo-replikering en databas mellan två regioner inom samma Azure geografi?
Vi stöder för närvarande ett Återställningpunktsmål fem sekunder och replikeringsfördröjning har mindre än att när geo-sekundär finns i Azure bör parad region och på samma tjänstnivån.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Vad är en förväntad replikeringsfördröjning när geo sekundär skapas i samma region som den primära databasen?
Baserat på uteslutande data, finns det inte för mycket skillnaden mellan intra-region och mellan region replikeringsfördröjning när Azure rekommenderar region parad används. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Om det finns ett fel i nätverket mellan två regioner, hur logik för omprövning fungerar när geo-replikering konfigureras?
Om det finns ett kopplas från, gör vi var 10: e sekund för att återupprätta anslutningarna.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Vad gör jag för att säkerställa att viktiga förändringar i den primära databasen replikeras?
Geo-sekundär är en asynkron replikering och vi gör inga försök att behållas i fullständig synkronisering med primärt. Men vi ger en metod för att tvinga synkronisering så replikering av viktiga ändringar (till exempel lösenordsuppdateringar). Framtvingad synkronisering påverkar prestanda eftersom den anropande tråden blockeras tills alla genomförda transaktioner replikeras. Mer information finns i [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Vilka verktyg är tillgängliga för att övervaka replikeringsfördröjning mellan den primära databasen och geo sekundär?
Använda realtid replikeringsfördröjning mellan den primära databasen och geo sekundära via en DMV. Mer information finns i [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Flytta en databas till en annan server i samma prenumeration
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, Välj en databas i listan och klicka sedan på **kopiera**. Se [kopiera en Azure SQL database](sql-database-copy.md) för mer information.

## <a name="to-move-a-database-between-subscriptions"></a>Flytta en databas mellan prenumerationer
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-servrar** och välj sedan den server som är värd för databasen i listan. Klicka på **flytta**, och välj sedan resurserna som ska flyttas och prenumeration att flytta till.
