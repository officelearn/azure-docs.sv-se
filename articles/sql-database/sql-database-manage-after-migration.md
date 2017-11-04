---
title: Hantera efter migreringen - Azure SQL Database | Microsoft Docs
description: "Lär dig hur du hanterar din databas efter migrering till Azure SQL Database."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: SQL
ms.prod_service: sql-database
ms.component: management
ms.openlocfilehash: 663ab4aaf229f8a88d1116b34ccb74450aa66c9d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>Hur ska hantera Azure SQL-databas efter migreringen?

*Vanliga frågor kring att hantera dina Azure SQL Database-investeringar*

Så du nyligen har flyttat SQL Server-databaser till Azure SQL Database eller kanske du tänker flytta inom kort. När du har flyttat vad kommer härnäst? Med hänsyn till att SQL-databasen är en *plattform som en tjänst*, Microsoft hanterar flera områden för din räkning. Men hur exakt ändras företagets praxis runt viktiga områden, till exempel säkerhet, affärskontinuitet, databasunderhåll, Prestandajustering, övervakning och mer? 

Syftet med den här artikeln är att kortfattat ordna resurser och riktlinjer som du behöver för att göra en övergång till att hantera dina investeringar i SQL-databas. Huvudområden i den här artikeln beskriver affärskontinuitet, säkerhet, databasunderhåll och övervakning, data om prestanda och transport. Vi tar upp viktiga områden som skiljer sig mellan SQL Server och SQL-databasen och anropar reda operativa metodtips som hjälper dig att maximera fördelar och minimera risker. 

## <a name="manage-business-continuity-after-migration"></a>Hantera affärskontinuitet efter migreringen

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hur jag för att skapa och hantera säkerhetskopior på SQL-databasen? 
SQL-databas säkerhetskopierar databaser för dig och ger dig möjlighet att återställa till valfri punkt i tiden i kvarhållningsperioden automatiskt. Bevarandeperiod 35 dagar för Standard- och Premium-databaser och 7 dagar för enkla databaser. Dessutom kan långsiktiga kvarhållning-funktionen du lagra säkerhetskopior för en längre period (upp till 10 år) och återställa från dessa säkerhetskopior när som helst. Dessutom är databassäkerhetskopiorna georeplikerad så möjligheten att geo-återställning i en region vid en katastrof eller regionala allvarlig händelse. Se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>Hur jag för att garantera kontinuitet för företag vid en katastrof datacenter-nivån eller en regional allvarlig händelse? 

Säkerhetskopiering av databaser är georeplikerad så möjligheten att geo-återställning i en region vid en katastrof eller regionala allvarlig händelse. Se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md). SQL Database tillhandahåller dessutom möjlighet att underhålla aktivt georeplikerad sekundära databaser i en annan region. Konfigurera dem i en grupp med automatisk redundans säkerställer att databaserna automatiskt växla över till sekundärt i ett scenario för katastrofåterställning. Om en grupp med automatisk växling vid fel inte är konfigurerad, måste programmet att övervaka för en katastrofåterställning och påbörja en växling till sekundärt aktivt. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server anges mig läsbara sekundära repliker, kan jag använda sekundärservrar på SQL-databasen? 

Ja, funktionen ”aktiv Geo-replikering” används för att skapa läsbara sekundära repliker. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>Hur min plan för katastrofåterställning ändras från lokal till SQL Database? 
SQL Server-implementeringar måste du aktivt hantera säkerhetskopior med hjälp av funktioner, till exempel redundanskluster databasspegling, replikering, Loggöverföring eller bara vanlig vanliga BACPAC säkerhetskopior. Men på SQL-databas säkerhetskopieringar fullständigt som hanteras av Microsoft och du kan bara ha säkerhetskopiering och katastrofåterställning återställningsplaner konfigurerad och arbeta med bara några klick på Azure-portalen (eller några kommandon på PowerShell). ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>Vid katastrofåterställning, hur återställer jag min databaser? 
SQL-databasen kan automatiskt du återställa databaserna till en valfri tidpunkt i de senaste 35 dagarna. Detta är ett alternativ om du förlorar data eller står inför en programrelaterad katastrofåterställning. 

