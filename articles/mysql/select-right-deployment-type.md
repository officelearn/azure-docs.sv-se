---
title: Välja rätt distributions typ – Azure Database for MySQL
description: I den här artikeln beskrivs vilka faktorer du bör tänka på innan du distribuerar Azure Database for MySQL som antingen infrastruktur som en tjänst (IaaS) eller Platform as a Service (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255536"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Välj rätt MySQL Server-alternativ i Azure

Med Azure kan dina MySQL server-arbetsbelastningar köras i en värdbaserad virtuell dator infrastruktur som en tjänst (IaaS) eller som en tjänst som värd plattform som en tjänst (PaaS). PaaS har flera distributions alternativ och det finns tjänst nivåer inom varje distributions alternativ. När du väljer mellan IaaS och PaaS måste du bestämma om du vill hantera din databas, tillämpa korrigeringar och göra säkerhets kopior, eller om du vill delegera dessa åtgärder till Azure.

När du fattar ditt beslut bör du tänka på följande två alternativ:

- **Azure Database for MySQL**. Det här alternativet är en fullständigt hanterad MySQL-databasmotor baserat på den stabila versionen av MySQL Community Edition. Den här Relations databas som en tjänst (DBaaS), som finns på Azure-molnets plattform, hamnar i bransch kategorin för PaaS.

  Med en hanterad instans av MySQL på Azure kan du använda inbyggda funktioner som annars kräver omfattande konfiguration när MySQL-servern är antingen lokalt eller i en virtuell Azure-dator.

  När du använder MySQL som en tjänst betalar du per användning med alternativ för att skala upp eller ut för bättre kontroll utan avbrott. Till skillnad från fristående MySQL-server har Azure Database for MySQL ytterligare funktioner som inbyggd hög tillgänglighet, intelligens och hantering.

- **MySQL på virtuella Azure-datorer**. Det här alternativet är inställt på IaaS för bransch kategorin. Med den här tjänsten kan du köra MySQL server i en helt hanterad virtuell dator på Azures moln plattform. Alla nya versioner och utgåvor av MySQL kan installeras på en virtuell IaaS-dator.

  I den viktigaste skillnaden från Azure Database for MySQL ger MySQL på virtuella Azure-datorer kontroll över databas motorn. Den här kontrollen kommer dock att ansvara för att hantera de virtuella datorerna och många databas administrations uppgifter (DBA). Dessa uppgifter omfattar att underhålla och korrigera databas servrar, databas återställning och design med hög tillgänglighet.

De huvudsakliga skillnaderna mellan dessa alternativ visas i följande tabell:

|            | Azure Database for MySQL | MySQL på virtuella Azure-datorer    |
|:-------------------|:-----------------------------|:--------------------|
| Service nivå avtal (SLA)                | Erbjuder SLA med 99,99% tillgänglighet| Upp till 99,95% tillgänglighet med två eller fler instanser i samma tillgänglighets uppsättning.<br/><br/>99,9% tillgänglighet med en enskild instans av en virtuell dator med Premium Storage.<br/><br/>99,99% med Tillgänglighetszoner med flera instanser i flera tillgänglighets uppsättningar.<br/><br/>Se [Virtual Machines service avtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Uppdatering av operativ system        | Automatisk  | Hanteras av kunder |
| MySQL-korrigering     | Automatisk  | Hanteras av kunder |
| Hög tillgänglighet | Modellen med hög tillgänglighet (HA) baseras på inbyggda funktioner för redundansväxling för när ett avbrott på nod-nivå sker. I sådana fall skapar tjänsten automatiskt en ny instans och kopplar lagring till den här instansen. | Kunders arkitekt, implementera, testa och underhålla hög tillgänglighet. Funktioner kan vara klustring, replikering osv.|
| Zon redundans | Stöds inte för närvarande | Virtuella Azure-datorer kan konfigureras för att köras i olika tillgänglighets zoner. För en lokal lösning måste kunderna skapa, hantera och underhålla ett eget sekundärt Data Center.|
| Hybrid scenarier | Med [datareplikering](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)kan du synkronisera data från en extern MySQL-server till tjänsten Azure Database for MySQL. Den externa servern kan vara lokalt, i virtuella datorer eller i en databas tjänst som är värd för andra moln leverantörer.<br/><br/> Med funktionen [Läs replik](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) kan du replikera data från en Azure Database for MySQL huvud server till upp till fem skrivskyddade replik servrar. Replikerna är antingen inom samma Azure-region eller i flera regioner. Skrivskyddade repliker uppdateras asynkront med BinLog-replikering.| Hanteras av kunder
| Säkerhets kopiering och återställning | Skapar automatiskt [Server säkerhets kopior](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) och lagrar dem i användar konfigurations lagring som antingen är lokalt redundant eller Geo-redundant. Tjänsten tar full, differentiell och transaktions logg säkerhets kopior | Hanteras av kunder |
| Övervaka databas åtgärder | Ger kunderna möjlighet att [ställa in aviseringar](https://docs.microsoft.com/azure/mysql/concepts-monitoring) för databas åtgärden och vidta åtgärder för att nå tröskelvärden. | Hanteras av kunder |
| Advanced Threat Protection | Tillhandahåller [Avancerat skydd](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Det här skyddet identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. | Kunderna måste bygga det här skyddet för sig själva.
| Haveriberedskap | Lagrar automatiserade säkerhets kopieringar i användar konfiguration [lokalt redundant eller Geo-redundant lagring](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Säkerhets kopieringar kan också återställa en server till en tidpunkt. Kvarhållningsperioden är var som helst från 7 till 35 dagar. Återställningen utförs med hjälp av Azure Portal. | Fullständigt hanterad av kunder. Ansvaret omfattar men är inte begränsat till schemaläggning, testning, arkivering, lagring och kvarhållning. Ett annat alternativ är att använda ett Azure Recovery Services-valv för att säkerhetskopiera virtuella Azure-datorer och databaser på virtuella datorer. Det här alternativet är i för hands version. |
| Prestandarekommendationer | Ger kunderna [prestanda rekommendationer](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) baserat på systemgenererade användnings loggar. Rekommendationerna hjälper till att optimera arbets belastningar. | Hanteras av kunder |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Affärs motivation för att välja PaaS eller IaaS

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS för att vara värd för MySQL-databaserna.

### <a name="cost"></a>Kostnad

Begränsad finansiering är ofta den främsta överväganden som avgör den bästa lösningen för dina databaser. Detta gäller oavsett om du är en start med lite kontant eller ett team i ett etablerat företag som arbetar under tätt budget begränsningar. I det här avsnittet beskrivs grundläggande om fakturering och licensiering i Azure eftersom de gäller för Azure Database for MySQL och MySQL på virtuella Azure-datorer.

#### <a name="billing"></a>Fakturering

Azure Database for MySQL är för närvarande tillgängligt som en tjänst på flera nivåer med olika priser för resurser. Alla resurser debiteras per timme med ett fast pris. Den senaste informationen om de tjänst nivåer, beräknings storlekar och lagrings belopp som stöds för närvarande finns i [vCore-baserad inköps modell](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov. Du debiteras för utgående Internet trafik med regelbundna [data överförings kostnader](https://azure.microsoft.com/pricing/details/data-transfers/).

Med Azure Database for MySQL konfigurerar Microsoft automatiskt, patchar och uppgraderar databas program varan. Dessa automatiserade åtgärder minskar dina administrations kostnader. Azure Database for MySQL har också [inbyggda funktioner för säkerhets kopiering](https://docs.microsoft.com/azure/mysql/concepts-backup) . Dessa funktioner hjälper dig att uppnå avsevärda kostnads besparingar, särskilt när du har ett stort antal databaser. Däremot kan du med MySQL på virtuella Azure-datorer välja och köra en MySQL-version. Oavsett vilken MySQL-version du använder betalar du för den etablerade virtuella datorn och kostnaderna för den speciella MySQL-licens typen som används.

Azure Database for MySQL ger en inbyggd hög tillgänglighet för alla typer av avbrott på nodnivå samtidigt som service avtalet för service avtalet för service avtal fortfarande 99,99 upprätthålls. För databas hög tillgänglighet i virtuella datorer bör dock kunderna använda alternativen för hög tillgänglighet som [MySQL-replikering](https://dev.mysql.com/doc/refman/8.0/en/replication.html) som är tillgängliga i en MySQL-databas. Att använda ett alternativ för hög tillgänglighet som stöds ger inte ytterligare ett service avtal. Men det gör att du kan uppnå mer än 99,99% tillgänglighet till databaser med extra kostnad och administrativa kostnader.

Mer information om priser finns i följande artiklar:
* [Azure Database for MySQL priser](https://azure.microsoft.com/pricing/details/mysql/)
* [Prissättning för virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag är beslutet att gå över till en moln tjänst lika mycket om att avlasta komplexiteten för administration eftersom det kostar mer. Med IaaS och PaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Replikerar automatiskt alla data för att tillhandahålla haveri beredskap.
- Konfigurerar och uppgraderar databasens program vara.
- Hanterar belastnings utjämning.
- Fungerar transparent om det uppstår ett Server haveri.

I följande lista beskrivs administrativa överväganden för varje alternativ:

* Med Azure Database for MySQL kan du fortsätta att administrera din databas. Men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på objekt som du kan fortsätta att administrera är:

  - Databaser
  - Logga in
  - Index justering
  - Fråga-justering
  - Granskning
  - Säkerhet

  Dessutom kräver en minimal konfiguration eller administration att konfigurera hög tillgänglighet till ett annat data Center.

* Med MySQL på virtuella Azure-datorer har du fullständig kontroll över operativ systemet och konfiguration av MySQL-serverinstansen. Med en virtuell dator bestämmer du när du vill uppdatera eller uppgradera operativ systemet och databas program varan. Du bestämmer också när du ska installera ytterligare program vara, till exempel ett antivirus program. Vissa automatiserade funktioner är till för att avsevärt förenkla korrigering, säkerhets kopiering och hög tillgänglighet. Du kan kontrol lera storleken på den virtuella datorn, antalet diskar och deras lagrings konfiguration. Mer information finns i [virtuella datorer och moln tjänst storlekar för Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Tid att flytta till Azure

* Azure Database for MySQL är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tid till marknaden för nya lösningar är viktiga. Med programmerings funktioner som fungerar som DBA är tjänsten lämplig för moln arkitekter och utvecklare, eftersom det minskar behovet av att hantera det underliggande operativ systemet och databasen.

* När du vill undvika tid och pengar på att förvärva ny lokal maskin vara är MySQL på virtuella Azure-datorer rätt lösning för program som kräver en MySQL-databas eller åtkomst till MySQL-funktioner i Windows eller Linux. Den här lösningen är också lämplig för att migrera befintliga lokala program och databaser till Azure intakt, i de fall där Azure Database for MySQL är en dålig anpassning.

  Eftersom du inte behöver ändra presentations-, program-och data lager, sparar du tid och budget för att bygga om din befintliga lösning. I stället kan du fokusera på att migrera alla dina lösningar till Azure och åtgärda vissa prestanda optimeringar som Azure-plattformen kan kräva.

## <a name="next-steps"></a>Nästa steg

* Se [Azure Database for MySQL prissättning](https://azure.microsoft.com/pricing/details/MySQL/).
* Komma igång genom att [skapa din första server](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
