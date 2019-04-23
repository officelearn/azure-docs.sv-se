---
title: Azure SQL Database avancerad översikt över data säkerhet | Microsoft Docs
description: Det här avsnittet beskrivs en avancerad datasäkerhet i Azure SQL Database och hur det fungerar och hur den skiljer sig från en enda eller grupperade databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 46c6972e20df69da236c151516d7d889f9db6084
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002756"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>Använda avancerad datasäkerhet med virtuella nätverk och nästan 100% kompatibilitet SQL-databas

Managed instance är ett nytt distributionsalternativ i Azure SQL Database, vilket ger nästan 100% kompatibilitet med den senaste SQL Server lokalt (Enterprise Edition) databasmotorn, ger ett ursprungligt [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md) implementeringen som åtgärdar vanliga säkerhetsproblem och en [affärsmodell](https://azure.microsoft.com/pricing/details/sql-database/) fördelaktig för en lokal SQL Server-kunder. Den hanterade instans distributionsmodellen kan befintliga SQL Server-kunder att flytta över sina lokala program till molnet med minimala ändringar för programmet och databasen. På samma gång, distributionsalternativ för hanterad instans bevarar alla PaaS-funktioner (automatiska uppdateringar för uppdatering och version, [automatiska säkerhetskopior](sql-database-automated-backups.md), [hög tillgänglighet](sql-database-high-availability.md) ), som drastiskt minskar hanteringskostnader och TCO.

> [!IMPORTANT]
> En lista över regioner som distributionsalternativ för hanterad instans är tillgänglig för tillfället finns i [regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions).

Följande diagram illustrerar viktiga funktioner i hanterade instanser:

![viktiga funktioner](./media/sql-database-managed-instance/key-features.png)

Den hanterade instans distributionsmodellen är utformat för kunder som vill migrera ett stort antal appar från lokala eller IaaS, lokal bygger eller ISV som angetts för miljön till fullständigt hanterad PaaS-molnmiljö med låg migrering arbete som möjligt. Med hjälp av den helt automatiserade [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) i Azure, kunderna kan flytta över sina lokala SQL Server till en hanterad instans som är kompatibel med SQL Server lokalt och över kundinstanser med inbyggt stöd för virtuellt nätverk.  Med Software Assurance kan du byta sina befintliga licenser för rabatterade priser på en hanterad instans med hjälp av den [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  En hanterad instans är den bästa mål för migrering i molnet för SQL Server-instanser som kräver hög säkerhet och en omfattande programmerbarheten för angrepp.

Hanterad instans distribution alternativet målen levererar nära 100% ytan kompatibilitet med den senaste lokala SQL Server-versionen via en mellanlagrad version-plan.

Att välja mellan Azure SQL Database-distributionsalternativen: enkel databas, databas, och hanterad instans och SQL Server i virtuell dator, se [hur du väljer rätt version av SQL Server i Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Viktiga funktioner och funktioner

Hanterade instans kombinerar de bästa funktionerna som finns både i Azure SQL Database och SQL Server Database Engine.

> [!IMPORTANT]
> En hanterad instans körs med alla funktioner i den senaste versionen av SQL Server, inklusive online åtgärder, plan för automatisk korrigeringar och andra enterprise prestandaförbättringar. En jämförelse av funktionerna förklaras i [funktionsjämförelse: Azure SQL Database jämfört med SQLServer](sql-database-features.md).

| **PaaS-fördelar** | **Verksamhetskontinuitet** |
| --- | --- |
|Ingen maskinvara att köpa och hantering <br>Ingen hantering av fasta kostnader för att hantera underliggande infrastruktur <br>Snabb etablering och skalning av tjänst <br>Automatisk uppdatering och version uppgradering <br>Integrering med andra data PaaS-tjänster |99,99% drifttid  <br>Inbyggda [hög tillgänglighet](sql-database-high-availability.md) <br>Data som skyddas med [automatiska säkerhetskopior](sql-database-automated-backups.md) <br>Kunden kan konfigureras kvarhållningsperiod <br>Användarinitierad [säkerhetskopior](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Tidpunkt för återställning till tidpunkt databasen](sql-database-recovery-using-backups.md#point-in-time-restore) funktion |
|**Säkerhet och efterlevnad** | **Hantering**|
|Isolerad miljö ([VNet-integrering](sql-database-managed-instance-connectivity-architecture.md), enskild klient-tjänsten, dedikerad beräkning och lagring) <br>[Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-autentisering](sql-database-aad-authentication.md), enkel inloggning för support <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-server-huvudkonton (inloggningar)</a> (**förhandsversion**) <br>Följer efterlevnadsstandarder samma som Azure SQL-databas <br>[SQL-granskning](sql-database-managed-instance-auditing.md) <br>[Identifiering av hot](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager-API för att automatisera service etablering och skalning <br>Azure-portalen funktioner för manuell tjänsten etablering och skalning <br>Data Migration Service

> [!IMPORTANT]
> Azure SQL Database (alla distributionsalternativ), har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i den [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan med SQL Database-kompatibilitetscertifieringar.

I följande tabell visas de viktigaste funktionerna i hanterade instanser:

|Funktion | Beskrivning|
|---|---|
| SQL Server-version / skapa | SQL Server Database Engine (senaste stabil) |
| Hanterade automatiska säkerhetskopieringar | Ja |
| Inbyggd instans och databasövervakning och mått | Ja |
| Automatisk uppdatering av programvaror | Ja |
| De senaste funktionerna i Database Engine | Ja |
| Antalet datafiler (rader) per databasen | Flera |
| Antal loggfiler (loggning) per databas | 1 |
| Virtuellt nätverk – Azure Resource Manager-distribution | Ja |
| Virtuellt nätverk – klassiska distributionsmodellen | Nej |
| Portal-support | Ja|
| Inbyggd Integration-tjänsten (SSIS) | Nej, SSIS är en del av [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Inbyggda Analysis Service (SSAS) | Nej, SSAS är separat [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Inbyggda rapporteringstjänsten (SSRS) | Nej, Använd Power BI eller SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuell kärna-baserad inköpsmodell

Den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) för hanterade instanser får du flexibilitet, kontroll, transparens och ett enkelt sätt att överföra lokala arbetsbelastningskrav till molnet. Den här modellen kan du ändra beräkning, minne och lagring utifrån dina arbetsbelastningsbehov. VCore-modellen är också berättigade till upp till 30 procent med den [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

Du kan välja mellan av maskinvara i vCore-modellen.

- **Gen4** logiska CPU baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, SSD, enheter anslutna fysiska kärnor, 7 GB RAM-minne per kärna och storlekar på mellan 8 och 24 virtuella kärnor.
- **Gen5** logiska CPU baseras på Intel E5-2673 v4-processorn (Broadwell) 2.3-GHz-processorer, snabb NVMe SSD, hyper-threaded logiska kärna, och beräkna storlekar mellan 8 och 80 kärnor.

Mer information om skillnaden mellan maskinvarugenerationer i [hanterad instans resursbegränsningar](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Hanterad instans-tjänstnivåer

Hanterad instans finns i två tjänstnivåer:

- **Generella**: Avsedd för tillämpningar med vanliga prestanda och i/o-svarstidskrav.
- **Affärskritisk**: Avsedd för tillämpningar med låg i/o-svarstidskrav och minimal påverkan på underliggande underhållsåtgärder på arbetsbelastningen.

Båda versionerna garanterar 99,99% tillgänglighet och gör att du kan välja lagringsstorlek oberoende och beräkningskapacitet. Mer information om arkitektur med hög tillgänglighet i Azure SQL Database finns i [hög tillgänglighet och Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Tjänstnivå för generell användning

I följande lista beskrivs nyckelegenskap för tjänstnivån generell användning:

- Design för flesta affärsprogram med vanliga prestandakrav
- Azure Blob storage med höga prestanda (8 TB)
- Inbyggda [hög tillgänglighet](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) baserat på den pålitliga Azure Blob storage och [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Mer information finns i [storage layer generellt syfte nivån](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) och [bästa praxis för prestanda för lagring och överväganden för hanterade instanser (Allmänt)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Mer information om skillnaden mellan tjänstnivåerna i [hanterad instans resursbegränsningar](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Kritiska-affärsnivå

Kritiska-affärsnivå har utformats för program med höga i/o-krav. Det erbjuder högsta återhämtning till fel med flera isolerade repliker.

I följande lista beskrivs de främsta egenskaperna för nivån affärskritisk service:

- Utformad för affärsprogram med högsta prestanda och hög tillgänglighet krav
- Medföljer Supersnabb lokal SSD-lagring (upp till 1 TB på Gen4 och upp till 4 TB på Gen5)
- Inbyggda [hög tillgänglighet](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) utifrån [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) och [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Ytterligare inbyggda [skrivskyddad databasrepliken](sql-database-read-scale-out.md) som kan användas för rapportering och andra skrivskyddade arbetsbelastningar
- [In-Memory OLTP](sql-database-in-memory.md) som kan användas för arbetsbelastningar med höga prestanda krav  

Mer information om skillnaden mellan tjänstnivåerna i [hanterad instans resursbegränsningar](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

Distributionsalternativ för hanterad instans kombinerar avancerade säkerhetsfunktioner som tillhandahålls av Azure-molnet och SQL Server Database Engine.

### <a name="managed-instance-security-isolation"></a>Hanterad instans säkerhetsisolering

En hanterad instans ger ytterligare säkerhetsisolering från andra klienter i Azure-molnet. Säkerhetsisolering innehåller:

- [Intern implementering](sql-database-managed-instance-connectivity-architecture.md) och vara ansluten till din lokala miljö med hjälp av Azure Express Route eller VPN-Gateway.
- I en standarddistribution exponeras SQL slutpunkten endast via en privat IP-adress som tillåter säker anslutning från privata Azure eller hybrid-nätverk.
- Enda klient med dedikerad underliggande infrastruktur (beräkning, lagring).

Följande diagram illustrerar olika anslutningsalternativ för dina program:

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Läs mer om VNet-integrering och nätverk genomförande av principer på undernätverksnivån i i [VNet-arkitekturen för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md) och [Anslut ditt program till en hanterad instans](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Placera flera hanterade instanser i samma undernät, var som tillåts av din säkerhetskrav, eftersom som ger dig ytterligare förmåner. Collocating instanser i samma undernät kan avsevärt förenkla nätverk infrastrukturunderhåll och minska instans etableringstid eftersom länge etablering varaktighet är associerat med kostnaden för att distribuera den första hantera instansen i ett undernät.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database-säkerhetsfunktioner

Azure SQL Database innehåller en uppsättning avancerade säkerhetsfunktioner som kan användas för att skydda dina data.

- [Hanterad instans granskning](sql-database-managed-instance-auditing.md) spårar databashändelser och skriver dem till en granskningsloggfil placeras i ditt Azure storage-konto. Granskning kan upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Datakryptering i rörelse – en hanterad instans skyddar dina data genom att tillhandahålla kryptering för data i rörelse med hjälp av Transport Layer Security. Förutom transport layer security distributionsalternativ för hanterad instans erbjuder skydd av känsliga data i rörelse, i vila och under frågebearbetning med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted är först i branschen med att erbjuda oslagbar datasäkerhet mot överträdelser, inklusive stöld av viktiga data. Till exempel med Always Encrypted lagras kreditkortsnummer krypterade i databasen alltid, även under frågebearbetning, vilket möjliggör dekryptering vid användning av behörig personal eller program som behöver bearbeta datan.
- [Hotidentifiering](sql-database-managed-instance-threat-detection.md) kompletterar [granskning](sql-database-managed-instance-auditing.md) genom att tillhandahålla ett ytterligare säkerhetslager intelligens är inbyggt i tjänsten som identifierar onormala och potentiellt skadliga försöker komma åt eller utnyttja databaser. Du aviseras om misstänkta aktiviteter, potentiella svagheter, och SQL-inmatning attacker och avvikande mönster i databasåtkomst. Hotidentifieringsaviseringar kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/) och ger information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot.  
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking) begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) gör det möjligt att styra åtkomst till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (till exempel av grupmedlemskap eller körning). Säkerheten på radnivå (RLS) förenklar design och kodning av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Till exempel så att anställda har åtkomst till de datarader som är relevanta för deras avdelning eller att begränsa en data-åtkomsten till endast de relevanta data.
- [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) krypterar hanterad instans-datafiler, kallas även kryptera vilande data. TDE utför i realtid i/o-kryptering och dekryptering av de data och loggfiler. Krypteringen använder en databaskrypteringsnyckel (DEK), som lagras i boot databaspost för tillgänglighet under återställningen. Du kan skydda alla dina databaser i en hanterad instans med transparent datakryptering. TDE är SQL Servers beprövade kryptering i vila-teknik som krävs av många efterlevnadsstandarder för att skydda mot stöld av lagringsmedier.

Migrering av en krypterad databas till en hanterad instans stöds via Azure Database Migration Service (DMS) eller intern återställning. Om du planerar att migrera en krypterad databas med inbyggd återställning är ett obligatoriskt steg i migreringen av det befintliga TDE-certifikatet från den lokala SQL Server eller SQL Server på en virtuell dator till en hanterad instans. Mer information om migreringsalternativ finns i [migrering av SQL Server-instans till managed instance](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Distributionsalternativ för hanterad instans har stöd för traditionella SQL server Database engine-inloggningar och inloggningar som integreras med Azure Active Directory (AAD). Azure AD-server-huvudkonton (inloggningar) (**förhandsversion**) är version av en lokal databasinloggningar som du använder i din lokala miljö. Azure AD-server-huvudkonton (inloggningar) kan du ange användare och grupper från Azure Active Directory-klient som true instans omfattar huvudnamn, kan utföra alla åtgärder som på instansnivå, inklusive frågor mellan databaser inom samma hanterad instans.

Introduceras en ny syntax för att skapa Azure AD server-huvudkonton (inloggningar) (**förhandsversion**), **från extern PROVIDER**. Mer information om syntaxen finns i <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>, och granska de [etablera en Azure Active Directory-administratör för din hanterade instans](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) artikeln.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Distributionsalternativ för hanterad instans kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication-configure.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning.

### <a name="authentication"></a>Authentication

Hanterad instans autentisering refererar till hur användarna bekräfta sin identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:  

- **SQL-autentisering**:

  Den här autentiseringsmetoden använder ett användarnamn och lösenord.
- **Azure Active Directory Authentication**:

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory och har stöd för hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Auktorisering

Auktorisering hänvisar till vad en användare kan göra inom en Azure SQL Database och styrs av ditt användarkontos databas rollmedlemskap och behörigheter på objektnivå. En hanterad instans har samma funktioner för auktorisering som SQL Server 2017.

## <a name="database-migration"></a>Databasmigrering

De hanterade instansen alternativet mål användaren distributionsscenarierna med drivrutiner för masslagring Databasmigrering från lokala eller IaaS databas implementeringar. Hanterad instans stöder flera alternativ för databas-migrering:

### <a name="back-up-and-restore"></a>Säkerhetskopiera och återställa  

Förhållningssätt till Databasmigrering utnyttjar SQL-säkerhetskopior till Azure Blob storage. Säkerhetskopior som lagras i Azure storage blob kan återställas direkt till en hanterad instans med hjälp av den [T-SQL RESTORE-kommandot](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- En Snabbstart som visar hur du återställer Wide World Importers – Standard databassäkerhetskopia, se [återställa en säkerhetskopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md). Denna Snabbstart visar du behöver ladda upp en säkerhetskopia till Azure BLOB-lagring och skydda den med en signatur för delad åtkomstnyckel.
- Information om återställning från URL: en finns i [interna ÅTERSTÄLLA från URL: en](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Säkerhetskopior från en hanterad instans kan bara återställas till en annan hanterad instans. De kan inte återställas till en lokal SQL Server eller till en enda databas/elastisk pool.

### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service är en fullständigt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. Den här tjänsten effektiviserar uppgifter som krävs för att flytta befintliga från tredje part och SQL Server-databaser till Azure SQL Database (enskilda databaser, databaser i pooler i elastiska pooler och instansdatabaser i en hanterad instans) och SQL Server i Azure VM. Se [hur du migrerar din lokala databas till managed instance med DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>SQL-funktioner som stöds

Distributionsalternativ för hanterad instans syftar till att leverera nära 100% ytan kompatibilitet med en lokal SQL Server kommer i steg tills tjänsten allmänt tillgängliga. För en funktioner och jämförelse lista, se [SQL Database funktionsjämförelse](sql-database-features.md), och en lista över T-SQL skillnader i hanterade instanser jämfört med SQL Server finns i [managed instance T-SQL skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md).

Distributionsalternativ för hanterad instans stöder bakåtkompatibilitet till SQL 2008-databaser. Direct migrering från SQL 2005 databasservrar stöds, kompatibilitetsnivå för migrerade SQL 2005-databaserna har uppdaterats till SQL 2008.
  
Följande diagram illustrerar ytan kompatibilitet i hanterade instansen:  

![Migrering](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Viktiga skillnader mellan SQL Server lokalt och i en hanterad instans

Hanterad instans distribution alternativet fördelarna från att alltid upp-hittills i molnet, vilket innebär att vissa funktioner i en lokal SQL Server kan vara antingen föråldrade dragits tillbaka eller har alternativ. Det finns vissa fall när verktyg måste du identifiera att en viss funktion fungerar på ett något annorlunda sätt eller att tjänsten inte körs i en miljö som du inte helt styra:

- Hög tillgänglighet är inbyggd i och redan har konfigurerats med hjälp av teknik som liknar [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatiserade säkerhetskopieringar och tidpunkt för återställning till tidpunkt. Kunden kan initiera `copy-only` säkerhetskopieringar som inte stör automatisk loggsäkerhetskopieringssekvensen.
- Hanterad instans tillåter inte att ange fullständig fysiska sökvägar så att alla motsvarande scenarier behöver stödjas på olika sätt: ÅTERSTÄLLA DB stöder inte WITH MOVE, skapa DB tillåter inte fysiska sökvägar, BULK INSERT fungerar med Azure Blobs endast osv.
- Hanterad instans stöder [Azure AD-autentisering](sql-database-aad-authentication.md) som molnet alternativ till Windows-autentisering.
- Hanterad instans hanterar automatiskt XTP-filgruppen och filer för databaser som innehåller In-Memory OLTP-objekt
- Hanterad instans har stöd för SQL Server Integration Services (SSIS) och kan värden SSIS-katalogen (SSISDB) som lagrar SSIS-paket, men de körs på en hanterad Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF), se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Om du vill jämföra SSIS-funktioner i SQL-databas, se [jämför Azure SQL Database enkel databaser/elastiska pooler och hanterade instansen](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Hanterad instans-funktioner för administration

Distributionsalternativ för hanterad instans kan systemadministratören att ägna mindre tid åt administrativa uppgifter, eftersom SQL Database-tjänsten utför dem åt dig eller förenklar dessa uppgifter. Till exempel [OS / RDBMS installation, uppdatering och](sql-database-high-availability.md), [dynamisk instans storleksändringar och konfiguration](sql-database-single-database-scale.md), [säkerhetskopior](sql-database-automated-backups.md), [databasreplikering](replication-with-sql-database-managed-instance.md)(inklusive systemdatabaser), [konfiguration för hög tillgänglighet](sql-database-high-availability.md), och konfiguration av hälsotillstånd och [prestandaövervakning](../azure-monitor/insights/azure-sql.md) dataströmmar.

> [!IMPORTANT]
> En lista över funktioner stöds, delvis stöds och som inte stöds finns i [SQL Database-funktioner](sql-database-features.md). En lista över T-SQL skillnader i hanterade instanser jämfört med SQL Server finns i [managed instance T-SQL skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Hur du programmässigt identifierar en hanterad instans

I följande tabell visar flera egenskaper som är tillgängliga via Transact-SQL, att du kan använda för att identifiera att programmet fungerar med hanterad instans och hämta viktiga egenskaper.

|Egenskap|Value|Kommentar|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Det här värdet är samma som i SQL-databas.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Det här värdet är samma som i SQL-databas.|
|`SERVERPROPERTY('EngineEdition')`|8|Det här värdet identifierar en hanterad instans.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Komplett instans DNS-namn i följande format:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, där `<instanceName>` är namn som tillhandahålls av kunden, medan `<dnsPrefix>` är automatiskt genererade del av namnet, vilket ger global unikhet för DNS-namn (”wcus17662feb9ce98”, till exempel)|Exempel: min-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Nästa steg

- Läs hur du skapar din första hanterad instans i [Snabbstartsguide](sql-database-managed-instance-get-started.md).
- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
- Mer information om konfiguration av virtuellt nätverk finns i [hanterad instanskonfiguration av virtuellt nätverk](sql-database-managed-instance-connectivity-architecture.md).
- En Snabbstart som skapar en hanterad instans och återställer en databas från en säkerhetskopia, se [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- En självstudiekurs med hjälp av Azure Database Migration Service (DMS) för migrering finns i [hanterad instans-migrering med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Information om avancerad övervakning av databasprestanda för hanterad instans med inbyggd felsökningsintelligens finns i artikeln om att [övervaka Azure SQL Database med hjälp av Azure SQL-analys](../azure-monitor/insights/azure-sql.md)
- Information om priser finns i [SQL Database managed instance priser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