Om du står inför regionala katastrofåterställning, om geo-replikerade sekundära databaser har konfigurerats kan du återställa från din geo sekundära databaser i en annan region. För realtid åtkomst till dina program kan du växla över till den geo sekundärt i andra region manuellt. Du kan också om du har automatisk redundans grupp konfigureras sker den här redundansen för geo-sekundära automatiskt i ett scenario med disaster. Om du inte har georeplikerad sekundär databas som har konfigurerats, du kan fortfarande återställa databaserna från automatisk replikerade säkerhetskopior (inbyggd funktion konfiguration krävs inte), med relativt längre återställningstid (12 timmar RTO) och upp till en timme dataförlust. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>Är redundans till sekundär transparenta? Hur hanterar databasen växling vid fel i mitt program? 
Om du har konfigurerade för automatisk redundans grupper är redundans till sekundär transparent. Men om du inte har måste programmet innehålla logik för att övervaka tillgängligheten för den primära servern och sedan manuellt växla över till sekundärt. 
 
## <a name="manage-security-after-migration"></a>Hantera säkerheten efter migreringen

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Hur kan jag begränsa åtkomsten till SQL-databasen? 
 
Det finns några sätt att låsa anslutningen åtkomst till SQL-databaser. 
1. Begränsa trafik över Internet Express Route ger du dedikerad fiber till Azure-nätverket så att dina data inte överföras via internet. Du kan också konfigurera mellan region anslutning med hjälp av expressroute. Följande länkar beskriver Express Route i detalj: 
 - [Introduktion på Express Route](../expressroute/expressroute-introduction.md)
 - [Förutsättningar](../expressroute/expressroute-prerequisites.md) 
 - [Arbetsflöden](../expressroute/expressroute-workflows.md) 
 
