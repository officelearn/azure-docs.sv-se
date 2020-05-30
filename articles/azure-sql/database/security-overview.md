---
title: Säkerhets översikt
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig mer om säkerhet i Azure SQL Database och Azure SQL-hanterad instans, inklusive hur den skiljer sig från SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 617c06c9f9a86e8377f32a5e80102eb92e9f029c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191709"
---
# <a name="an-overview-of-azure-sql-database--sql-managed-instance-security-capabilities"></a>En översikt över säkerhets funktioner i Azure SQL Database & SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här artikeln beskriver grunderna för att skydda data nivån för ett program med hjälp av [Azure SQL Database](sql-database-paas-overview.md) och [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md). Säkerhets strategin som beskrivs följer efter det skiktade försvars fördjupet, som visas i bilden nedan, och som rör sig från utsidan i:

![SQL-Security-Layer. png](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Nätverkssäkerhet

Microsoft Azure SQL Database-och SQL-hanterad instans tillhandahåller en Relations databas tjänst för moln-och företags program. För att skydda kund information, förhindrar brand väggar nätverks åtkomst till servern förrän åtkomst uttryckligen beviljas baserat på IP-adress eller trafik ursprung i Azures virtuella nätverk.

### <a name="ip-firewall-rules"></a>Regler för IP-brandvägg

Regler för IP-brandvägg ger åtkomst till databaser baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i [Översikt över brand Väggs regler för Azure SQL Database och Azure Synapse Analytics](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk

[Tjänst slut punkter i virtuella nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md) utökar din virtuella nätverks anslutning via Azures stamnät och aktiverar Azure SQL Database för att identifiera det virtuella nätverkets undernät som trafiken kommer från. Om du vill tillåta att trafik når Azure SQL Database använder du SQL [-tjänstens Taggar](../../virtual-network/security-overview.md) för att tillåta utgående trafik via nätverks säkerhets grupper.

[Regler för virtuella nätverk](vnet-service-endpoint-rule-overview.md) gör det möjligt för Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

> [!NOTE]
> Kontroll av åtkomst med brand Väggs regler gäller *inte* för **en SQL-hanterad instans**. Mer information om nätverks konfigurationen som behövs finns i [ansluta till en SQL-hanterad instans](../managed-instance/connect-application-instance.md).

## <a name="access-management"></a>Åtkomsthantering

> [!IMPORTANT]
> Hantering av databaser och servrar i Azure styrs av ditt användar kontos roll tilldelningar för portalen. Mer information om den här artikeln finns i [rollbaserad åtkomst kontroll i Azure Portal](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Autentisering

Autentisering är en process för att bevisa att användaren är den som han eller hon ansöker. Azure SQL Database-och SQL-hanterade instanser stöder två typer av autentisering:

- **SQL-autentisering**:

    SQL Database-autentisering syftar på autentisering av en användare vid anslutning till Azure SQL Database eller Azure SQL-hanterad instans med hjälp av användar namn och lösen ord. Du måste ange en inloggning med ett användar namn och lösen ord för **Server administratören** när servern skapas. Med dessa autentiseringsuppgifter kan en **Server administratör** autentisera till valfri databas på servern eller instansen som databas ägare. Efter det kan ytterligare SQL-inloggningar och användare skapas av Server administratören, vilket gör det möjligt för användare att ansluta med användar namn och lösen ord.

- **Azure Active Directory autentisering**:

    Azure Active Directory autentisering är en mekanism för att ansluta till [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (Azure AD). Med Azure AD-autentisering kan administratörer centralt hantera identiteter och behörigheter för databas användare tillsammans med andra Azure-tjänster på en central plats. Detta inkluderar minimering av lösen ords lagring och möjliggör centraliserade principer för lösen ords rotation.

     En Server administratör som kallas **Active Directory administratör** måste skapas för att använda Azure AD-autentisering med SQL Database. Mer information finns i [ansluta till SQL Database med Azure Active Directory autentisering](authentication-aad-overview.md). Azure AD-autentisering stöder både hanterade och federerade konton. Federerade konton stöder Windows-användare och grupper för en kund domän som är federerad med Azure AD.

    Ytterligare alternativ för Azure AD-autentisering är tillgängliga [Active Directory Universal-autentisering för SQL Server Management Studio](authentication-mfa-ssms-overview.md) anslutningar inklusive [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) och [villkorlig åtkomst](conditional-access-configure.md).

> [!IMPORTANT]
> Hantering av databaser och servrar i Azure styrs av ditt användar kontos roll tilldelningar för portalen. Mer information om den här artikeln finns i [rollbaserad åtkomst kontroll i Azure Portal](../../role-based-access-control/overview.md). Kontroll av åtkomst med brand Väggs regler gäller *inte* för **en SQL-hanterad instans**. I följande artikel om hur [du ansluter till en hanterad instans](../managed-instance/connect-application-instance.md) finns mer information om nätverks konfigurationen som behövs.

## <a name="authorization"></a>Auktorisering

Auktorisering syftar på de behörigheter som tilldelats till en användare i en Azure SQL Database eller SQL-hanterad instans och avgör vad användaren får göra. Behörigheter styrs genom att lägga till användar konton till [databas roller](/sql/relational-databases/security/authentication-access/database-level-roles) och tilldela behörigheter på databas nivå till dessa roller eller genom att bevilja användaren vissa [behörigheter på objekt nivå](/sql/relational-databases/security/permissions-database-engine). Mer information finns i [inloggningar och användare](logins-create-manage.md)

Vi rekommenderar att du skapar anpassade roller vid behov. Lägg till användare i rollen med de minsta privilegier som krävs för att utföra jobb funktionen. Tilldela inte behörigheter direkt till användare. Server administratörs kontot är medlem i den inbyggda db_owners rollen, som har omfattande behörigheter och bör bara beviljas till några få användare med administrativa uppgifter. För program använder du [Kör som](/sql/t-sql/statements/execute-as-clause-transact-sql) för att ange körnings kontexten för den anropade modulen eller använda [program roller](/sql/relational-databases/security/authentication-access/application-roles) med begränsad behörighet. Den här metoden säkerställer att det program som ansluter till databasen har den lägsta behörighet som krävs för programmet. Genom att följa dessa rekommendationer bör du också skilja på olika uppgifter.

### <a name="row-level-security"></a>Säkerhet på radnivå

Säkerhet på radnivå gör det möjligt för kunder att styra åtkomsten till rader i en databas tabell baserat på egenskaperna för användaren som kör en fråga (till exempel grupp medlemskap eller körnings kontext). Säkerhet på radnivå kan också användas för att implementera anpassade, märkta säkerhets begrepp. Mer information finns i [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security).

![Azure-Database-RLS. png](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Skydd mot hot

SQL Database och SQL-hanterad instans säkra kunddata genom att tillhandahålla funktioner för granskning och hot identifiering.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-granskning i Azure Monitor loggar och Event Hubs

SQL Database-och SQL-hanterad instans granskning spårar databas aktiviteter och hjälper till att upprätthålla efterlevnaden av säkerhets standarder genom att registrera databas händelser till en Gransknings logg i ett kundägda Azure Storage-konto. Med granskning kan användarna övervaka pågående databas aktiviteter, samt analysera och undersöka historiska aktiviteter för att identifiera potentiella hot eller misstänkt missbruk och säkerhets överträdelser. Mer information finns i kom igång med [SQL Database granskning](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Avancerat skydd analyserar dina loggar för att identifiera onormalt beteende och potentiellt skadliga försök att komma åt eller utnyttja databaser. Aviseringar skapas för misstänkta aktiviteter, till exempel SQL-inmatning, potentiell data intrånget och brutna force-attacker eller för avvikelser i åtkomst mönster för att fånga in behörighets eskaleringar och avslöjade autentiseringsuppgifter. Aviseringar visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/), där information om misstänkta aktiviteter tillhandahålls och rekommendationer för ytterligare undersökning ges tillsammans med åtgärder för att minimera hotet. Avancerat skydd kan aktive ras per server för ytterligare en avgift. Mer information finns i [Kom igång med SQL Database Avancerat skydd](threat-detection-configure.md).

![Azure-Database-TD. jpg](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Informations skydd och kryptering

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (kryptering under överföring)

SQL Database-och SQL-hanterad instans skydda kund information genom att kryptera data i rörelse med [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Database och SQL-hanterad instans upprätthåller kryptering (SSL/TLS) hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och servern oavsett inställningen för **kryptering** eller **TrustServerCertificate** i anslutnings strängen.

Som bästa praxis rekommenderar vi att du i anslutnings strängen som används av programmet anger en krypterad anslutning och _**inte**_ litar på Server certifikatet. Detta tvingar ditt program att verifiera Server certifikatet och förhindrar därför att ditt program är sårbart för människor i mellan typ attacker.

Till exempel när du använder ADO.NET-drivrutinen utförs detta via **kryptera = True** och **TrustServerCertificate = false**. Om du får anslutnings strängen från Azure Portal har den rätt inställningar.

> [!IMPORTANT]
> Observera att vissa driv rutiner som inte kommer från Microsoft kanske inte använder TLS som standard eller som förlitar sig på en äldre version av TLS (<1,2) för att fungera. I det här fallet tillåter servern fortfarande att du ansluter till din databas. Vi rekommenderar dock att du utvärderar säkerhets riskerna med att tillåta att driv rutiner och program ansluter till SQL Database, särskilt om du lagrar känsliga data.
>
> Mer information om TLS och anslutningar finns i [TLS-överväganden](connect-query-content-reference-guide.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent datakryptering (kryptering vid vila)

[Transparent datakryptering (TDE) för Azure SQL Database & SQL-hanterad instans](transparent-data-encryption-tde-overview.md) lägger till ett säkerhets lager som hjälper till att skydda data i vila från obehörig eller offline-åtkomst till RAW-filer eller säkerhets kopior. Vanliga scenarier är stöld av data Center eller osäkert omhändertagande av maskin vara eller medium, till exempel disk enheter och säkerhets kopierings band.TDE krypterar hela databasen med en AES-krypteringsalgoritm, som inte kräver att programutvecklare gör några ändringar i befintliga program.

I Azure krypteras alla nyligen skapade databaser som standard och databas krypterings nyckeln skyddas av ett inbyggt Server certifikat.  Certifikat underhåll och-rotation hanteras av tjänsten och kräver inga indata från användaren. Kunder som föredrar att ta kontroll över krypterings nycklarna kan hantera nycklarna i [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Nyckel hantering med Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) support för [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) gör det möjligt för kunder att bli ägare till nyckel hantering och rotation med hjälp av [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), molnbaserad hanterings system för externa nycklar i Azure. Om databasens åtkomst till nyckel valvet har återkallats kan en databas inte dekrypteras och läsas in i minnet. Azure Key Vault tillhandahåller en central nyckel hanterings plattform, använder tätt övervakade HSM: er (Hardware Security modules) och gör det möjligt att separera uppgifter mellan hantering av nycklar och data för att uppfylla kraven på säkerhetskompatibilitet.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (kryptering används)

![Azure-Database-AE. png](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) är en funktion som har utformats för att skydda känsliga data som lagras i vissa databas kolumner från åtkomst (till exempel kreditkorts nummer, nationella identifierings nummer eller data på ett _behov av att känna till_ grunden). Detta inkluderar databas administratörer eller andra privilegierade användare som har behörighet att komma åt databasen för att utföra hanterings uppgifter, men inte ha någon verksamhet som behöver komma åt specifika data i de krypterade kolumnerna. Data krypteras alltid, vilket innebär att krypterade data endast dekrypteras för bearbetning av klient program med åtkomst till krypterings nyckeln.  Krypterings nyckeln exponeras aldrig för SQL och kan lagras antingen i [Windows certifikat Arkiv](always-encrypted-certificate-store-configure.md) eller i [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

![Azure-Database-DDM. png](./media/security-overview/azure-database-ddm.png)

Dynamisk data maskning begränsar känslig data exponering genom att maskera den till icke-privilegierade användare. Dynamisk data maskning identifierar automatiskt potentiellt känsliga data i Azure SQL Database-och SQL-hanterad instans och ger rekommendationer som kan användas för att maskera dessa fält, med minimal påverkan på program skiktet. Det fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad. Mer information finns i [Kom igång med SQL Database och SQL-hanterad instans dynamisk data maskning](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Säkerhetshantering

### <a name="vulnerability-assessment"></a>Sårbarhetsbedömning

[Sårbarhets bedömning](sql-vulnerability-assessment.md) är ett enkelt sätt att konfigurera en tjänst som kan upptäcka, spåra och hjälpa till att åtgärda potentiella databas sårbarheter med målet att proaktivt förbättra övergripande databas säkerhet. Sårbarhets bedömning (VA) är en del av det avancerade data säkerhets erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Sårbarhets bedömning kan nås och hanteras via den centrala SQL-portalen för avancerad data säkerhet.

### <a name="data-discovery--classification"></a>Dataidentifiering och -klassificering

Data identifierings & klassificering (för närvarande i för hands version) innehåller avancerade funktioner som är inbyggda i Azure SQL Database och SQL-hanterad instans för identifiering, klassificering, märkning och skydd av känsliga data i dina databaser. Att upptäcka och klassificera dina ytterst känsliga data (företag/ekonomi, sjukvård, personliga data osv.) kan spela en pivot-roll i din organisations informations skydds datasekretesstandarder. Tjänsten kan fungera som infrastruktur inom följande områden:

- Olika säkerhets scenarier, till exempel övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data.
- Kontroll av åtkomst till och härdning av säkerheten för databaser som innehåller mycket känsliga data.
- Hjälp med att uppfylla standarder för datasekretess och efterlevnadsregler.

Mer information finns i [Kom igång med data identifiering & klassificering](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Efterlevnad

Förutom de ovanstående funktionerna och funktionerna som kan hjälpa ditt program att uppfylla olika säkerhets krav, kan Azure SQL Database också delta i vanliga revisioner och har certifierats mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

## <a name="next-steps"></a>Nästa steg

- En beskrivning av hur du använder inloggningar, användar konton, databas roller och behörigheter i SQL Database och SQL-hanterad instans finns i [Hantera inloggningar och användar konton](logins-create-manage.md).
- En diskussion om databas granskning finns i [granskning](../../azure-sql/database/auditing-overview.md).
- En diskussion om hot identifiering finns i [hot identifiering](threat-detection-configure.md).
