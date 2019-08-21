---
title: Översikt över Azure SQL Database Managed instance | Microsoft Docs
description: I den här artikeln beskrivs Azure SQL Database Hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: ebf4f516b8f90ce2ba8b277281300ae3239821c5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640806"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Vad är Azure SQL Database Hanterad instans?

Hanterad instans är ett nytt distributions alternativ för Azure SQL Database, med 100% kompatibilitet med den senaste SQL Server lokala (Enterprise Edition) databas motorn, vilket ger en inbyggd [virtuell nätverks implementering (VNet)](../virtual-network/virtual-networks-overview.md) som åtgärdar vanliga säkerhets problem och en [affärs modell](https://azure.microsoft.com/pricing/details/sql-database/) är fördelaktig för lokala SQL Server kunder. Distributions modellen för hanterade instanser gör att befintliga SQL Server kunder kan lyfta och byta sina lokala program till molnet med minimala program-och databas ändringar. På samma gång bevarar den hanterade instansen distributions alternativet alla PaaS-funktioner (automatiska korrigeringar och versions uppdateringar, [automatiserade säkerhets kopieringar](sql-database-automated-backups.md), [hög tillgänglighet](sql-database-high-availability.md) ) som drastiskt minskar hanterings kostnader och TCO.

> [!IMPORTANT]
> För en lista över regioner där distributions alternativet för hanterade instanser är tillgängligt, se [regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions).

Följande diagram beskriver viktiga funktioner i hanterade instanser:

![viktiga funktioner](./media/sql-database-managed-instance/key-features.png)

Distributions modellen för hanterade instanser är utformad för kunder som vill migrera ett stort antal appar från lokala eller IaaS, självbyggda eller ISV-baserade miljöer till fullständigt hanterad PaaS-moln miljö, med så liten migrering som möjligt. Med hjälp av den helt automatiserade [tjänsten för data migration (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) i Azure kan kunderna lyfta och byta lokala SQL Server till en hanterad instans som erbjuder kompatibilitet med SQL Server lokala och fullständiga isolering av kund instanser med inbyggt VNet-stöd.  Med Software Assurance kan du byta ut befintliga licenser för rabatterade priser på en hanterad instans med hjälp av [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  En hanterad instans är det bästa migrerings målet i molnet för SQL Server instanser som kräver hög säkerhet och en omfattande programmerings yta.

Distributions alternativet för hanterade instanser ger nära-100% Surface Area-kompatibilitet med den senaste lokala SQL Server versionen via en stegad versions plan.

För att välja mellan Azure SQL Database distributions alternativ: enkel databas, poolad databas och hanterad instans och SQL Server som finns på den virtuella datorn, se [hur du väljer rätt version av SQL Server i Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Viktiga funktioner och funktioner

Den hanterade instansen kombinerar de bästa funktionerna som finns tillgängliga både i Azure SQL Database och SQL Server databas motor.

> [!IMPORTANT]
> En hanterad instans körs med alla funktioner i den senaste versionen av SQL Server, inklusive online-åtgärder, automatiska plan korrigeringar och andra förbättringar i företags prestanda. En jämförelse av funktionerna som är tillgängliga förklaras i [jämförelse av funktioner: Azure SQL Database jämfört med](sql-database-features.md)SQL Server.

| **PaaS-förmåner** | **Verksamhetskontinuitet** |
| --- | --- |
|Ingen maskin varu inköp och-hantering <br>Ingen hanterings kostnad för hantering av underliggande infrastruktur <br>Snabb etablering och tjänst skalning <br>Automatiserad uppdatering och versions uppgradering <br>Integrering med andra PaaS Data Services |SLA för 99,99% drift tid  <br>Inbyggd [hög tillgänglighet](sql-database-high-availability.md) <br>Data som skyddas med [automatiserade säkerhets kopieringar](sql-database-automated-backups.md) <br>Kvarhållning av kundens bevarande period för säkerhets kopiering <br>Användarinitierad [säkerhets kopieringar](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>Återställnings funktion [för tidpunkts databas](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Säkerhet och efterlevnad** | **Hantering**|
|Isolerad miljö ([VNet-integrering](sql-database-managed-instance-connectivity-architecture.md), enskild klient tjänst, dedikerad beräkning och lagring) <br>[Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-autentisering](sql-database-aad-authentication.md), stöd för enkel inloggning <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD server-Huvudkonton (inloggningar)</a> (**offentlig för hands version**) <br>Följer standarderna för efterlevnad på samma sätt som Azure SQL Database <br>[SQL-granskning](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager-API för automatisering av tjänst etablering och skalning <br>Azure Portal funktioner för manuell tjänst etablering och skalning <br>Data migration service

> [!IMPORTANT]
> Azure SQL Database (alla distributions alternativ) har certifierats mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

Huvud funktionerna i hanterade instanser visas i följande tabell:

|Funktion | Beskrivning|
|---|---|
| SQL Server version/build | SQL Server databas motor (senaste stabila) |
| Hanterade automatiserade säkerhets kopieringar | Ja |
| Inbyggd instans och databas övervakning och mått | Ja |
| Automatisk uppdatering av program vara | Ja |
| De senaste databas motor funktionerna | Ja |
| Antal datafiler (rader) per databas | Flera |
| Antal loggfiler (logg) per databas | 1 |
| VNet – Azure Resource Manager distribution | Ja |
| VNet – klassisk distributions modell | Nej |
| Portal stöd | Ja|
| Inbyggd integrerings tjänst (SSIS) | No-SSIS är en del av [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Inbyggd Analysis Service (SSAS) | No-SSAS är separat [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Inbyggd repor ting service (SSRS) | Inga användnings Power BI eller SSRS-IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

Den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md) för hanterade instanser ger dig flexibilitet, kontroll, transparens och ett enkelt sätt att översätta lokala arbets belastnings krav till molnet. Med den här modellen kan du ändra beräkning, minne och lagring baserat på dina arbets belastnings behov. VCore-modellen är också berättigad till upp till 30 procent besparingar med [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

I vCore-modellen kan du välja mellan generationens maskin vara.

- **Gen4** Logiska processorer baseras på Intel E5-2673 v3 (Haswell) 2,4-GHz-processorer, anslutna SSD, fysiska kärnor, 7 GB RAM-minne per kärna och beräknings storlekar mellan 8 och 24 virtuella kärnor.
- **Gen5** Logiska processorer baseras på Intel E5-2673 v4 (Broadwell) 2,3-GHz-processorer, fast NVMe SSD, Hyper-threaded Logical Core och beräknings storlekar mellan 4 och 80 kärnor.

Hitta mer information om skillnaden mellan maskin varu generationer i [resurs gränser för hanterade instanser](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

## <a name="managed-instance-service-tiers"></a>Tjänst nivåer för hanterade instanser

Hanterad instans finns tillgänglig på två tjänst nivåer:

- **Generell användning**: Utformad för program med krav på standard prestanda och IO-latens.
- **Verksamhets kritiskt**: Utformad för program med krav på låg IO-latens och minimal påverkan på underliggande underhålls åtgärder på arbets belastningen.

Båda tjänst nivåerna garanterar 99,99% tillgänglighet och gör att du kan välja lagrings storlek och beräknings kapacitet separat. Mer information om arkitekturen för hög tillgänglighet för Azure SQL Database finns i [hög tillgänglighet och Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Tjänst nivå för generell användning

I följande lista beskrivs viktiga egenskaper för Generell användning tjänst nivån:

- Design för de flesta affärs program med typiska prestanda krav
- Azure Blob Storage med höga prestanda (8 TB)
- Inbyggd [hög tillgänglighet](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) baserat på tillförlitlig Azure Blob Storage och [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Mer information finns i [lagrings skikt i generell användnings nivå](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) och [metod tips för lagrings prestanda och överväganden för hanterade instanser (generell användning)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Hitta mer information om skillnaden mellan tjänst nivåer i [resurs gränser för hanterade instanser](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Affärskritisk tjänst nivå

Affärskritisk Service Tier är byggd för program med höga IO-krav. Den ger högsta möjliga återhämtning till problem med flera isolerade repliker.

I följande lista beskrivs viktiga egenskaper för Affärskritisk tjänst nivå:

- Utformad för företags program med högsta prestanda och HA krav
- Levereras med super-fast lokal SSD-lagring (upp till 1 TB på Gen4 och upp till 4 TB på Gen5)
- Inbyggd [hög tillgänglighet](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) baserat på tillgänglighets [grupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) och [Azure-Service Fabric](../service-fabric/service-fabric-overview.md)som alltid är tillgängliga.
- Inbyggd ytterligare skrivskyddad [databas replik](sql-database-read-scale-out.md) som kan användas för rapportering och andra skrivskyddade arbets belastningar
- [Minnes intern OLTP](sql-database-in-memory.md) som kan användas för arbets belastning med höga prestanda krav  

Hitta mer information om skillnaden mellan tjänst nivåer i [resurs gränser för hanterade instanser](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Hanterings åtgärder för hanterade instanser

Azure SQL Database innehåller hanterings åtgärder som du kan använda för att automatiskt distribuera nya hanterade instanser, uppdatera instans egenskaper och ta bort instanser när de inte längre behövs. Det här avsnittet innehåller information om hanterings åtgärder och deras normala varaktighet.

För att stödja [distributioner inom Azure Virtual Networks (virtuella nätverk)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) och tillhandahålla isolering och säkerhet för kunder förlitar sig den hanterade instansen på [virtuella kluster](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), som representerar en dedikerad uppsättning isolerade virtuella datorer som distribueras i kundens virtuella nätverks undernät. Varje distribution av hanterade instanser i ett tomt undernät resulterar i grunden i en ny version av det virtuella klustret.

Efterföljande åtgärder på distribuerade hanterade instanser kan också ha effekter på det underliggande virtuella klustret. Detta påverkar varaktigheten för hanterings åtgärder, eftersom distribution av ytterligare virtuella datorer levereras med en kostnad som måste beaktas när du planerar nya distributioner eller uppdateringar till befintliga hanterade instanser.

Alla hanterings åtgärder kan kategoriseras på följande sätt:

- Instans distribution (ny instans skapas). 
- Instans uppdatering (ändring av instans egenskaper, t. ex. virtuella kärnor, reserverad lagring osv.).
- Borttagning av instans.

Åtgärder i virtuella kluster tar vanligt vis längst. Varaktigheten för de virtuella klustren varierar – nedan visas de värden som du normalt förväntar dig, baserat på befintliga telemetridata för tjänsten:

- Skapa virtuellt kluster. Det här är ett synkront steg i instans hanterings åtgärder. **90% av åtgärderna har slutförts på 4 timmar**.
- Storleks ändring av virtuellt kluster (expandering eller krympning). Expansion är ett synkront steg, medan krympningen utförs asynkront (utan inverkan på instans hanterings åtgärdernas varaktighet). **90% av kluster utökningar slutförs på mindre än 2,5 timmar**.
- Borttagning av virtuellt kluster. Borttagning är ett asynkront steg, men det kan också [initieras manuellt](sql-database-managed-instance-delete-virtual-cluster.md) på ett tomt virtuellt kluster, vilket innebär att det körs synkront. **90% av de virtuella kluster borttagningarna slutförs om 1,5 timmar**.

Dessutom kan hantering av instanser också innehålla en av åtgärderna på värdbaserade databaser, vilket leder till längre varaktigheter:

- Bifogar databasfiler från Azure Storage. Det här är ett synkront steg, till exempel Compute (vCore) eller skalning av lagrings utrymme på Generell användning tjänst nivå. **90% av dessa åtgärder har slutförts på 5 minuter**.
- Dirigerar alltid om tillgänglighets gruppens dirigering. Det här är ett synkront steg, till exempel Compute (vCore) eller lagrings skalning på Affärskritisk tjänst nivå samt i ändra tjänst nivån från Generell användning till Affärskritisk (eller vice versa). Den här åtgärdens varaktighet är proportionerlig till den totala databas storleken samt den aktuella databas aktiviteten (antal aktiva transaktioner). Databas aktivitet vid uppdatering av en instans kan medföra betydande varianser för den totala varaktigheten. **90% av dessa åtgärder körs vid 220 GB/timme eller högre**.

I följande tabell sammanfattas åtgärder och typiska övergripande varaktigheter:

|Category  |Åtgärd  |Tids krävande segment  |Uppskattad varaktighet  |
|---------|---------|---------|---------|
|**Distribution** |Första instansen i ett tomt undernät|Skapa virtuellt kluster|90% av åtgärderna har slutförts på 4 timmar|
|Distribution |Första instansen av en annan maskin varu generation i ett undernät som inte är tomt (till exempel första generation 5-instansen i ett undernät med generation 4 instanser)|Skapa virtuellt kluster *|90% av åtgärderna har slutförts på 4 timmar|
|Distribution |Första instans skapandet av 4 virtuella kärnor, i ett tomt eller icke-tomt undernät|Skapa virtuellt kluster * *|90% av åtgärderna har slutförts på 4 timmar|
|Distribution |Efterföljande instans skapas i det icke-tomma under nätet (andra, tredje osv.)|Storleks ändring av virtuellt kluster|90% av åtgärderna har slutförts om 2,5 timmar|
|**Uppdatering** |Ändring av instans egenskap (administratörs lösen ord, AAD-inloggning, Azure Hybrid-förmån flagga)|Gäller inte|Upp till 1 minut|
|Uppdatera |Skalning av instans lagring upp/ned (Generell användning tjänst nivå)|-Storleks ändring av virtuellt kluster<br>-Bifoga databasfiler|90% av åtgärderna har slutförts om 2,5 timmar|
|Uppdatera |Skalning av instans lagring upp/ned (Affärskritisk tjänst nivå)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna slutförs om 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme)|
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Generell användning)|-Storleks ändring av virtuellt kluster<br>-Bifoga databasfiler|90% av åtgärderna har slutförts om 2,5 timmar|
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Affärskritisk)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna slutförs om 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme)|
|Uppdatera |Instans skala ned till 4 virtuella kärnor (Generell användning)|-Storleks ändring av virtuellt kluster (om det är färdigt för första gången kan det krävas att skapa virtuella kluster * *)<br>-Bifoga databasfiler|90% av åtgärderna har slutförts på 4 timmar 5 min * *|
|Uppdatera |Instans skala ned till 4 virtuella kärnor (Generell användning)|-Storleks ändring av virtuellt kluster (om det är färdigt för första gången kan det krävas att skapa virtuella kluster * *)<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts på 4 timmar + tid för att dirigera alla databaser (220 GB/timme)|
|Uppdatera |Instans tjänst nivå ändring (Generell användning till Affärskritisk och vice versa)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna slutförs om 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme)|
|**Redundanstestning**|Borttagning av instans|Logg säkerhets kopiering för alla databaser|90% åtgärder har slutförts på upp till 1 minut.<br>OBS! om den sista instansen i under nätet tas bort, kommer den här åtgärden att schemalägga borttagning av virtuellt kluster efter 12 timmar * * *|
|Redundanstestning|Borttagning av virtuellt kluster (som användarinitierad åtgärd)|Borttagning av virtuellt kluster|90% av åtgärderna har slutförts på upp till 1,5 timmar|

\*Det virtuella klustret skapas per maskin varu generation.

\*\*Distributions alternativet 4 virtuella kärnor släpptes i juni 2019 och kräver en ny virtuell kluster version. Om du har instanser i mål under nätet som alla skapades före 12 juni, distribueras ett nytt virtuellt kluster automatiskt till värd 4 vCore-instanser.

\*\*\*12 timmar är den aktuella konfigurationen men som kan ändras i framtiden, så ta inte ett hårt beroende av den. Om du behöver ta bort ett virtuellt kluster tidigare (för att frigöra under nätet till exempel), se [ta bort ett undernät när du har tagit bort en Azure SQL Database Hanterad instans](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Tillgänglighet för instanser under hantering

Hanterade instanser är inte tillgängliga för klient program under distributions-och borttagnings åtgärder.

Hanterade instanser är tillgängliga under uppdaterings åtgärder, men det finns ett kort stillestånd som orsakas av redundansväxlingen som inträffar i slutet av uppdateringar som vanligt vis varar upp till 10 sekunder.

> [!IMPORTANT]
> Varaktigheten för en redundansväxling kan variera avsevärt vid tids krävande transaktioner som inträffar på databaserna på grund av [långvarig återställnings tid](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Det rekommenderas därför inte att skala beräkning eller lagring av Azure SQL Database hanterade instanser eller ändra tjänst nivån samtidigt med tids krävande transaktioner (data import, data bearbetnings jobb, index återuppbyggnad osv.). Redundansväxling av databasen som ska utföras i slutet av åtgärden avbryter pågående transaktioner och resulterar i långvarig återställnings tid.

[Accelererad databas återställning](sql-database-accelerated-database-recovery.md) är för närvarande inte tillgänglig för Azure SQL Database hanterade instanser. När den här funktionen är aktive rad minskar också Variations tiden för redundans, även om tids krävande transaktioner körs.



## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

Alternativet för distribution av hanterade instanser kombinerar avancerade säkerhetsfunktioner som tillhandahålls av Azure-molnet och SQL Server databas motor.

### <a name="managed-instance-security-isolation"></a>Säkerhets isolering för hanterad instans

En hanterad instans ger ytterligare säkerhets isolering från andra klienter i Azure-molnet. Säkerhets isolering innehåller:

- [Inbyggd implementering av virtuella nätverk](sql-database-managed-instance-connectivity-architecture.md) och anslutning till din lokala miljö med hjälp av Azure Express Route eller VPN gateway.
- I en standard distribution exponeras SQL-slutpunkten bara via en privat IP-adress, vilket ger säker anslutning från privata Azure-eller hybrid nätverk.
- En-klient med dedikerad underliggande infrastruktur (beräkning, lagring).

I följande diagram beskrivs olika anslutnings alternativ för dina program:

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Om du vill veta mer om VNet-integrering och nätverks princip tillämpning på under näts nivån, se [VNet-arkitektur för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md) och [Anslut ditt program till en hanterad instans](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Placera flera hanterade instanser i samma undernät, där de tillåts av dina säkerhets krav, eftersom de ger dig ytterligare förmåner. Collocating-instanser i samma undernät fören klar avsevärt nätverks infrastrukturen och minskar instansen av etablerings tiden, eftersom varaktigheten för lång etablering är kopplad till kostnaden för att distribuera den första hanterade instansen i ett undernät.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database säkerhetsfunktioner

Azure SQL Database innehåller en uppsättning avancerade säkerhetsfunktioner som kan användas för att skydda dina data.

- [Granskning av hanterade instanser](sql-database-managed-instance-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg fil som placeras i ditt Azure Storage-konto. Granskning kan hjälpa till att upprätthålla regelefterlevnad, förstå databas aktivitet och få insikt i avvikelser och avvikelser som kan tyda på affärs problem eller misstänkta säkerhets överträdelser.
- Data kryptering i rörelse – en hanterad instans skyddar dina data genom att tillhandahålla kryptering för data i rörelse med hjälp av Transport Layer Security. Förutom Transport Layer Security ger distributions alternativet hanterad instans skydd av känsliga data i Flight, i vila och under bearbetning av frågor med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted är först i branschen med att erbjuda oslagbar datasäkerhet mot överträdelser, inklusive stöld av viktiga data. Med Always Encrypted lagras exempelvis kreditkorts nummer krypterade i databasen alltid, även under frågekörning, vilket tillåter dekryptering vid användnings punkten av behörig personal eller program som behöver behandla dessa data.
- [Avancerat skydd](sql-database-managed-instance-threat-detection.md) kompletterar [granskning](sql-database-managed-instance-auditing.md) genom att tillhandahålla ett extra lager av säkerhets information som är inbyggt i tjänsten och som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Du får varningar om misstänkta aktiviteter, potentiella sårbarheter och SQL-injektering, samt avvikande databas åtkomst mönster. Aviseringar för avancerat skydd kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/) och ger information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minimera hotet.  
- [Dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking) begränsar känslig data exponering genom att maskera den till icke-privilegierade användare. Dynamisk datamaskering bidrar till att förhindra obehörig åtkomst till känsliga data genom att göra det möjligt att ange hur mycket av känsliga data som ska visas med minimal påverkan på program nivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) gör att du kan styra åtkomsten till rader i en databas tabell baserat på egenskaperna för användaren som kör en fråga (t. ex. efter grupp medlemskap eller körnings kontext). Säkerheten på radnivå (RLS) förenklar design och kodning av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Du kan till exempel se till att anställda endast kan komma åt de data rader som är relevanta för deras avdelning eller begränsa en data åtkomst till enbart relevanta data.
- [Transparent data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) krypterar hanterade instans data filer, som kallas att kryptera data i vila. TDE utför I/O-kryptering i real tid och dekryptering av data-och loggfiler. Krypteringen använder en databas krypterings nyckel (DEK), som lagras i databasens start post för tillgänglighet under återställningen. Du kan skydda alla dina databaser i en hanterad instans med transparent data kryptering. TDE är SQL Server en beprövad krypterings-till-rest-teknik som krävs av många kompatibla standarder för att skydda mot stöld av lagrings medier.

Migrering av en krypterad databas till en hanterad instans stöds via Azure Database Migration Service (DMS) eller intern återställning. Om du planerar att migrera en krypterad databas med intern återställning är det ett obligatoriskt steg att migrera det befintliga TDE-certifikatet från SQL Server lokalt eller SQL Server i en virtuell dator till en hanterad instans. Mer information om alternativ för migrering finns i [SQL Server instans migrering till hanterad instans](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Distributions alternativet för hanterade instanser stöder traditionella inloggningar och inloggningar för SQL Server-databasmotorn som är integrerade med Azure Active Directory (AAD). Azure AD server-Huvudkonton (inloggningar) (**offentlig för hands**version) är Azure Cloud-versioner av lokala databas inloggningar som du använder i din lokala miljö. Med Azure AD-serverns huvud namn (inloggningar) kan du ange användare och grupper från din Azure Active Directory klient som sann huvud namn för instans – som kan utföra alla åtgärder på instans nivå, inklusive frågor över flera databaser inom samma hanterade session.

En ny syntax introduceras för att skapa Azure AD server-huvudobjekt (inloggningar) (**offentlig för hands version**), **från extern provider**. Mer information om syntaxen finns i <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning</a>och granska [en Azure Active Directory administratör för den hanterade](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) instansen av artikeln.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med alternativet för distribution av hanterade instanser kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication-configure.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning.

### <a name="authentication"></a>Authentication

Autentisering med hanterade instanser avser hur användare bekräftar sin identitet när de ansluter till databasen. SQL Database stöder två typer av autentisering:  

- **SQL-autentisering**:

  Den här autentiseringsmetoden använder ett användar namn och lösen ord.
- **Azure Active Directory autentisering**:

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Authorization

Auktorisering syftar på vad en användare kan göra inom en Azure SQL Database och styrs av ditt användar kontos databas roll medlemskap och behörigheter på objekt nivå. En hanterad instans har samma Authorization-funktioner som SQL Server 2017.

## <a name="database-migration"></a>Databasmigrering

Distributions alternativet för hanterade instanser riktar sig mot användar scenarier med en Mass databas migrering från lokala eller IaaS databas implementeringar. Hanterad instans har stöd för flera alternativ för migrering av databas:

### <a name="back-up-and-restore"></a>Säkerhetskopiera och återställa  

Migreringsprocessen utnyttjar SQL-säkerhetskopieringar till Azure Blob Storage. Säkerhets kopior som lagras i Azure Storage BLOB kan återställas direkt till en hanterad instans med hjälp av [kommandot T-SQL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)Restore.

- En snabb start som visar hur du återställer Wide World-importörer – standard säkerhets kopierings filen för databasen finns i [återställa en säkerhets kopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md). Den här snabb starten visar att du måste ladda upp en säkerhets kopia till Azures blogg lagring och skydda den med en SAS-nyckel (signatur för delad åtkomst).
- Information om återställning från URL finns i [intern återställning från URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Säkerhets kopieringar från en hanterad instans kan bara återställas till en annan hanterad instans. De kan inte återställas till en lokal SQL Server eller till en enskild databas/elastisk pool.

### <a name="data-migration-service"></a>Data migration service

Azure Database Migration Service är en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar med minimal stillestånds tid. Den här tjänsten effektiviserar de uppgifter som krävs för att flytta befintliga tredje parter och SQL Server databaser till Azure SQL Database (enstaka databaser, pooler databaser i elastiska pooler och instans databaser i en hanterad instans) och SQL Server i Azure VM. Se [hur du migrerar din lokala databas till en hanterad instans med DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>SQL-funktioner som stöds

Distributions alternativet för hanterade instanser syftar på att leverera nära till 100% Surface Area-kompatibilitet med lokala SQL Server kommer i stadier tills tjänsten är allmänt tillgänglig. För en funktion och jämförelse lista, se [SQL Database funktions jämförelse](sql-database-features.md)och för en lista över t-SQL-skillnader i hanterade instanser och SQL Server, se [hanterade instanser t-SQL-skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md).

Distributions alternativet för hanterade instanser stöder bakåtkompatibilitet till SQL 2008-databaser. Direkt migrering från SQL 2005-databas servrar stöds, kompatibilitetsnivå för migrerade SQL 2005-databaser uppdateras till SQL 2008.
  
Följande diagram visar kompatibiliteten för Surface Area i den hanterade instansen:  

![migreringsarkivet](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Viktiga skillnader mellan SQL Server lokalt och i en hanterad instans

Distributions alternativet för hanterade instanser är inte alltid uppdaterat i molnet, vilket innebär att vissa funktioner i lokala SQL Server kan vara antingen föråldrade, föråldrade eller ha alternativ. Det finns särskilda fall när verktyg måste identifiera att en viss funktion fungerar på ett något annorlunda sätt eller att tjänsten inte körs i en miljö som du inte har fullständig kontroll över:

- Hög tillgänglighet är inbyggd och förkonfigurerad med teknik som liknar Always [on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatiserade säkerhets kopieringar och tidpunkts återställning. Kunden kan initiera `copy-only` säkerhets kopieringar som inte stör automatisk säkerhets kopierings kedja.
- Den hanterade instansen tillåter inte att du anger fullständiga fysiska sökvägar så att alla motsvarande scenarier måste stödja olika: Restore DB stöder inte flytt, CREATE DB tillåter inte fysiska sökvägar, BULK INSERT endast fungerar med Azure-blobbar, osv.
- Den hanterade instansen stöder [Azure AD-autentisering](sql-database-aad-authentication.md) som moln alternativ till Windows-autentisering.
- Hanterad instans hanterar automatiskt XTP-filgrupp och filer för databaser som innehåller InMemory OLTP-objekt
- Den hanterade instansen stöder SQL Server Integration Services (SSIS) och kan vara värd för SSIS Catalog (SSISDB) som lagrar SSIS-paket, men de körs på en hanterad Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) finns i [Skapa Azure-SSIS IR i ADF ](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Information om hur du jämför SSIS-funktionerna i SQL Database finns i [jämför Azure SQL Database enstaka databaser/elastiska pooler och hanterade instanser](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Administrations funktioner för hanterade instanser

Med distributions alternativet för hanterade instanser kan system administratören ägna mindre tid åt administrativa uppgifter eftersom SQL Database tjänsten utför dem antingen åt dig eller avsevärt fören klar dessa aktiviteter. Till exempel [installation och uppdatering av OS/RDBMS](sql-database-high-availability.md), [dynamisk storleks ändring och konfiguration av instanser](sql-database-single-database-scale.md), [säkerhets kopieringar](sql-database-automated-backups.md), [databasreplikering](replication-with-sql-database-managed-instance.md) (inklusive system databaser), [konfiguration med hög tillgänglighet](sql-database-high-availability.md)och konfiguration av data strömmar för hälso-och [prestanda övervakning](../azure-monitor/insights/azure-sql.md) .

> [!IMPORTANT]
> En lista över funktioner som stöds delvis och som inte stöds finns i [SQL Database funktioner](sql-database-features.md). En lista över T-SQL-skillnader i hanterade instanser och SQL Server finns i [hanterade instanser t-SQL-skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Så här identifierar du en hanterad instans program mässigt

I följande tabell visas flera egenskaper, som är tillgängliga via Transact SQL, som du kan använda för att identifiera att programmet fungerar med en hanterad instans och hämta viktiga egenskaper.

|Egenskap|Value|Kommentar|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Värdet är samma som i SQL Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Värdet är samma som i SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Det här värdet identifierar en hanterad instans unikt.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Komplett instans DNS-namn i följande format:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, där `<instanceName>` är namn som tillhandahålls av kunden, medan `<dnsPrefix>` är automatiskt genererade del av namnet, vilket ger global unikhet för DNS-namn (”wcus17662feb9ce98”, till exempel)|Exempel: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](sql-database-managed-instance-get-started.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](sql-database-features.md).
- Mer information om VNet-konfiguration finns i [konfiguration av VNet](sql-database-managed-instance-connectivity-architecture.md)-instansen VNet.
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- En själv studie kurs om hur du använder Azure Database Migration Service (DMS) för migrering finns i [migrering av hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av hanterade instanser av databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL Database att använda Azure SQL-analys](../azure-monitor/insights/azure-sql.md).
- Pris information finns i [priser för SQL Database Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