2. Välj vilka resurser som ska ansluta till SQL-databasen: 

   SQL-databasen är som standard konfigurerad att ”Tillåt alla Azure-tjänster” – vilket innebär att alla virtuella datorer i Azure kan försöka ansluta till databasen.  Autentisering av alla inloggningar fortfarande genomföras. Om du inte vill att databasen ska vara tillgänglig för alla Azure IP-adresser, kan du inaktivera ”Tillåt alla Azure-tjänster” och använda [VNET Tjänsteslutpunkter](sql-database-vnet-service-endpoint-rule-overview.md) att begränsa inkommande åtkomst till databasen från endast Azure-resurser som finns inom en viss Azure VNET-undernätet. 

   ![Slutpunkter för virtuella nätverk](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   Ett alternativ är att etablera [reserverade IP-adresser](../virtual-network/virtual-networks-reserved-public-ip.md) för virtuella datorer och godkända de specifika VM IP-adresser i servern brandväggsinställningar. (Se skärmbilden nedan som exempel i Azure-portalen.) Genom att tilldela reserverade IP-adresser kan spara du att uppdatera brandväggsreglerna ändrar IP-adresser. 

3. Undvika port 1433 utanför Azure

   Kör SSMS i Azure med hjälp av [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Detta behöver du inte att öppna port 1433 utgående anslutningar, IP-Adressen är statisk så att databasen kan vara öppet för endast RemoteApp, den stöder Multi Factor Authentication (MFA) och flera användare. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>Vilka autentiseringsmetoder som erbjuds i SQL-databasen?

De viktigaste metoderna som erbjuds i SQL Database och SQL Data Warehouse är Azure Active Directory-autentisering och SQL-autentisering. Azure Active Directory (AD) är en centraliserad identitets- och management-tjänsten och SQL är bara en av många Azure-tjänster som är integrerade med Azure AD. Fördelen med en centraliserad hanterad tjänst är att en användares autentiseringsuppgifter delas över alla Azure-tjänster som du använder för enklare autentisering. Detta kan också SQL Database och SQL Data Warehouse att erbjuda Multi-Factor authentication och Gäst användarkonton i Azure AD-domän. 

Om du redan har ett Active Directory lokalt kan du federera katalogen med Azure Active Directory för att utöka din katalog till Azure. 


|||
|---|---|
| Om du…|Azure SQL-databas / Azure SQL Data Warehouse|
| Inte vill använda Azure Active Directory (AD) i Azure|Använd [SQL-autentisering](sql-database-security-overview.md)|
| Använda AD på SQLServer lokalt|[Federera AD med Azure AD](../active-directory/connect/active-directory-aadconnect.md), och använda Azure AD-autentisering. Med den här, kan du använda enkel inloggning.|
| Om du behöver använda multifaktorautentisering (MFA)|Kräva MFA som en princip via [villkorlig åtkomst för Microsoft](sql-database-conditional-access.md), och använda [Azure AD Universal autentisering med stöd för MFA](sql-database-ssms-mfa-authentication.md).|
| Har gästkonton från Microsoft-konton (live.com, outlook.com) eller andra domäner (gmail.com)|Använd [Azure AD Universal autentisering](sql-database-ssms-mfa-authentication.md) i databasen /-informationslager, som använder [Azure AD B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| Loggas Windows med dina autentiseringsuppgifter för Azure AD från en federerad domän|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
| Loggas Windows med autentiseringsuppgifter från en domän som inte är federerat med Azure|Använd [Azure AD-lösenordsautentisering](sql-database-aad-authentication-configure.md).|
| Tjänster för mellannivå som behöver ansluta till Azure SQL-databas eller datalager|Använd [Azure AD-tokenautentisering](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>Hur kan jag begränsa åtkomst till känsliga data i min databaser från program sida? 

Om du vill förhindra att obehöriga användare från att kunna visa känsliga data, finns det några alternativ i SQL-databas: 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) är en typ av kryptering på klientsidan som krypterar känsliga kolumner i databasen (så att de är i ciphertext databasadministratörer och obehöriga användare). Nyckeln för Always Encrypted lagras på klientsidan, så att endast auktoriserade klienter kan dekryptera känsliga kolumner. Krypterad stöder alltid lika jämförelser idag så DBAs kan fortsätta att fråga krypterade kolumner som en del av sin SQL-kommandon. Alltid krypterat kan användas med en mängd olika KeyStore-alternativ som [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows-certifikatarkivet och lokala maskinvarusäkerhetsmoduler.
- [Dynamisk Datamaskering](sql-database-dynamic-data-masking-get-started.md) är en funktion för maskering av data som begränsar exponering av känsliga data genom att maskera till icke-privilegierade användare på programnivån. Du definierar en maskningsregel som kan skapa ett mönster för maskering (till exempel att endast visa sista 4 siffrorna i ett nationellt ID och markera resten som x) och identifiera vilka användare som kan uteslutas från maskering regeln.
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) låter dig att styra åtkomst till rader i en databastabell baserat på användaren som kör frågan (grupp medlemskap eller köra sammanhang). Begränsningen åtkomst görs på databasnivå i stället för i en program-nivå, att förenkla app-logiken. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Vilka krypteringsalternativ finns i SQL-databas och vilka aktörer kryptering skyddar från?
Det finns tre huvudsakliga krypteringstekniker som är tillgängliga i SQL-databasen: 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (som nämns i frågan ovan): krypterar känsliga kolumner i tabellen slutpunkt till slutpunkt, från obehörig klienter till den fysiska disken. Server och dataadministratörer kan inte se känsliga data, eftersom krypteringsnycklarna lagras på klienten. 
- [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE): kryptering i vila, som krypterar på databasnivå och skyddar filer, loggfiler och tillhörande säkerhetskopior från fysiska media stöld. TDE är aktiverad som standard för alla nya databaser.
 
  Följande diagram visar en översikt över krypteringen tekniska alternativ.

   ![Översikt över kryptering](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>Hur får jag för att hantera krypteringsnycklar i molnet? 
Det finns alternativ för nyckelhantering för Always Encrypted (kryptering på klientsidan) och Transparent datakryptering (kryptering i vila). Vi rekommenderar för att rotera krypteringsnycklar regelbundet och med en frekvens som överensstämmer med både interna och kompatibilitet krav.

- **Always Encrypted**: det finns en [två nyckel hierarkin](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) i Always Encrypted – en kolumn av känsliga data krypteras med en AES 256 kolumnkrypteringsnyckeln (CEK), som i sin tur är krypterad med kolumnhuvudnyckeln (CMK). Klientdrivrutinerna för Always Encrypted har inga begränsningar CMKs längd.

  Det krypterade värdet för CEK lagras i databasen och CMK lagras i en betrodd KeyStore, till exempel Windows certifikatarkiv, Azure Key Vault eller en maskinvarusäkerhetsmodul. 
  
  Både den [CEK och CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kan roteras. CEK rotation kan vara tidskrävande beroende på storleken på de tabeller som innehåller krypterade kolumner. Planera därför CEK rotationer mycket noggrant. CMK rotation, å andra sidan stör inte databasprestanda och kan göras med åtskilda roller.

  Följande diagram visar KeyStore-alternativen för huvudnycklar kolumn i Always Encrypted 

   ![Alltid krypterad CMK lagra providers](./media/sql-database-manage-after-migration/always-encrypted.png)

- **Transparent Data kryptering (TDE)**: det finns en två-key-hierarki i TDE – data i varje användardatabas krypteras med en symmetrisk AES 256 databasen unika databaskrypteringsnyckel (DEK), som i sin tur är krypterad med en unik server asymmetriska RSA 2048 huvudnyckel. 

  Som standard hanteras huvudnyckeln för Transparent datakryptering av tjänsten SQL-databas i informationssyfte. Om organisationen vill ha kontroll över huvudnyckeln, finns alternativet att använda [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) som nyckelarkivet. 

  Med hjälp av Azure Key Vault förutsätter organisationen kontroll över viktiga etablering, rotation och behörigheten kontroller. [Rotation eller växla typ av en huvudnyckel TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) är snabb, eftersom det endast krypterar DEK. 

  För organisationer med åtskillnad av roller mellan säkerhet och hantering av kan en säkerhets-administratör etablera nyckelmaterial för huvudnyckeln TDE i Azure Key Vault och förse databasadministratören för ett Azure Key Vault nyckelidentifierare kryptering i vila på en server. 

## <a name="monitoring-and-compliance-after-migration"></a>Övervakning och efterlevnad efter migreringen

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Hur övervakar databasaktiviteter i SQL-databasen?
Det finns några övervakning funktioner finns inbyggda i SQL-databasen, som spårar säkerhet och andra händelser för databasen:
- [SQL-granskning](sql-database-auditing.md) kan du samla in granskningsloggar databasen händelser i din egen Azure Storage-konto.
- [SQL-Hotidentifiering](sql-database-threat-detection.md) kan du identifiera misstänkta aktiviteter som indikerar ett möjligt skadliga åtgärder kan komma åt, bryta mot eller utnyttja data i databasen. Hotidentifiering för SQL-databasen körs flera uppsättningar av algoritmer som identifierar potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasen åtkomstmönster (till exempel åtkomst från en ovanlig plats eller av en okänd huvudnamn). Säkerhet polis eller andra avsedda administratörer får ett e-postmeddelande om ett hot upptäcks på databasen. Varje meddelande innehåller information om misstänkt aktivitet och rekommendationer om hur du ytterligare undersöka och minska risken. 
- [SQL Vulnerability Assessment](sql-vulnerability-assessment.md) är en databas genomsöka och rapportera tjänst som gör att du kan övervaka säkerhetstillståndet hos dina databaser i skala, och identifiera säkerhetsrisker och driva från en säkerhetsbaslinje som du definierar. Efter varje skanning tillhandahålls en egen lista över tillämplig steg och reparation skript samt en rapport som kan användas för att hjälpa för att uppfylla efterlevnad. 
- [SQL-OMS Sync säkerhetsprogram](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) använder Operations Management Suite (OMS) offentliga API: er för att vidarebefordra SQL granskningsloggar till OMS för logganalys och möjligheten att definiera anpassade identifiering aviseringar, inklusive: 
 - SQL-databas Audit panelen & instrumentpanel som tillhandahåller klart och sammanhängande rapport för databasaktiviteter. 
 - SQL Log Analytics för att analysera dina Databasaktivitet och undersöka avvikelser och fel som kan tyda på säkerhetsöverträdelser har gjorts.
 - Avancerade aviseringar särskilda regler på observerade händelser som utlöser e-post, aviseringar Webhook och Azure automation-runbook (t.ex. ändring av lösenord, efter kontorstid, specifik SQL-kommandon).
- [Azure Security Center](../security-center/security-center-intro.md) tillhandahåller centraliserad hantering över arbetsbelastningar som körs i Azure, lokalt och i andra moln. Du kan visa om grundläggande skydd för SQL-databas, till exempel granskning och Transparent datakryptering är konfigurerade på alla resurser och skapa principer baserat på dina egna behov. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Är SQL-databas som är kompatibel med eventuella krav och som sätt med min egen organisation efterlevnad? 
Azure SQL Database är kompatibla med en mängd regelverk compliances. Om du vill visa den senaste uppsättningen compliances har uppfyllts, finns det [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) och detaljgranska compliances som är viktiga för din organisation att se om Azure SQL Database ingår under kompatibla Azure tjänster. Det är viktigt att Observera att även om Azure SQL Database kan vara certifierat kompatibla tjänster, det hjälper till att kompatibiliteten för organisationens tjänst men garanterar inte att automatiskt den. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Databasunderhåll och övervakning efter migreringen

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>Hur övervakar tillväxt i data storlek och resursen användning?

- Du kan visa Övervakare mått om din databas storlek och resursen användningsnivån på diagrammet ”övervakning' i Azure-portalen. 

  ![Övervakning av diagram](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Du kan använda 'Query Performance Insight' tillgängliga på Azure-portalen för att få en djupare inblick och öka detaljnivån information om frågor. Detta kräver att Query Store är aktiv på din databas.

  ![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Alternativt kan du visa mått med dynamiska hanteringsvyer (av DMV: er) för – med [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>Hur ofta behöver jag kör konsekvenskontroller som DBCC_CHECKDB?
DBCC_CHECKDB kontrollerar logiska och fysiska integriteten för alla objekt i databasen. Du behöver inte längre göra de här kontrollerna eftersom dessa hanteras av Microsoft på Azure. Mer information finns i [dataintegriteten i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Övervaka användningen av prestanda- och efter migreringen

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Hur övervakar jag prestanda och resursanvändningen i Azure SQL Database?
Du kan övervaka prestanda och resurs-användning i Azure SQL Database med följande metoder:

- **Azure-portalen**: Azure-portalen visar en enskild databas resursutnyttjning genom att välja databasen och klicka på diagrammet i översiktsfönstret. Du kan ändra diagrammet så att flera mått, inklusive CPU-procent, DTU-procent, Data IO-procent, sessioner procent och procent av databasen. 
  ![utnyttjande av nätverksresurser](./media/sql-database-manage-after-migration/resource-utilization.png)

  Du kan också konfigurera aviseringar efter resurs från det här diagrammet. Dessa aviseringar kan du svara på resursen villkor med ett e-postmeddelande, skriva till en HTTPS/HTTP-slutpunkt eller utföra en åtgärd. Finns det [övervaka databasprestanda i Azure SQL Database](sql-database-single-database-monitor.md) detaljerade anvisningar.

- **Vyer**: du kan fråga efter den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamisk hanteringsvy att returnera resurs förbrukning tidigare från den senaste timmen och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) system katalogvyn att returnera historik för de senaste 14 dagarna. 

- **Query Performance Insight**: [Query Performance Insight](sql-database-query-performance.md) kan du se en historik över de vanligaste frågorna för förbrukning av resursen och tidskrävande frågor för en viss databas. Den här funktionen kräver [Frågearkivet](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) ska vara aktiverad och aktiv för databasen.

- **Azure SQL analyser (förhandsgranskning) i logganalys**: [Azure logganalys](../log-analytics/log-analytics-azure-sql.md) kan du samla in och visualisera viktiga Azure SQL Azure-prestandamått, stöd för upp till 150 000 Azure SQL-databaser och elastiska 5 000 SQL Pooler per arbetsytan. Du kan använda den för att övervaka och ta emot meddelanden. Du kan övervaka Azure SQL Database och elastisk pool mått över flera Azure-prenumerationer och elastiska pooler och kan användas för att identifiera problem på varje nivå i en programstack. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Hur kontrollerar jag att jag använder rätt tjänstnivå och prestandanivå servicenivån?
Övervaka den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamiska hanteringsvyer för att förstå förbrukning av CPU, i/o och minne. Du kan också använda SQL-databas [Query Performance Insight](sql-database-query-performance.md) att se resursanvändningen. Om du kör konsekvent på en hög andel tillgängliga resurser, bör du du överväga att flytta till en högre prestandanivå inom befintliga tjänstnivån eller flytta till en högre tjänstnivå. Om du regelbundet använder en liten andel av tillgängliga resurser kan du däremot du överväga att flytta till en lägre prestandanivå eller servicenivå.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jag får prestandaproblem. Hur skiljer sig min Azure SQL Database felsökning metoder från SQL Server?
Många aspekter av din prestanda felsökning metod förblir detsamma i Azure SQL Database finns, men det vissa skillnader. Till exempel om du ser en försämring i prestandan övervaka den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamiska hanteringsvyer för att förstå CPU, i/o och minne förbrukning. Du kan behöva ändra prestandanivå och servicenivå baserat på arbetsbelastning.
En omfattande uppsättning rekommendationer för att finjustera prestandaproblem, se [justera prestanda i Azure SQL Database](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>Behöver jag Underhåll index och statistik?
Azure SQL-databasen har inte index och statistik automatiskt som en del av tjänsten. Du ansvarar för att schemalägga underhåll av index och statistik. I följande artikel Azure Automation-metoder, detaljerad information om flera alternativ för schemaläggning av Underhållsjobb mot Azure SQL-databasen.

## <a name="data-movement-after-migration"></a>Flytt av data efter migreringen

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Hur jag för att exportera och importera data som BACPAC filer från Azure SQL Database? 

- **Exportera**: du kan exportera din Azure SQL-databas som en BACPAC-fil från Azure-portalen.

  ![Exportera som en BACPAC](./media/sql-database-export/database-export.png)

- **Importera**: du kan importera som BACPAC filen till en databas med Azure-portalen.

  ![Importera en BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Hur synkronisera data mellan Azure SQL Database SQL Server 2016 / 2012?
Den [datasynkronisering](sql-database-sync-data.md) funktionen hjälper dig att synkronisera data båda riktningarna mellan flera lokala SQL Server-databaser och Azure SQL Database. Eftersom det är utlösning baserat slutliga konsekvensen garanteras (ingen dataförlust), men transaktionen konsekvenskontroll kan inte garanteras. 

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [Azure SQL Database](sql-database-technical-overview.md).
