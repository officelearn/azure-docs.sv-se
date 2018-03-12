---
title: "Azure SQL Database hanteras instans översikt | Microsoft Docs"
description: "Det här avsnittet beskriver en Azure SQL Database hanteras och förklarar hur det fungerar och hur den skiljer sig från en enskild databas i Azure SQL Database."
services: sql-database
documentationcenter: na
author: bonova
ms.reviewer: carlrab
manager: cguyer
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: dc3c93a1a13f3e10f9159d26411d6337c0269722
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="what-is-a-managed-instance-preview"></a>Vad är en hanterad-instans (förhandsgranskning)?

Azure SQL-hanterade databasinstans (förhandsversion) är en ny funktion i Azure SQL Database, tillhandahåller nästan 100% kompatibilitet med SQL Server on-premises genom att tillhandahålla ett ursprungligt [virtuella nätverk (VNet)](../virtual-network/virtual-networks-overview.md) implementering som adresser vanliga säkerhetsfrågor, och en [affärsmodell](https://azure.microsoft.com/pricing/details/sql-database/) fördelaktig för lokala SQL Server-kunder. Hanterade instansen tillåter befintliga SQL Server-kunder att lyfta och flytta sina lokala program till molnet med minimala ändringar för programmet och databasen. På samma gång bevarar hanteras instans alla PaaS funktioner (automatiska uppdateringar för uppdatering och version, säkerhetskopiering, hög tillgänglighet), som minskar drastiskt hanteringskostnader och TCO.
 
Följande diagram ger en översikt över viktiga funktioner i den hanterade instans:

![viktiga funktioner](./media/sql-database-managed-instance/key-features.png)

Hanterade instans envisioned som önskade plattform för följande scenarier: 

- SQLServer lokalt / IaaS-kunder som planerar för att migrera sina program till en helt hanterad tjänst med minimal utforma ändringar.
- ISV: er att förlita sig på SQL-databaser som du vill aktivera sina kunder att migrera till molnet och därmed få betydande konkurrensfördel eller globala marknaden nå. 

Efter allmän tillgänglighet hanteras instans syftet är att leverera nära ytan 100% kompatibilitet med den senaste lokala SQL Server-versionen via en mellanlagrad version plan. 

Följande tabell beskrivs nyckeln skillnader och envisioned Användningsscenarier mellan SQL IaaS, Azure SQL Database och hanteras instans:

| | Användningsscenariot | 
| --- | --- | 
|Managed Instance |För kunder som planerar för att migrera ett stort antal appar från lokala eller IaaS, automatisk inbyggd eller ISV finns med som ansträngning låg migrering som möjligt, föreslå hanteras instans. Med hjälp av helt automatiserad [Data migrering Service (DMS)](/sql/dma/dma-overview) i Azure, kan kunder lyfta och flytta sina lokala SQL Server till en hanterad instans som är kompatibel med SQL Server lokalt och fullständig isolering av kunden instanser med inbyggt stöd för virtuella nätverk.  Med Software Assurance, kan du byta sina befintliga licenser för rabatterade priser på en SQL-hanterade databasinstans med hjälp av den [Azure Hybrid Använd förmån för SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  SQL-hanterade databasinstans är den bästa mål för migrering i molnet för SQL Server-instanser som kräver hög säkerhet och en omfattande programmering yta. |
|Azure SQL Database |För kunder att utveckla nya SaaS flera program eller avsiktligt omvandla sina befintliga lokala appar i en multitenant SaaS-app föreslå elastiska pooler. Fördelarna med den här modellen är: <br><ul><li>Konvertering av modellen verksamhet från sälja licenser för att sälja prenumerationer (för ISV: er)</li></ul><ul><li>Enkel och punkt bevis klientisolering</li></ul><ul><li>En förenklad databasen till Central programmeringsmodell</li></ul><ul><li>Möjlighet att skala ut utan att en hård gräns</li></ul>För kunder att utveckla nya appar än SaaS flera innehavare, vars arbetsbelastning är stabila och förutsägbara, föreslå enskilda databaser. Fördelarna med den här modellen är:<ul><li>En förenklad databasen till Central programmeringsmodell</li></ul>  <ul><li>Förutsägbar prestanda för varje databas</li></ul>|
|SQL-IaaS |För kunder som behöver anpassa operativsystemet eller den databasserver, samt kunder som har specifika krav som kör appar från tredje part vid sida med SQL Server (i samma VM), föreslå SQL virtuella datorer / IaaS som den bästa lösningen|
|||

![placering](./media/sql-database-managed-instance/positioning.png)

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Så här identifierar du en instans som hanteras via programmering

I följande tabell visas flera egenskaper, tillgängligt via Transact SQL som du kan använda för att identifiera att programmet fungerar med hanterade instans och hämta viktiga egenskaper.

|Egenskap|Värde|Kommentar|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) Microsoft Corporation som 2018.|Det här värdet är densamma som SQL-databasen.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Det här värdet är densamma som SQL-databasen.|
|`SERVERPROPERTY('EngineEdition')`|8|Det här värdet identifierar hanteras instans.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Fullständig DNS-instansnamn i följande format:<instanceName>.<dnsPrefix>. Database.Windows.NET, där <instanceName> är namn som tillhandahålls av kunden, medan <dnsPrefix> tillhör automatiskt genererade namnet garanterar globala unikhet för DNS-namn (”wcus17662feb9ce98”, till exempel)|Exempel: min-hanterad-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Viktiga funktioner och funktioner på en instans av hanterade 

| **PaaS fördelar** | **Verksamhetskontinuitet** |
| --- | --- |
|Ingen maskinvara inköp och hantering <br>Ingen hantering omkostnader för att hantera underliggande infrastruktur <br>Snabb etablering och skalning för tjänsten <br>Automatisk uppdatering och version uppgradering <br>Integrering med andra tjänster för PaaS-data |99,99% drifttid SERVICENIVÅAVTAL  <br>Inbyggd hög tillgänglighet <br>Data som skyddas med automatisk säkerhetskopiering <br>Kunden kan konfigureras säkerhetskopiering kvarhållningsperiod (fast till 7 dagar i förhandsversion) <br>Användarinitierad säkerhetskopieringar <br>Punkt i tiden databasen återställa kapaciteten |
|**Säkerhet och efterlevnad** | **Hantering**|
|Isolerad miljö (VNet integration, stöd för en innehavare service, dedikerad beräkning och lagring <br>Kryptering av data under överföring <br>Azure AD-autentisering, stöd för enkel inloggning <br>Följer efterlevnadsstandarder samma som Azure SQL-databas <br>SQL-granskning <br>Hotidentifiering |Azure Resource Manager API för att automatisera service etablering och skalning <br>Azure portal funktioner för manuell tjänst för etablering och skalning <br>Migrering datatjänst 

![Enkel inloggning](./media/sql-database-managed-instance/sso.png) 

## <a name="managed-instance-service-tier"></a>Hanterade instans tjänstnivå

Hanterade instansen är är tillgängliga i en enda tjänstnivå - generella - som är avsedd för program med vanliga tillgänglighet och gemensamma krav för i/o-svarstid.

I följande lista beskrivs viktiga egenskap för tjänstnivån generella: 

- Design för flesta affärsprogram typiska prestanda och hög tillgänglighet krav 
- Högpresterande Azure Premium-lagring (8 TB) 
- 100 databaser / instans 

I det här skiktet du oberoende Välj lagring och beräkna kapacitet. 

I följande diagram illustreras active beräknings- och redundanta noder i den här tjänstnivån.
 
![Tjänstnivån för allmänna ändamål](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

Följande beskriver viktiga funktioner i tjänstnivån generella:

|Funktion | Beskrivning|
|---|---|
| Antal vCores * | 8, 16, 24|
| SQL Server-version / skapa | SQL Server senaste (tillgänglig) |
| Min lagringsstorlek | 32 GB |
| Maxstorlek för lagring | 8 TB |
| Förväntade lagring IOPS | 500-7500 IOPS per fil (beroende på datafilen). Se [Premium-lagring](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Antalet datafiler (rader) per databasen | Flera | 
| Antal loggfiler (loggning) per databas | 1 | 
| Hanterade automatisk säkerhetskopiering | Ja |
| HA | Baserat på Fjärrlagring och [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Inbyggda instans och databas övervakning och mått | Ja |
| Automatisk programuppdatering | Ja |
| VNet - Azure Resource Manager-distribution | Ja |
| VNet - klassiska distributionsmodellen | Nej |
| Stöd för Portal | Ja|
|||

\* En virtuell kärna representerar logiska Processorn erbjuds med möjlighet att välja mellan generationer av maskinvara. Gen 4 logiska processorer är baserade på Intel E5-2673 v3 (Haswell) 2,4 GHz processorer och Gen 5 logiska processorer är baserade på Intel E5-2673 v4 (Broadwell) 2.3 GHz-processorer.  

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad 

### <a name="managed-instance-security-isolation"></a>Hanterade instans säkerhetsisolering 

Hanterade instans isolera ytterligare säkerhet från andra klienter i Azure-molnet. Säkerhetsisolering innehåller: 

- Implementering av interna virtuella nätverk och anslutning till din lokala miljö med hjälp av Azure Express Route eller VPN-Gateway 
- SQL-slutpunkten exponeras endast via en privat IP-adress som tillåter säker anslutning från privata Azure eller hybrid-nätverk
- Stöd för en innehavare med dedikerad underliggande infrastruktur (beräkning, lagring)

Följande diagram ger en översikt över isolering design: 

![Hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet 

Hanterade instans [granskning](sql-database-auditing.md) spårar databashändelser och skriver dem till en granskningslogg logga i Azure storage-konto-databas. Granskning kan det hjälpa att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser. 

### <a name="data-encryption-in-motion"></a>Datakryptering i rörelse 

Hanterade instans skyddar dina data genom att tillhandahålla kryptering för data i rörelse med Transport Layer Security.

Utöver transport layer security hanteras SQL-databasinstans erbjuder skydd för känsliga data som rör sig i vila och under frågebearbetning med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted är först i branschen med att erbjuda oslagbar datasäkerhet mot överträdelser, inklusive stöld av viktiga data. Till exempel med Always Encrypted lagras kreditkortsnummer krypterade i databasen alltid, även under frågan bearbetning, att tillåta dekryptering vid användning av behöriga personer eller program som behöver bearbeta dessa data. 

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning 

SQL-databas [dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking) begränsar exponering av känsliga data genom att maskera till obehöriga användare. Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att du kan ange hur mycket av känsliga data för att avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad. 

### <a name="row-level-security"></a>Säkerhet på radnivå 

[Radnivå säkerhet](/sql/relational-databases/security/row-level-security) låter dig att styra åtkomst till rader i en databastabell baserat på egenskaperna för den användare som kör en fråga (t.ex med gruppen medlemskap eller köra context). Säkerheten på radnivå (RLS) förenklar design och kodning av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Till exempel så att anställda har åtkomst till de datarader som är relevanta för deras avdelning eller begränsa en dataåtkomst till relevanta data. 

### <a name="threat-detection"></a>Hotidentifiering 

Azure SQL Database [Hotidentifiering](sql-database-threat-detection.md) kompletterar granskning genom att tillhandahålla ett extra lager av säkerhet för tillgångsinformation är inbyggda i tjänsten som identifierar onormal och potentiellt skadliga försök att komma åt eller utnyttja databaser. Du meddelas om misstänkta aktiviteter, potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasen åtkomstmönster. Hotidentifieringsaviseringar kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/) och ange information om misstänkt aktivitet och rekommenderar åtgärd att undersöka och minska risken.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering 

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication-configure.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning. 

### <a name="authentication"></a>Autentisering 
SQL-autentisering för databasen syftar på hur användare bevisa sin identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:  

- SQL-autentisering, som använder ett användarnamn och lösenord.
- Azure Active Directory-autentisering, som använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerad domäner.  

### <a name="authorization"></a>Auktorisering

Auktorisering refererar till vilka en användare kan utföra i en Azure SQL Database och styrs av ditt användarkonto databasen rollmedlemskap och behörigheter på objektnivå. Hanterade instansen har samma funktioner för auktorisering som SQL Server 2017. 

## <a name="database-migration"></a>Databasmigrering 

Hanterade instans mål användarscenarier masslagring databasen migreringen från lokala eller IaaS databasen implementeringar.  Hanterade instans stöder flera alternativ för migrering av databasen: 

### <a name="data-migration-service"></a>Migrering datatjänst

Tjänsten Azure Database migrering är en helt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure Data plattformar med minimal avbrottstid.   Den här tjänsten förenklar uppgifter som krävs för att flytta befintliga tredjeparts- och SQL Server-databaser till Azure. Distributionsalternativ inkluderar Azure SQL Database, hanteras instans och SQL Server i Azure VM i Public Preview. Se [hur du migrerar lokala databasen till hanterade instans med DMS](https://aka.ms/migratetoMIusingDMS).  

### <a name="backup-and-restore"></a>Säkerhetskopiering och återställning  

Metod för migrering utnyttjar SQL-säkerhetskopiering till Azure blob storage. Säkerhetskopior som lagras i Azure storage blob kan återställas direkt till hanterade instans. 

## <a name="sql-features-supported"></a>SQL-funktioner som stöds 

Hanterade instans syftet är att leverera nära ytan 100% kompatibilitet med lokal SQL Server kommer i steg tills tjänsten Allmän tillgänglighet. För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
 
Hanterade instans stöder bakåtkompatibilitet till SQL 2008-databaser.  Direkt migrering från SQL 2005 databasservrar stöds, kompatibilitetsnivå för migrerade SQL 2005-databaserna har uppdaterats till SQL 2008. 
 
Följande diagram ger en översikt över ytan kompatibilitet i hanterade instans:  

![Migrering](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Viktiga skillnader mellan SQL Server lokalt och hanteras instans 

Hanterade instans fördelar från att alltid upp-hittills i molnet, vilket innebär att vissa funktioner i lokal SQL Server kan vara antingen föråldrade avyttras eller har alternativ.  Det finns särskilda fall när verktygen behöver känner igen att en viss funktion fungerar på ett något annorlunda sätt eller att tjänsten inte körs i en miljö som du inte helt styra: 

- Hög tillgänglighet är inbyggd och förkonfigurerade. Funktioner med hög tillgänglighet alltid på exponeras inte på samma sätt som i SQL-IaaS-implementeringar 
- Automatisk säkerhetskopiering och punkt tidpunkt för återställning. Kunden kan initiera `copy-only` säkerhetskopieringar som inte stör automatisk säkerhetskopiering kedjan. 
- Hanterade instansen tillåter inte att ange fullständiga fysiska sökvägar så alla motsvarande scenarier stöds inte på samma sätt: ÅTERSTÄLLA DB stöder inte WITH MOVE, skapa DB tillåter inte fysiska sökvägar, BULK INSERT fungerar med Azure BLOB endast osv. 
- Hanterade instans stöder [Azure AD authentication](sql-database-aad-authentication.md) moln alternativ till Windows-autentisering. 
- Hanterade instans hanterar automatiskt XTP-filgruppen och filer som innehåller objekt i minnet OLTP-databaser
 
### <a name="managed-instance-administration-features"></a>Hanterade instans-funktioner för administration  

Hanterade instans aktivera systemadministratören att fokusera på vad gäller de flesta för företag. Många system administratör/DBA aktiviteter krävs inte eller så är enkel. Till exempel OS / RDBMS installationen och korrigering, dynamiska instans storleksändring och konfiguration, säkerhetskopiering, databasreplikering (inklusive systemdatabaser), konfiguration för hög tillgänglighet och konfigurationen av hälsotillstånd och prestanda övervakningsdata dataströmmar.  

> [!IMPORTANT]
> En lista över funktioner som stöds, delvis stöds och som inte stöds, se [SQL databasfunktioner](sql-database-features.md). En lista över T-SQL-skillnader i hanterade instanser jämfört med SQL Server finns [hanteras instans T-SQL-skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Nästa steg

- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
- En självstudiekurs som skapar en instans som hanteras och återställer en databas från en säkerhetskopia finns [skapa en instans för hanterade](sql-database-managed-instance-tutorial-portal.md).
- En självstudiekurs som använder Azure databas migrering Service (DMS) för migrering finns [hanteras instans migrering med hjälp av DMS](../dms/tutorial-sql-server-to-managed-instance.md).
