---
title: Säkerhetsöversikt för Azure SQL Database | Microsoft Docs
description: Läs mer om Azure SQL Database och SQL Server-säkerhet, inklusive skillnaderna mellan molnet och lokala SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 7c01e8132cc8fe8fcc01e468973a273dbfcfad64
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261062"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>En översikt över säkerhetsfunktionerna i Azure SQL Database

Den här artikeln beskriver grunderna för att skydda datanivån hos ett program med Azure SQL Database. Säkerhetsstrategi beskrivs följer det överlappande tillvägagångssättet för skydd på djupet enligt bilden nedan och flyttar utifrån i:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Nätverkssäkerhet

Microsoft Azure SQL Database är en relationsdatabas-tjänst för moln-och företagsprogram. För att skydda kunddata, förhindrar brandväggar nätverksåtkomst till databasservern tills åtkomst beviljas explicit baserat på IP-adress eller Azure Virtual network trafik ursprung.

### <a name="ip-firewall-rules"></a>IP-brandväggsregler

IP-brandväggsregler bevilja åtkomst till databaser baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i [översikt över Azure SQL Database och SQL Data Warehouse brandväggsregler](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuellt nätverk

[Tjänstslutpunkter i virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) utöka dina anslutningar för virtuella nätverk via Azures stamnät och aktivera Azure SQL Database för att identifiera virtuella nätverkets undernät som trafiken kommer från. Om du vill låta trafik nå Azure SQL Database använder SQL [tjänsttaggar](../virtual-network/security-overview.md) som tillåter utgående trafik via Nätverkssäkerhetsgrupper.

[Virtuella Nätverksregler](sql-database-vnet-service-endpoint-rule-overview.md) aktivera Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

> [!NOTE]
> Kontrollera åtkomst med brandväggsregler har *inte* avser **en hanterad instans**. Mer information om nätverkskonfigurationen behövs finns [ansluter till en hanterad instans](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Åtkomsthantering

> [!IMPORTANT]
> Hantera databaser och databasservrar inom Azure kontrolleras av din portalanvändarkontos rolltilldelningar. Mer information om den här artikeln finns [rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Autentisering är processen för att bevisa att användaren är som de påstår sig vara. Azure SQL Database stöder två typer av autentisering:

- **SQL-autentisering**:

    SQL-Databasautentisering refererar till autentisering av en användare när du ansluter till [Azure SQL Database](sql-database-technical-overview.md) med användarnamn och lösenord. En ”serveradministratörsinloggning” med ett användarnamn och lösenord måste anges när databasen server skapas för databasen. Med dessa autentiseringsuppgifter kan ”serveradministratör” autentisera till valfri databas på den databasservern som databasens ägare. Efter det kan ytterligare SQL-inloggningar och användare skapas av serveradministratören, som gör det möjligt för användarna att ansluta med användarnamn och lösenord.

- **Azure Active Directory-autentisering**:

    Azure Active Directory-autentisering är en mekanism för att ansluta till [Azure SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (AD Azure). Azure AD-autentisering kan administratörer centralt hantera identiteter och behörigheterna för databasanvändare tillsammans med andra Microsoft-tjänster på en central plats. Detta omfattar lagring av lösenord syftar till att minimera och möjliggör centraliserad lösenordsprinciper för rotation.

     En serveradministratör kallas den **Active Directory-administratör** måste skapas för att använda Azure AD-autentisering med SQL-databas. Mer information finns i [ansluter till SQL-databas med Azure Active Directory-autentisering](sql-database-aad-authentication.md). Azure AD-autentisering har stöd för både hanterade och externa konton. Externa konton stöder Windows-användare och grupper för en kund-domän som federeras med Azure AD.

    Ytterligare Azure AD authentication alternativen är [Active Directory Universal-autentisering för SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) anslutningar inklusive [Multifaktorautentisering](../active-directory/authentication/concept-mfa-howitworks.md) och [ Villkorlig åtkomst](sql-database-conditional-access.md).

> [!IMPORTANT]
> Hantera databaser och servrar inom Azure kontrolleras av din portalanvändarkontos rolltilldelningar. Mer information om den här artikeln finns [rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md). Kontrollera åtkomst med brandväggsregler har *inte* avser **en hanterad instans**. Finns i följande artikel om [ansluter till en hanterad instans](sql-database-managed-instance-connect-app.md) för mer information om nätverkskonfiguration som krävs.

Auktorisering hänvisar till de behörigheter som tilldelas till en användare i en Azure SQL Database och avgör vad användaren har tillåtelse att göra. Behörigheter kontrolleras genom att lägga till användarkonton till [databasroller](/sql/relational-databases/security/authentication-access/database-level-roles) som definierar databasbehörigheter och bevilja användaren vissa [objektnivå behörigheter](/sql/relational-databases/security/permissions-database-engine). Mer information finns i [inloggningar och användare](sql-database-manage-logins.md)

Ett bra tips är att lägga till användare till rollen med den minsta behörigheten som krävs för att utföra arbetsuppgifter. Serveradministratörskontot är medlem i rollen db_owner som har omfattande behörigheter och försiktighet ska beviljas till användare. Använd när du använder program med Azure SQL Database [programroller](/sql/relational-databases/security/authentication-access/application-roles) med begränsad behörighet. Detta säkerställer att programmet ansluter till databasen har de minsta behörigheten som krävs av programmet.

### <a name="row-level-security"></a>Säkerhet på radnivå

Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (till exempel gruppen grupmedlemskap eller körningskontext kontext). Mer information finns i [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  Den här autentiseringsmetoden använder ett användarnamn och lösenord. 

En översikt över behörigheter i Azure SQL Database finns i [inloggningar och användare](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>Hotskydd

SQL Database skyddar kundernas data genom att tillhandahålla granskning och funktioner.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-granskning i Azure Monitor-loggar och Event Hubs

SQL Database-granskning spårar databasaktiviteter och hjälper dig upprätthålla överensstämmelse med säkerhetsstandarder genom att registrera databashändelser till en granskningslogg i ett kundägda Azure storage-konto. Granskning kan du övervaka pågående databasaktiviteter, samt analysera och undersöka historiska aktiviteter för att identifiera potentiella hot eller misstänkt missbruk och säkerhetsöverträdelser. Mer information finns i Kom igång med [SQL Database Auditing](sql-database-auditing.md).  

### <a name="threat-detection"></a>Hotidentifiering

Threat detection förbättrar granskning genom att analysera granskningsloggarna för ovanlig funktion och potentiellt skadliga försöker komma åt eller utnyttja databaser. Aviseringar skapas för misstänkta aktiviteter eller avvikande åtkomstmönster som SQL-inmatningsattacker, potentiella data intrång och lösenord för råstyrkeattacker. Hotidentifieringsaviseringar som öppnas från den [Azure Security Center](https://azure.microsoft.com/services/security-center/), där finns information om misstänkta aktiviteter och rekommendationer för ytterligare undersökningar anges tillsammans med åtgärder för att minska risken. Hotidentifiering kostar $15/server/månad. Det är kostnadsfritt de första 60 dagarna. Mer information finns i [Kom igång med SQL Database Threat detection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Information protection och kryptering

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (kryptering under överföring)

SQL Database skyddar kundernas data genom att kryptera data i rörelse med [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server använder kryptering (SSL/TLS) på hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras ”under överföringen” mellan klienten och servern oavsett inställningen för **Encrypt** eller **TrustServerCertificate** i anslutningssträngen.

Som bästa praxis rekommenderar att i ditt programs anslutning sträng som du anger en krypterad anslutning och _**inte**_ lita på servercertifikatet. Detta gör att i ditt program för att verifiera servercertifikatet och innebär att ditt program från att vara sårbar för mannen i de mellersta typen av attackerna.

Till exempel när du använder ADO.NET-drivrutinen detta görs **Encrypt = True** och **TrustServerCertificate = False**. Om du har fått anslutningssträngen från Azure portal, har de korrekta inställningarna.

> [!IMPORTANT]
> Observera att vissa icke-Microsoft drivrutiner inte kan använda TLS som standard eller förlitar sig på en äldre version av TLS (< 2.0) för att kunna fungera. I det här fallet möjligt SQL Server fortfarande att ansluta till databasen. Vi rekommenderar dock att du utvärderar säkerhetsriskerna med att tillåta sådana drivrutiner och program för att ansluta till SQL-databas, särskilt om du lagrar känsliga data. 
>
> Mer information om TLS och anslutning finns [TLS-överväganden](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent datakryptering (kryptering i vila)

[Transparent datakryptering (TDE) för Azure SQL Database](transparent-data-encryption-azure-sql.md) lägger till ett lager säkerhet för att skydda data i vila från obehöriga eller offline-åtkomst till raw-filer eller säkerhetskopior. Vanliga scenarier är datacenter stöld eller oskyddat borttagning av maskinvara eller medier som diskenheter och band. Transparent Datakryptering krypteras hela databasen med en AES-krypteringsalgoritmen som inte kräver programutvecklare att göra ändringar i befintliga program.

Alla nyligen skapade SQL-databaser är krypterad som standard i Azure, och databaskrypteringsnyckeln skyddas av ett certifikat för inbyggda.  Certifikatet underhåll och rotation som hanteras av tjänsten och kräver inga indata från användaren. Kunder som föredrar att ta kontroll över krypteringsnycklarna som kan hantera nycklar i [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Nyckelhantering med Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) stöd för [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) gör att kunder kan bli ägare till nyckelhantering och rotation med [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), azures molnbaserade externa nyckelhanteringssystem. Databasåtkomst till nyckelvalvet har återkallats, en databas dekrypteras och läsa in i minnet. Azure Key Vault ger en central nyckelhantering plattform, utnyttjar nära övervakade maskinvarusäkerhetsmoduler (HSM) och aktiverar uppdelning av uppgifter mellan hantering av nycklar och data för att uppfylla krav på efterlevnad.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (kryptering vid användning)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine) är en funktion som utformats för att skydda känsliga data som lagras i specifika kolumner från åtkomst (till exempel kreditkortsnummer, nationella identifikationsnummer eller data på en _behöver veta_ dag). Detta inkluderar databasadministratörer eller andra Privilegierade användare som har behörighet att komma åt databasen för att utföra administrativa uppgifter, men inte finns några affärsmässiga som behöver åtkomst till viss data i de krypterade kolumnerna. Data krypteras alltid, vilket innebär att krypterade data dekrypteras endast för bearbetning av klientprogram med åtkomst till krypteringsnyckeln.  Krypteringsnyckeln exponeras aldrig till SQL och kan lagras antingen i den [Windows Certificate Store](sql-database-always-encrypted.md) eller i [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>Maskera

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

SQL Database dynamisk datamaskning begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk datamaskning automatiskt identifierar potentiellt känsliga data i Azure SQL Database och ger handlingsbara rekommendationer att maskera fälten, med minimal påverkan på programnivån. Det fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad. Mer information finns i [Kom igång med SQL Database dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Statisk datamaskning

[Statisk Datamaskning](/sql/relational-databases/security/static-data-masking) är ett verktyg för klientsidan som är tillgängliga i [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18,0 preview 5 och högre.  Statisk Datamaskning tillåter användare att skapa en kopia av en databas där data i de markerade kolumnerna har varit permanent maskeras. Maskning-funktionerna inkluderar NULL Maskning, enskilt värde maskning, shuffle och grupp shuffle maskera sträng sammansatta Maskning. Med den maskerade kopian av databasen kan organisationer separera produktions- och testmiljöer genom att dela den maskerade kopian. Känsliga data skyddas tillräckligt och alla andra egenskaper för databasen upprätthållits. Maskera databaser bör där från tredje part åtkomst till databaser krävs.

## <a name="security-management"></a>Säkerhetshantering

### <a name="vulnerability-assessment"></a>Sårbarhetsbedömning

[Sårbarhetsbedömning](sql-vulnerability-assessment.md) är ett enkelt sätt att konfigurera tjänsten som kan upptäcka, spåra och att åtgärda säkerhetsrisker i databasen med målet att proaktivt förbättra övergripande databassäkerhet. Sårbarhetsbedömning (VA) är en del av avancerade data security (ADS) erbjudandet som en enhetlig paket för avancerade säkerhetsfunktioner i SQL. Utvärdering av säkerhetsrisker kan nås och hanteras via den centrala SQL ADS-portalen.

### <a name="data-discovery--classification"></a>Dataidentifiering och -klassificering

Dataidentifiering och klassificering (för närvarande i förhandsversion) ger avancerade funktioner i Azure SQL Database för identifiering, klassificering, märkning och skydda känsliga data i dina databaser. Identifiera och klassificera största känsliga data (företag för finansiella, hälsovård, personliga data, osv.) kan spela upp en central roll i din organisations Information protection datasekretesstandarder. Det kan fungera som en infrastruktur för:

- Olika säkerhetsscenarier, till exempel övervakning (granskning) och Varna vid avvikande åtkomsten till känsliga data.
- Kontrollera åtkomst till och Härdning av säkerheten i,-databaser som innehåller mycket känsliga data.
- Hjälper uppfylla data sekretesstandarder och efterlevnadskrav.

Mer information finns i [Kom igång med dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Efterlevnad

Förutom ovanstående egenskaper och funktioner som kan hjälpa programmet att uppfylla olika säkerhetskrav, Azure SQL Database också granskas regelbundet och har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i den [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/) där du hittar den senaste listan med SQL Database-kompatibilitetscertifieringar.

## <a name="next-steps"></a>Nästa steg

- En beskrivning av åtkomstkontrollfunktionerna i SQL Database finns i [Kontrollera åtkomst](sql-database-control-access.md).
- En beskrivning av databasgranskning finns i [SQL Database-granskning](sql-database-auditing.md).
- En beskrivning av identifiering av hot finns i [SQL Database-hotidentifiering](sql-database-threat-detection.md).
