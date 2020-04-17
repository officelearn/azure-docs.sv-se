---
title: Översikt över säkerhet
description: Lär dig mer om Azure SQL Database och SQL Server-säkerhet, inklusive skillnaderna mellan molnet och SQL Server lokalt.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 1d08770d81ade2d976142b2ce1fd94dae044cf32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461403"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>En översikt över azure SQL Database-säkerhetsfunktioner

I den här artikeln beskrivs grunderna för att skydda datanivån för ett program med Azure SQL Database. Den säkerhetsstrategi som beskrivs följer den skiktade försvars-djupgående metoden som visas i bilden nedan, och rör sig utifrån i:

![sql-säkerhet-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Nätverkssäkerhet

Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för moln- och företagsprogram. För att skydda kunddata förhindrar brandväggar nätverksåtkomst till databasservern tills åtkomst uttryckligen beviljas baserat på IP-adress eller Azure Virtual Network Traffic Origin.

### <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen

IP-brandväggsregler ger åtkomst till databaser baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i [Översikt över Azure SQL Database och SQL Data Warehouse-brandväggsregler](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk

[Slutpunkter för virtuella nätverkstjänster](../virtual-network/virtual-network-service-endpoints-overview.md) utökar din virtuella nätverksanslutning över Azure-stamnätet och aktiverar Azure SQL Database för att identifiera det virtuella nätverksundernät som trafiken kommer från. Om du vill tillåta trafik att nå Azure SQL Database använder du [SQL-tjänsttaggarna](../virtual-network/security-overview.md) för att tillåta utgående trafik via nätverkssäkerhetsgrupper.

[Regler för virtuella nätverk](sql-database-vnet-service-endpoint-rule-overview.md) gör det möjligt för Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

> [!NOTE]
> Att styra åtkomst med brandväggsregler gäller *inte* för **en hanterad instans**. Mer information om nätverkskonfigurationen finns i [ansluta till en hanterad instans](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Åtkomsthantering

> [!IMPORTANT]
> Hantera databaser och databasservrar i Azure styrs av portalanvändarkontots rolltilldelningar. Mer information om den här artikeln finns [i Rollbaserad åtkomstkontroll i Azure Portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autentisering

Autentisering är processen att bevisa att användaren är den de påstår sig vara. Azure SQL Database stöder två typer av autentisering:

- **SQL-autentisering:**

    SQL-databasautentisering refererar till autentisering av en användare när du ansluter till [Azure SQL Database](sql-database-technical-overview.md) med användarnamn och lösenord. När databasservern skapas för databasen måste en "Server admin" inloggning med ett användarnamn och lösenord anges. Med hjälp av dessa autentiseringsuppgifter kan en "serveradministratör" autentisera till valfri databas på databasservern som databasägare. Därefter kan ytterligare SQL-inloggningar och användare skapas av serveradministratören, vilket gör det möjligt för användare att ansluta med användarnamn och lösenord.

- **Azure Active Directory-autentisering:**

    Azure Active Directory-autentisering är en mekanism för anslutning till [Azure SQL Database](sql-database-technical-overview.md) och SQL Data [Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (Azure AD). Azure AD-autentisering gör det möjligt för administratörer att centralt hantera identiteter och behörigheter för databasanvändare tillsammans med andra Microsoft-tjänster på en central plats. Detta inkluderar minimering av lösenordslagring och aktiverar centraliserade principer för lösenordsrotation.

     En serveradministratör som kallas **Active Directory-administratören** måste skapas för att använda Azure AD-autentisering med SQL Database. Mer information finns i [Ansluta till SQL Database med hjälp av Azure Active Directory Authentication](sql-database-aad-authentication.md). Azure AD-autentisering stöder både hanterade och federerade konton. De federerade kontona stöder Windows-användare och -grupper för en kunddomän som federerats med Azure AD.

    Ytterligare Azure AD-autentiseringsalternativ som är tillgängliga är [Active Directory Universal Authentication for SQL Server Management Studio-anslutningar,](sql-database-ssms-mfa-authentication.md) inklusive [multifaktorautentisering](../active-directory/authentication/concept-mfa-howitworks.md) och [villkorlig åtkomst](sql-database-conditional-access.md).

> [!IMPORTANT]
> Hantera databaser och servrar i Azure styrs av ditt portalanvändarkonto rolltilldelningar. Mer information om den här artikeln finns [i Rollbaserad åtkomstkontroll i Azure Portal](../role-based-access-control/overview.md). Att styra åtkomst med brandväggsregler gäller *inte* för **en hanterad instans**. Se följande artikel om [anslutning till en hanterad instans](sql-database-managed-instance-connect-app.md) för mer information om nätverkskonfigurationen som behövs.

## <a name="authorization"></a>Auktorisering

Auktorisering refererar till de behörigheter som tilldelats en användare i en Azure SQL-databas och avgör vad användaren får göra. Behörigheter styrs genom att användarkonton läggs till i [databasroller](/sql/relational-databases/security/authentication-access/database-level-roles) och tilldela behörigheter på databasnivå till dessa roller eller genom att användaren beviljas vissa [behörigheter på objektnivå](/sql/relational-databases/security/permissions-database-engine). Mer information finns i [Inloggningar och användare](sql-database-manage-logins.md)

Du kan göra det bästa genom att skapa anpassade roller när det behövs. Lägg till användare i rollen med de lägsta behörigheter som krävs för att utföra sin jobbfunktion. Tilldela inte behörigheter direkt till användare. Serveradministratörskontot är medlem i den inbyggda db_owner roll, som har omfattande behörigheter och bör endast beviljas för få användare med administrativa uppgifter. För Azure SQL Database-program använder du [KÖR SOM](/sql/t-sql/statements/execute-as-clause-transact-sql) för att ange körningskontexten för den anropade modulen eller använda [programroller](/sql/relational-databases/security/authentication-access/application-roles) med begränsade behörigheter. Den här metoden säkerställer att programmet som ansluter till databasen har de lägsta behörigheter som krävs av programmet. Genom att följa dessa bästa metoder främjas också åtskillnad mellan arbetsuppgifter.

### <a name="row-level-security"></a>Säkerhet på radnivå

Med säkerhet på radnivå kan kunder styra åtkomsten till rader i en databastabell baserat på egenskaperna hos den användare som kör en fråga (till exempel gruppmedlemskap eller körningskontext). Säkerhet på radnivå kan också användas för att implementera anpassade etikettbaserade säkerhetsbegrepp. Mer information finns i [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Hotskydd

SQL Database skyddar kunddata genom att tillhandahålla gransknings- och hotidentifieringsfunktioner.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-granskning i Azure Monitor-loggar och eventhubbar

SQL Database-granskning spårar databasaktiviteter och hjälper till att upprätthålla efterlevnaden av säkerhetsstandarder genom att registrera databashändelser i en granskningslogg i ett kundägt Azure-lagringskonto. Granskning gör det möjligt för användare att övervaka pågående databasaktiviteter, samt analysera och undersöka historisk aktivitet för att identifiera potentiella hot eller misstänkt missbruk och säkerhetsöverträdelser. Mer information finns i Komma igång med [SQL Database Auditing](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Avancerat skydd mot hot analyserar dina SQL Server-loggar för att upptäcka ovanliga beteenden och potentiellt skadliga försök att komma åt eller utnyttja databaser. Aviseringar skapas för misstänkta aktiviteter som SQL-injektion, potentiell datainfiltration och brute force-attacker eller för avvikelser i åtkomstmönster för att fånga upp eskalering av privilegier och autentiseringsuppgifter. Aviseringar visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/), där information om misstänkta aktiviteter tillhandahålls och rekommendationer för ytterligare undersökning ges tillsammans med åtgärder för att minska hotet. Avancerat skydd mot hot kan aktiveras per server mot en extra avgift. Mer information finns i [Komma igång med SQL Database Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Informationsskydd och kryptering

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS för transportlagersäkerhet (kryptering-under överföring)

SQL Database skyddar kunddata genom att kryptera data i rörelse med [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Sql Server framtvingar kryptering (SSL/TLS) hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och servern oavsett inställningen **av Encrypt** eller **TrustServerCertificate** i anslutningssträngen.

Du rekommenderar därför att du i programmets anslutningssträng anger en krypterad anslutning och _**inte**_ litar på servercertifikatet. Detta tvingar ditt program att verifiera servercertifikatet och förhindrar därmed att ditt program blir sårbart för man i mittentypens attacker.

Till exempel när du använder ADO.NET drivrutinen detta sker via **Encrypt = True** och **TrustServerCertificate = False**. Om du hämtar din anslutningssträng från Azure-portalen har den rätt inställningar.

> [!IMPORTANT]
> Observera att vissa drivrutiner som inte kommer från Microsoft kanske inte använder TLS som standard eller är beroende av en äldre version av TLS (<1.2) för att fungera. I det här fallet kan du fortfarande ansluta till databasen i SQL Server. Vi rekommenderar dock att du utvärderar säkerhetsriskerna med att tillåta sådana drivrutiner och program att ansluta till SQL Database, särskilt om du lagrar känsliga data.
>
> Mer information om TLS och anslutning finns i [TLS-överväganden](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent datakryptering (kryptering i vila)

[Transparent datakryptering (TDE) för Azure SQL Database](transparent-data-encryption-azure-sql.md) lägger till ett säkerhetslager för att skydda data i vila från obehörig eller offlineåtkomst till råa filer eller säkerhetskopior. Vanliga scenarier är datacenterstöld eller oskyddad kassering av maskinvara eller media, till exempel hårddiskar och säkerhetskopieringsband.TDE krypterar hela databasen med hjälp av en AES-krypteringsalgoritm, som inte kräver att programutvecklare gör några ändringar i befintliga program.

I Azure krypteras alla nyskapade SQL-databaser som standard och databaskrypteringsnyckeln skyddas av ett inbyggt servercertifikat.  Underhåll och rotation av certifikat hanteras av tjänsten och kräver ingen indata från användaren. Kunder som föredrar att ta kontroll över krypteringsnycklarna kan hantera nycklarna i [Azure Key Vault](../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Nyckelhantering med Azure Key Vault

[Med stöd](transparent-data-encryption-byok-azure-sql.md) för din egen nyckel (BYOK) för [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) kan kunder bli ägare till nyckelhantering och rotation med hjälp av [Azure Key Vault](../key-vault/general/secure-your-key-vault.md), Azures molnbaserade externa nyckelhanteringssystem. Om databasens åtkomst till nyckelvalvet återkallas kan en databas inte dekrypteras och läsas in i minnet. Azure Key Vault tillhandahåller en central viktig hanteringsplattform, utnyttjar noggrant övervakade maskinvarusäkerhetsmoduler (HSM) och möjliggör åtskillnad mellan uppgifter mellan hantering av nycklar och data för att uppfylla kraven på säkerhetsefterlevnad.

### <a name="always-encrypted-encryption-in-use"></a>Alltid krypterad (kryptering som används)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine) är en funktion som är utformad för att skydda känsliga data som lagras i specifika databaskolumner från åtkomst (till exempel kreditkortsnummer, nationella id-nummer eller data _på behov av att veta)._ Detta inkluderar databasadministratörer eller andra privilegierade användare som har behörighet att komma åt databasen för att utföra hanteringsuppgifter, men som inte har något affärsbehov att komma åt vissa data i de krypterade kolumnerna. Data krypteras alltid, vilket innebär att de krypterade data dekrypteras endast för bearbetning av klientprogram med tillgång till krypteringsnyckeln.  Krypteringsnyckeln exponeras aldrig för SQL och kan lagras antingen i [Windows-certifikatarkivet](sql-database-always-encrypted.md) eller i [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Dynamisk datamaskering i SQL Database begränsar känslig dataexponering genom att maskera den för användare som inte är privilegierade. Dynamisk datamaskering identifierar automatiskt potentiellt känsliga data i Azure SQL Database och ger användbara rekommendationer för att maskera dessa fält, med minimal påverkan på programlagret. Det fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad. Mer information finns i [Komma igång med dynamisk datamaskering i SQL Database](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Säkerhetshantering

### <a name="vulnerability-assessment"></a>Sårbarhetsbedömning

[Sårbarhetsbedömning](sql-vulnerability-assessment.md) är en enkel att konfigurera tjänst som kan identifiera, spåra och hjälpa till att åtgärda potentiella databassårbarheter med målet att proaktivt förbättra den övergripande databassäkerheten. Sårbarhetsbedömning (VA) är en del av det avancerade datasäkerhetserbjudandet (ADS), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Sårbarhetsbedömning kan nås och hanteras via den centrala SQL ADS-portalen.

### <a name="data-discovery--classification"></a>Dataidentifiering och -klassificering

Dataidentifiering & klassificering (för närvarande i förhandsversion) ger avancerade funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera, etikettera och skydda känsliga data i dina databaser. Att upptäcka och klassificera dina ytterst känsliga data (företag/ekonomi, hälso- och sjukvård, personuppgifter osv.) kan spela en central roll i din organisationsinformationsskyddsstatus. Tjänsten kan fungera som infrastruktur inom följande områden:

- Olika säkerhetsscenarier, till exempel övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data.
- Styra åtkomsten till och härda säkerheten för databaser som innehåller mycket känsliga data.
- Hjälp med att uppfylla standarder för datasekretess och efterlevnadsregler.

Mer information finns i [Komma igång med dataidentifiering & klassificering](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Efterlevnad

Utöver ovanstående funktioner som kan hjälpa ditt program att uppfylla olika säkerhetskrav, deltar Azure SQL Database också i regelbundna granskningar och har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

## <a name="next-steps"></a>Nästa steg

- En diskussion om användningen av inloggningar, användarkonton, databasroller och behörigheter i SQL Database finns i [Hantera inloggningar och användarkonton](sql-database-manage-logins.md).
- En diskussion om databasgranskning finns i [SQL Database-granskning](sql-database-auditing.md).
- En diskussion om hotidentifiering finns i [SQL Database threat detection](sql-database-threat-detection.md).
