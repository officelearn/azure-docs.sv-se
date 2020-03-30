---
title: Välja rätt distributionstyp - Azure Database for MySQL
description: I den här artikeln beskrivs vilka faktorer du bör tänka på innan du distribuerar Azure Database för MySQL som antingen infrastruktur som en tjänst (IaaS) eller plattform som en tjänst (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255536"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Välj rätt MySQL Server-alternativ i Azure

Med Azure kan dina MySQL-serverarbetsbelastningar köras i en värddatorinfrastruktur som en tjänst (IaaS) eller som en värdplattform som en tjänst (PaaS). PaaS har flera distributionsalternativ och det finns tjänstnivåer inom varje distributionsalternativ. När du väljer mellan IaaS och PaaS måste du bestämma om du vill hantera databasen, använda korrigeringar och göra säkerhetskopior eller om du vill delegera dessa åtgärder till Azure.

När du fattar ditt beslut bör du tänka på följande två alternativ:

- **Azure-databas för MySQL**. Det här alternativet är en fullständigt hanterad MySQL-databasmotor baserad på den stabila versionen av MySQL community edition. Den här relationsdatabasen som en tjänst (DBaaS), som finns på Azure-molnplattformen, hör till branschkategorin PaaS.

  Med en hanterad instans av MySQL på Azure kan du använda inbyggda funktioner som annars kräver omfattande konfiguration när MySQL Server är antingen lokal eller i en Azure VM.

  När du använder MySQL som en tjänst betalar du allteftersom med alternativ för att skala upp eller skala ut för större kontroll utan avbrott. Och till skillnad från fristående MySQL Server har Azure Database for MySQL ytterligare funktioner som inbyggd hög tillgänglighet, intelligens och hantering.

- **MySQL på virtuella Azure-datorer**. Detta alternativ hör till branschkategorin IaaS. Med den här tjänsten kan du köra MySQL Server inuti en fullständigt hanterad virtuell dator på Azure-molnplattformen. Alla senaste versioner och utgåvor av MySQL kan installeras på en virtuell IaaS-dator.

  I den mest betydande skillnaden från Azure Database för MySQL erbjuder MySQL på virtuella Azure-datorer kontroll över databasmotorn. Den här kontrollen sker dock på bekostnad av ansvaret för att hantera de virtuella datorerna och många databasadministrationsuppgifter (DBA). Dessa uppgifter omfattar underhåll och korrigering av databasservrar, databasåterställning och design med hög tillgänglighet.

De största skillnaderna mellan dessa alternativ visas i följande tabell:

|            | Azure Database for MySQL | MySQL på virtuella Azure-datorer    |
|:-------------------|:-----------------------------|:--------------------|
| Servicenivåavtal (SLA)                | Erbjuder SLA på 99,99% tillgänglighet| Upp till 99,95 % tillgänglighet med två eller flera instanser i samma tillgänglighetsuppsättning.<br/><br/>99,9 % tillgänglighet med en virtuell dator med en instans med premiumlagring.<br/><br/>99,99 % med hjälp av tillgänglighetszoner med flera instanser i flera tillgänglighetsuppsättningar.<br/><br/>Se [SLA för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Korrigering av operativsystem        | Automatisk  | Hanteras av kunder |
| MySQL-korrigering     | Automatisk  | Hanteras av kunder |
| Hög tillgänglighet | Ha-modellen (High Availability) baseras på inbyggda redundansmekanismer för när ett avbrott på nodnivå inträffar. I sådana fall skapar tjänsten automatiskt en ny instans och kopplar lagring till den här instansen. | Kunder arkitekt, genomföra, testa och upprätthålla hög tillgänglighet. Funktioner kan omfatta klustring, replikering etc.|
| Zon redundans | Stöds för närvarande inte | Virtuella Azure-datorer kan ställas in för att köras i olika tillgänglighetszoner. För en lokal lösning måste kunderna skapa, hantera och underhålla sitt eget sekundära datacenter.|
| Hybridscenarier | Med [Data-in Replication](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)kan du synkronisera data från en extern MySQL-server till Azure Database for MySQL-tjänsten. Den externa servern kan vara lokal, i virtuella datorer eller en databastjänst som finns hos andra molnleverantörer.<br/><br/> Med [funktionen läsreplik](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) kan du replikera data från en Azure-databas för MySQL-huvudserver till upp till fem skrivskyddade replikservrar. Replikerna är antingen inom samma Azure-region eller mellan regioner. Skrivskyddade repliker uppdateras asynkront med hjälp av binlogreplikeringsteknik.| Hanteras av kunder
| Säkerhetskopiering och återställning | Skapar automatiskt [säkerhetskopiering](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) av servrar och lagrar dem i användarkonfigurerad lagring som antingen är lokalt redundant eller geouppsagd. Tjänsten tar fullständiga, differentiella och transaktionsloggsäkerheter | Hanteras av kunder |
| Övervaka databasåtgärder | Ger kunderna möjlighet att [ställa in aviseringar](https://docs.microsoft.com/azure/mysql/concepts-monitoring) på databasåtgärden och agera när de når tröskelvärden. | Hanteras av kunder |
| Advanced Threat Protection | Ger [avancerat skydd mot hot](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Det här skyddet identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. | Kunderna måste bygga detta skydd för sig själva.
| Haveriberedskap | Lagrar automatiska säkerhetskopior i användarkonfigurerad [lokalt redundant eller geoupptundssant lagring](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Säkerhetskopior kan också återställa en server till en tidpunkt. Lagringsperioden är allt från 7 till 35 dagar. Återställningen utförs med hjälp av Azure-portalen. | Fullt förvaltad av kunderna. Ansvaret omfattar men är inte begränsade till schemaläggning, testning, arkivering, lagring och kvarhållning. Ett ytterligare alternativ är att använda ett Azure Recovery Services-valv för att säkerhetskopiera virtuella Azure-datorer och databaser på virtuella datorer. Det här alternativet är i förhandsgranskning. |
| Prestandarekommendationer | Ger kunderna [prestandarekommendationer](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) baserade på systemgenererade användningsloggfiler. Rekommendationerna hjälper till att optimera arbetsbelastningar. | Hanteras av kunder |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Affärsmotiv för att välja PaaS eller IaaS

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS som värd för dina MySQL-databaser.

### <a name="cost"></a>Kostnad

Begränsad finansiering är ofta den primära faktor som avgör den bästa lösningen för att vara värd för dina databaser. Detta gäller oavsett om du är en startup med lite pengar eller ett team i ett etablerat företag som verkar under snäva budgetbegränsningar. I det här avsnittet beskrivs grunderna för fakturering och licensiering i Azure när de gäller för Azure Database för MySQL och MySQL på virtuella Azure-datorer.

#### <a name="billing"></a>Fakturering

Azure Database for MySQL är för närvarande tillgänglig som en tjänst på flera nivåer med olika priser för resurser. Alla resurser faktureras varje timme till en fast ränta. Den senaste informationen om de tjänstnivåer som för närvarande stöds, beräkningsstorlekar och lagringsbelopp finns i [vCore-baserad inköpsmodell](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Du kan justera tjänstnivåer och beräkningsstorlekar dynamiskt så att de matchar programmets olika dataflödesbehov. Du faktureras för utgående Internettrafik med regelbundna [dataöverföringshastigheter](https://azure.microsoft.com/pricing/details/data-transfers/).

Med Azure Database for MySQL konfigurerar, korrigeras och uppgraderas databasprogramvaran automatiskt. Dessa automatiserade åtgärder minskar dina administrationskostnader. Dessutom har Azure Database for MySQL [inbyggda funktioner för säkerhetskopiering.](https://docs.microsoft.com/azure/mysql/concepts-backup) Dessa funktioner hjälper dig att uppnå betydande kostnadsbesparingar, särskilt när du har ett stort antal databaser. Däremot med MySQL på Azure virtuella datorer kan du välja och köra alla MySQL-versioner. Oavsett vilken MySQL-version du använder betalar du för den etablerade virtuella datorn och kostnaderna för den specifika MySQL-licenstypen som används.

Azure Database for MySQL ger inbyggd hög tillgänglighet för alla typer av avbrott på nodnivå samtidigt som 99,99 % SLA-garantin för tjänsten bibehålls. För databas hög tillgänglighet inom virtuella datorer bör kunderna dock använda de alternativ för hög tillgänglighet som [MySQL-replikering](https://dev.mysql.com/doc/refman/8.0/en/replication.html) som är tillgängliga i en MySQL-databas. Att använda ett alternativ med hög tillgänglighet som stöds ger inte ett ytterligare serviceavtal. Men det gör att du kan uppnå större än 99,99% databastillgänglighet till extra kostnad och administrativa omkostnader.

Mer information om priser finns i följande artiklar:
* [Azure-databas för MySQL-priser](https://azure.microsoft.com/pricing/details/mysql/)
* [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure priskalkylator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag handlar beslutet att gå över till en molntjänst lika mycket om att avlasta komplexiteten i administrationen som om kostnaden. Med IaaS och PaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Replikerar automatiskt alla data för att tillhandahålla haveriberedskap.
- Konfigurerar och uppgraderar databasprogramvaran.
- Hanterar belastningsutjämning.
- Har transparent fail-over om det finns en server fel.

I följande lista beskrivs administrativa överväganden för varje alternativ:

* Med Azure Database for MySQL kan du fortsätta att administrera databasen. Men du behöver inte längre hantera databasmotorn, operativsystemet eller maskinvaran. Exempel på objekt som du kan fortsätta att administrera är:

  - Databaser
  - Logga in
  - Indexjustering
  - Frågejustering
  - Granskning
  - Säkerhet

  Dessutom kräver konfiguration av hög tillgänglighet till ett annat datacenter minimal till ingen konfiguration eller administration.

* Med MySQL på virtuella Azure-datorer har du full kontroll över operativsystemet och MySQL-serverinstanskonfigurationen. Med en virtuell dator bestämmer du när du ska uppdatera eller uppgradera operativsystemet och databasprogramvaran. Du bestämmer också när du ska installera ytterligare program, till exempel ett antivirusprogram. Vissa automatiserade funktioner tillhandahålls för att avsevärt förenkla korrigering, säkerhetskopiering och hög tillgänglighet. Du kan styra storleken på den virtuella datorn, antalet diskar och deras lagringskonfigurationer. Mer information finns i [Storlekar för virtuella datorer och molntjänster för Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Dags att flytta till Azure

* Azure Database for MySQL är rätt lösning för molndesignade program när utvecklares produktivitet och snabb tid till marknaden för nya lösningar är avgörande. Med programmatisk funktionalitet som är som DBA, tjänsten är lämplig för moln arkitekter och utvecklare eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

* När du vill undvika tid och kostnader för att skaffa ny lokal maskinvara är MySQL på virtuella Azure-datorer rätt lösning för program som kräver en MySQL-databas eller åtkomst till MySQL-funktioner på Windows eller Linux. Den här lösningen är också lämplig för att migrera befintliga lokala program och databaser till Azure intakt, för fall där Azure Database for MySQL passar dåligt.

  Eftersom du inte behöver ändra presentations-, program- och datalager sparar du tid och budget för att ändra din befintliga lösning bakåt. I stället kan du fokusera på att migrera alla dina lösningar till Azure och ta itu med vissa prestandaoptimeringar som Azure-plattformen kan kräva.

## <a name="next-steps"></a>Nästa steg

* Se [Azure Database för MySQL-priser](https://azure.microsoft.com/pricing/details/MySQL/).
* Kom igång genom [att skapa din första server](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
