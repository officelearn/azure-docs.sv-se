---
title: Välja rätt distributions typ för din Azure Database for MySQL
description: I den här artikeln beskrivs vad du behöver göra innan du fortsätter med IaaS (Infrastructure-as-a-Service) eller PaaS (Platform-as-a-Service) för din Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7bcbf379ea8d046c8c477dc716711a6a6ffa1dc9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813839"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Välj rätt MySQL Server-alternativ i Azure

Med Azure kan dina MySQL server-arbetsbelastningar köras i en virtuell dator med en värdbaserad infrastruktur (IaaS) eller som en värdbaserad tjänst (PaaS). PaaS har flera distributions alternativ och det finns tjänst nivåer inom varje distributions alternativ. När du väljer mellan PaaS eller IaaS måste du bestämma om du vill hantera din databas, använda korrigeringar och göra säkerhets kopior, eller om du vill delegera dessa åtgärder till Azure.</br>

Överväg följande alternativ baserat på ditt svar på frågan: <br/>

**Azure Database for MySQL** är en fullständigt hanterad MySQL-databasmotor som baseras på den stabila versionen av community-versionen. Den här Relations databas-som-tjänst (DBaaS), som finns i Azure-molnet, hamnar i bransch kategorin för PaaS (Platform-as-a-Service). Med en hanterad instans av MySQL på Azure kan du använda inbyggda funktioner och funktioner som kräver omfattande konfiguration när du använder MySQL server (antingen lokalt eller i en virtuell Azure-dator). När du använder MySQL som en tjänst betalar du per användning med alternativ för att skala upp eller ut för bättre kraft utan avbrott. Till skillnad från fristående MySQL-server har Azure Database for MySQL dessutom ytterligare funktioner, till exempel inbyggd hög tillgänglighet, intelligens och hantering. <br/><br/>
**MySQL på den virtuella Azure-datorn** tillhör bransch kategorin Infrastructure-as-a-Service (IaaS) och gör att du kan köra MySQL server i en fullständigt hanterad virtuell dator i Azure-molnet. Alla nya versioner och utgåvor av MySQL kan installeras på en virtuell IaaS-dator. Den viktigaste skillnaden från Azure Database for MySQL är att MySQL på virtuella Azure-datorer tillåter kontroll över databas motorn. Den här kontrollen kommer dock att ha till gång till kostnaden för ytterligare ansvar för att hantera de virtuella datorerna och flera DBA-uppgifter, till exempel underhåll/korrigeringar av databas server, återställning och hög tillgänglighets design osv.

De huvudsakliga skillnaderna mellan dessa alternativ visas i följande tabell:

|            | **Azure Database for MySQL** | **MySQL på virtuella Azure-datorer**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | Erbjuder SLA med 99,99% tillgänglighet| Upp till 99,95% tillgänglighet med två eller fler instanser i samma tillgänglighets uppsättning. <br/>99,9% virtuell dator med en instans med Premium Storage <br/> 99,99% med tillgänglighets zon med 2 eller mer instans i 2 eller mer tillgänglighets uppsättning.<br/> Obs! [service avtal för virtuell dator](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **OS-uppdatering**        | Automatisk  | Hanteras av kunder |
|**MySQL-korrigering**     | Automatisk  | Hanteras av kunder |
| **Hög tillgänglighet** | Modellen med hög tillgänglighet (HA) baseras på inbyggda funktioner för redundansväxling när ett avbrott uppstår på nod-nivå. I sådana fall skapar tjänsten automatiskt en instans och kopplar lagring till den nya instansen. | Hög tillgänglighet är konstruerad, implementerad, testad och underhålls av kunden. Detta kan omfatta Always On (redundanskluster eller gruppreplikering), logg överföring och Transaktionsreplikering, beroende på vilken version av MySQL-motorn som används.|
| **Zon redundans** | Stöds inte för närvarande. | Virtuella Azure-datorer kan konfigureras för att köras i olika tillgänglighets zoner. För en lokal lösning förväntas kunderna skapa, hantera och underhålla sina egna sekundära Data Center.|
| **Hybrid-scenarier** | Med [datareplikering](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) kan du synkronisera data från en extern MySQL-server till tjänsten Azure Database for MySQL. Den externa servern kan vara lokalt, i virtuella datorer eller i en databas tjänst som är värd för andra moln leverantörer.  <br/> <br/> Med funktionen [Läs replik](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) kan du replikera data från en Azure Database for MySQL server (Master) till upp till fem skrivskyddade servrar (repliker) inom samma Azure-region eller mellan regioner. Skrivskyddade repliker uppdateras asynkront med BinLog-replikering.   <br/> <br/> Obs! Läs replikering mellan regioner är för närvarande en offentlig för hands version.| Hanteras av kunder <br/>
| **Säkerhetskopiering och återställning** | Skapar automatiskt [Server säkerhets kopior](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) och lagrar dem i användare som har konfigurerat lokalt redundant eller Geo-redundant lagring. Tjänsten tar full, differentiell och transaktions logg säkerhets kopior | Hanteras av kunder |
| **Övervaka databas åtgärder** | Gör det möjligt för kunder att [Ange aviseringar](https://docs.microsoft.com/azure/mysql/concepts-monitoring) för databas åtgärden och vidta åtgärder för att nå tröskelvärden. | Hanteras av kunder |
| **Avancerat tråd skydd** | Tillhandahåller [Avancerat skydd](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) som identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. | Kunderna måste själva bygga själva.
| **Säkerhets kopieringar (haveri beredskap)** | Lagrar automatiska säkerhets kopieringar i användare som kon figurer ATS [lokalt redundant eller Geo-redundant lagring](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Säkerhets kopieringar kan också användas för att återställa en server till en tidpunkt. Kvarhållningsperioden kan anges från 7-35 dagar. Återställningen kan utföras med hjälp av Azure Portal. <br/> | Fullständigt hanterad av kunden, inklusive men inte begränsat till schemaläggning, testning, arkivering, lagring och kvarhållning. Ett annat alternativ är att använda Azure Recovery Services Vault för att säkerhetskopiera virtuella Azure-datorer och databaser på virtuella datorer (för hands version). |
| **Prestanda rekommendation** | Ger kunderna proaktiva [prestanda rekommendationer](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) baserat på användningen av telemetri för att hjälpa till att optimera arbets belastningar. | Hanteras av kunder |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Affärs motivation för att välja PaaS eller IaaS

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS för att vara värd för MySQL-databaserna:

### <a name="cost"></a>Kostnad

Oavsett om du är en start dålig för kontanter eller ett team i ett etablerat företag som arbetar under tätt budget begränsningar, är den begränsade finansieringen ofta den främsta överväganden när du fastställer den bästa lösningen för att vara värd för dina databaser. Det här avsnittet beskriver grundläggande om fakturering och licensiering i Azure med avseende på Azure Database for MySQL och MySQL på virtuella Azure-datorer:

#### <a name="billing"></a>Fakturering

För närvarande är Azure Database for MySQL tillgänglig som en tjänst på flera nivåer med olika priser för resurser, som alla debiteras per timme med en fast taxa. Den senaste informationen om aktuella tjänst nivåer, beräknings storlek och lagrings utrymme som stöds finns i [vCore-baserad inköps modell](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov. Du debiteras för utgående Internet trafik med regelbundna [data överförings kostnader](https://azure.microsoft.com/pricing/details/data-transfers/).

Med Azure Database for MySQL konfigureras databas program varan automatiskt, korrigeras och uppgraderas av Microsoft, vilket minskar administrations kostnaderna. Dessutom gör dess [inbyggda säkerhetskopierings](https://docs.microsoft.com/azure/mysql/concepts-backup)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser. Med MySQL på virtuella Azure-datorer kan du välja och köra någon av MySQL-versionerna. Oavsett vilken MySQL-version du använder betalar du för den virtuella datorn och kostnaderna för den speciella licens typen som används för MySQL.

Azure Database for MySQL tillhandahåller hög tillgänglighet för alla typer av noder på radnivå och behåller fortfarande service avtalet på 99,99% för tjänsten. För databas hög tillgänglighet (HA) inom de virtuella datorerna ska kunden dock gå igenom de alternativ för hög tillgänglighet som finns i MySQL-databasen, till exempel [MySQL-replikering](https://dev.mysql.com/doc/refman/8.0/en/replication.html). Att använda ett alternativ för hög tillgänglighet som stöds ger inte ytterligare ett service avtal, men gör att du kan uppnå > 99,99% databas tillgänglighet till ytterligare kostnad och administrativa kostnader.

Mer information om priser finns i följande resurser:
* [Azure Database for MySQL priser](https://azure.microsoft.com/pricing/details/mysql/)
* [Prissättning för virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag är beslutet att gå över till en moln tjänst lika mycket om att avlasta administrations komplexitet. Med IaaS och PaaS, administrerar Microsoft den underliggande infrastrukturen och replikerar automatiskt alla data för att tillhandahålla haveri beredskap, konfigurera och uppgradera databasens program vara, hanterar belastnings utjämning och transparent redundans om det finns en Server haveri.

* **Med Azure Database for MySQL**kan du fortsätta att administrera din databas, men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom kräver en minimal eller ingen konfiguration eller administration om du konfigurerar hög tillgänglighet till ett annat data Center.<br/><br/>
* **Med MySQL på virtuella Azure-datorer**har du fullständig kontroll över konfiguration av operativ system och MySQL-serverinstansen. Med en virtuell dator bestämmer du när du vill uppdatera/uppgradera operativ systemet och databas programmet och när du ska installera ytterligare program vara, till exempel ett antivirus program. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Mer information finns i virtuella datorer och moln tjänst storlekar för Azure.

### <a name="time-to-move-to-azure-br"></a>Tid att flytta till Azure <br/>
* **Azure Database for MySQL** är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tids till marknad för nya lösningar är viktiga. Med program mässig DBA-liknande funktioner är tjänsten perfekt för moln arkitekter och utvecklare, eftersom det minskar behovet av att hantera det underliggande operativ systemet och databasen.<br/><br/>
* **MySQL på virtuella Azure-datorer** är perfekt för befintliga eller nya program som kräver MySQL-databas eller åtkomst till funktioner i MySQL-databasen på Windows/Linux och du vill undvika tid och kostnader för att förvärva ny lokal maskin vara. Det här alternativet är också en bra plats för att migrera befintliga lokala program och databaser till Azure som de är – för de fall där en Azure Database for MySQL instans inte passar bra. Eftersom du inte behöver ändra presentations-, program-och data lager sparar du tid och budget när du skapar en ny konstruktion för din befintliga lösning. I stället kan du fokusera på att migrera alla dina lösningar till Azure och för att åtgärda vissa prestanda optimeringar som kan krävas av Azure-plattformen.

## <a name="next-steps"></a>Nästa steg

* Se [Azure Database for MySQL priser](https://azure.microsoft.com/pricing/details/mysql/)
* Komma igång genom att [skapa din första server](https://review.docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

