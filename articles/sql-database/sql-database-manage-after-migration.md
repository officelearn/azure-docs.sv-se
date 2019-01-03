---
title: Hantera efter migrering – Azure SQL Database | Microsoft Docs
description: Lär dig mer om att hantera din databas efter migrering till Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 7b40496d22ffed8096ac40efcb96ec55a8ba63ca
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652803"
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Ny DBA i molnet – hantera din databas i Azure SQL Database

Flytta från den traditionella självhantering, lokal kontrollerad miljö till en PaaS-miljö kan verka lite överväldigande först. Som apputvecklare eller DBA, skulle du vill veta de viktigaste funktionerna i plattformen som kan hjälpa dig att hålla ditt program som är tillgängliga, prestanda, säker och flexibel - alltid. Den här artikeln är att göra just. Artikeln kortfattat ordnar resurser och ger dig vägledning om hur du bäst använder de viktigaste funktionerna i SQL-databas för att hantera och att programmet effektivt och få bästa resultat i molnet. Vanliga målgruppen för den här artikeln är de som:

- Utvärderar migrering av associerade program till Azure SQL DB – modernisera dina program.
- Håller på att migrera associerade program – pågående Migreringsscenario.
- Nyligen har slutförts migreringen till Azure SQL DB – ny DBA i molnet.

Den här artikeln beskriver några av de grundläggande egenskaperna för Azure SQL DB som en plattform som du lätt kan använda. De är följande:

- Företag affärskontinuitet och haveriberedskap recovery (BCDR)
- Säkerhet och efterlevnad
- Intelligent database-övervakning och underhåll
- Dataförflyttning

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Företag affärskontinuitet och haveriberedskap recovery (BCDR)

Funktioner för Business affärskontinuitet och haveriberedskap återställning kan du fortsätta din verksamhet, som vanligt vid en katastrof. Katastrofen kan vara en databas på händelse (till exempel någon av misstag släpper en viktig tabell) eller en nivå Datacenter-händelse (regionala allvarliga händelser, till exempel en tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hur jag för att skapa och hantera säkerhetskopior på SQL Database

Du skapar inte säkerhetskopior i Azure SQL DB och det beror på att du inte behöver. SQL-databas säkerhetskopierar automatiskt databaser för dig, så att du inte längre måste bekymra dig om schemaläggning, tar och hantera säkerhetskopior. Plattformen tar en fullständig säkerhetskopiering varje vecka, differentiell säkerhetskopiering några timmars mellanrum och en logg säkerhetskopiering var femte minut så haveriberedskap är effektivt och den minimal förlusten av data. Den första fullständiga säkerhetskopieringen sker när du skapar en databas. Dessa säkerhetskopior är tillgängliga för dig för en viss tid som kallas ”Kvarhållningsperioden” och varierar beroende på tjänstnivå som du väljer. SQL-databas ger dig möjlighet att återställa till valfri punkt inom denna kvarhållning period med [peka i tiden Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Tjänstenivå|Kvarhållningsperiod i dagar|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Dessutom kan den [Long-Term Retention (LTR)](sql-database-long-term-retention.md) funktionen kan du hålla till säkerhetskopiorna under en mycket längre mer specifikt för upp till 10 år och återställa data från säkerhetskopiorna när som helst inom denna period. Dessutom ska säkerhetskopior av databasen sparas i geo-replikerade lagringsutrymmen för att säkerställa återhämtning från regionala allvarliga händelser. Du kan även återställa säkerhetskopiorna i valfri Azure-region vid någon tidpunkt inom kvarhållningsperioden. Se [översikt över affärskontinuitet](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hur undviker jag att affärskontinuiteten i händelse av ett haveri datacenter på eller regionala allvarliga händelser

Eftersom din databassäkerhetskopior lagras i geo-replikerade lagringsutrymmen för att se till att vid en regional katastrof kan återställa du säkerhetskopian till en annan Azure-region. Detta kallas för geo-återställning. RPO (mål för återställningspunkt) för det här är vanligtvis < 1 timme och ERT (uppskattad återställningstid – några minuter till timmar).

För verksamhetskritiska databaser erbjuder Azure SQL DB, aktiv geo-replikering. Vad detta i stort sett gör är att en geo-replikerad sekundär kopia av den ursprungliga databasen skapas i en annan region. Exempelvis om din databas är inledningsvis finns i Azure västra vill USA och du regionala katastrofresiliens. Du skapar en aktiv GEO-replikering replik av databasen i västra USA att säga östra USA. När calamity inträffar i västra USA, kan du växla över till regionen östra USA. Konfigurera dem i en grupp för automatisk redundans är ännu bättre eftersom detta garanterar att databasen automatiskt flyttas över till sekundärt i USA, östra vid en katastrof. Återställningspunktmålet för det här är < 5 sekunder och ERT < 30 sekunder.

Om en grupp med automatisk redundans inte är konfigurerad, måste programmet aktivt övervaka för en katastrof och påbörja en växling till sekundärt. Du kan skapa upp till 4 sådana active geo-repliker i olika Azure-regioner. Hämtar ännu bättre. Du kan också komma åt dessa sekundära aktiva geo-repliker för skrivskyddad åtkomst. Detta är mycket praktiskt att minska svarstiden för ett scenario med geo-distribuerad program.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hur min plan för katastrofåterställning ändras från en lokal plats till SQL-databas

Sammanfattningsvis ska den traditionella lokala SQL Server-installationen måste du aktivt hantera din tillgänglighet med hjälp av funktioner, till exempel redundanskluster, databasspegling, Transaktionsreplikering eller Loggöverföring och underhålla och hantera säkerhetskopior för att säkerställa Kontinuitet för företag. Med SQL Database hanterar plattformen dessa för dig, så att du kan fokusera på att utveckla och optimera dina databasprogram och oroa dig inte om katastrofhantering av så mycket. Du kan ha säkerhetskopiering och haveriberedskapsplanerna konfigurerats och arbeta med bara några klick på Azure portal (eller några kommandon med hjälp av PowerShell APIs).

Mer information om haveriberedskap finns: [Haveriberedskap för Azure SQL Db 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Säkerhet och efterlevnad

SQL Database tar allvar säkerhet och sekretess. Säkerheten i SQL-databas är tillgängligt på databasnivå och på nivån för plattformen och är bäst att förstå när indelade i flera lager. På varje nivå får du styra och tillhandahålla optimal säkerhet för ditt program. Lager är:

- Identitet och autentisering ([Windows/SQL-autentisering och autentisering för Azure Active Directory [AAD]](sql-database-control-access.md)).
- Övervaka aktivitet ([granskning](sql-database-auditing.md) och [hotidentifiering](sql-database-threat-detection.md)).
- Skydda faktiska data ([Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) och [Always Encrypted [oa]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Kontrollera åtkomsten till känsliga och Privilegierade data ([säkerhet på](/sql/relational-databases/security/row-level-security) och [dynamisk Datamaskning](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) erbjuder centraliserad säkerhetshantering i arbetsbelastningar som körs i Azure, lokalt och i andra moln. Du kan visa om grundläggande skydd för SQL-databas som [granskning](sql-database-auditing.md) och [Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) konfigureras på alla resurser och skapa principer baserat på dina egna behov.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Vilka autentiseringsmetoder för användaren som erbjuds i SQL-databas

Det finns [två autentiseringsmetoder](sql-database-control-access.md#authentication) erbjuds i SQL-databas:

- [Azure Active Directory-autentisering](sql-database-aad-authentication.md)
- SQL-autentisering

Traditionella windows-autentisering stöds inte. Azure Active Directory (AD) är en centraliserad tjänst för identitets- och åtkomsthantering. Med detta kan du mycket bekvämt tillhandahålla en enkel inloggning för åtkomst (SSO) till all personal i din organisation. Det innebär att autentiseringsuppgifterna som är gemensamma för alla Azure-tjänster för enklare autentisering. Har stöd för AAD [MFA (Multifaktorautentisering)](sql-database-ssms-mfa-authentication.md) och med en [få klick](../active-directory/hybrid/how-to-connect-install-express.md) AAD kan integreras med Windows Server Active Directory. SQL-autentisering fungerar på exakt samma sätt som du har använt den tidigare. Du anger ett användarnamn/lösenord och du kan autentisera användare till valfri databas på en viss logisk server. Detta kan också SQL Database och SQL Data Warehouse att erbjuda Multi-Factor authentication och Gäst användarkonton i en Azure AD-domän. Om du redan har en Active Directory lokalt kan du federera katalogen med Azure Active Directory för att utöka din katalog till Azure.

|**Om du...**|**SQL-databas / SQL Data Warehouse**|
|---|---|
|Inte vill använda Azure Active Directory (AD) i Azure|Använd [SQL-autentisering](sql-database-security-overview.md)|
|Använda AD på SQLServer lokalt|[Federera AD med Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md), och använda Azure AD-autentisering. Du kan använda enkel inloggning med detta.|
|Behovet av att upprätthålla Multi-Factor authentication (MFA)|Kräva MFA som en princip via [villkorlig åtkomst för Microsoft](sql-database-conditional-access.md), och använda [Azure AD universell autentisering med stöd för MFA](sql-database-ssms-mfa-authentication.md).|
|Har gästkonton från Microsoft-konton (live.com, outlook.com) eller andra domäner (gmail.com)|Använd [Azure AD universell autentisering](sql-database-ssms-mfa-authentication.md) i SQL Database/Data Warehouse, som använder [Azure AD B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Loggas Windows med hjälp av Azure AD-autentiseringsuppgifter från en federerad domän|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|Loggas Windows med autentiseringsuppgifter från en domän som inte är federerat med Azure|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|Har mellannivå-tjänster som behöver ansluta till SQL Database eller SQL Data Warehouse|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hur jag begränsa eller kontrollera anslutning åtkomst till min databas

Det finns flera metoder till ditt förfogande som du kan använda för att uppnå optimal anslutning organisation för ditt program.

- Brandväggsregler
- Tjänstslutpunkter i virtuella nätverk
- Reserverade ip-adresser

#### <a name="firewall"></a>Brandvägg

En brandvägg förhindrar åtkomst till din server från en extern entitet genom att tillåta endast specifika entiteter åtkomst till din logiska server. Som standard alla anslutningar och databaserna på den logiska servern är inte tillåtna, förutom anslutningar kommer från andra Azure-tjänster. Du kan använda en brandväggsregel för att öppna åtkomst till din server endast för enheter (till exempel en utvecklare-dator) som du godkänner, genom att tillåta att datorns IP-adress via brandväggen. Du kan också ange ett intervall av IP-adresser som du vill tillåta åtkomst till den logiska servern. Developer datorns IP-adresser i din organisation kan till exempel läggas på en gång genom att ange ett intervall i sidan med brandväggsinställningar.

Du kan skapa brandväggsregler på servernivå eller på databasnivå. Nivån brandväggsregler för servern kan antingen skapas med hjälp av Azure portal eller med SSMS. Mer information om hur du anger en server och databas på brandväggsregel finns: [Skapa brandväggsregler i SQL Database](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Tjänstslutpunkter

Som standard SQL-databasen är konfigurerad att ”Tillåt Azure-tjänster åtkomst till servern” – vilket innebär att alla virtuella datorer i Azure kan försöka ansluta till databasen. Dessa försök fortfarande behöver autentiserad. Men om du inte vill din databas för att vara tillgängliga för alla Azure-IP-adresser, kan du inaktivera ”Tillåt Azure-tjänster åtkomst till servern”. Du kan också konfigurera [VNet-tjänstslutpunkter](sql-database-vnet-service-endpoint-rule-overview.md).

Tjänsteslutpunkter (SE) kan du exponera dina kritiska Azure-resurser endast till ditt eget privata virtuella nätverk i Azure. Då kan att du i princip offentlig åtkomst till resurser. Trafiken mellan ditt virtuella nätverk till Azure ligger i Azure-stamnätverket. Utan att SE få du Tvingad tunneltrafik routningen av datapaket. Det virtuella nätverket tvingar Internettrafik till din organisation och Azure-tjänsttrafiken att gå via samma väg. Med tjänstslutpunkter kan optimera du detta eftersom paket flödet direkt från ditt virtuella nätverk till tjänsten på Azure-stamnätverket.

![VNet-tjänstslutpunkter](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Reserverade ip-adresser

Ett annat alternativ är att etablera [reserverade IP-adresser](../virtual-network/virtual-networks-reserved-public-ip.md) för virtuella datorer och godkänna de specifika VM-IP-adresser i servern brandväggsinställningar. Genom att tilldela reserverade IP-adresser kan spara du besväret med att uppdatera brandväggsreglerna ändrar IP-adresser.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Vilken port som ansluter jag till SQL-databas på

Port 1433. SQL Database kommunicerar via den här porten. Om du vill ansluta inifrån ett företagsnätverk, måste du lägga till en utgående regel i brandväggsinställningarna för din organisation. Undvik att exponera port 1433 utanför gränsen för Azure som en riktlinje. Du kan köra SSMS i Azure med hjälp av [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Det här behöver du inte att öppna utgående anslutningar till port 1433, IP-Adressen är statisk, så att databasen kan vara öppen för endast RemoteApp och Multi Factor Authentication (MFA) stöds.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hur kan jag övervaka och reglera aktivitet på Min server och databas i SQL Database

#### <a name="sql-database-auditing"></a>SQL-Databasgranskning

Du kan stänga på granskning för att spåra databashändelser med SQL-databas. [SQL Database Auditing](sql-database-auditing.md) registrerar databashändelser och skriver dem till en granskningsloggfil i ditt Azure Storage-konto. Granskning är särskilt användbart om du planerar att få insikter om potentiella överträdelser av säkerhet och principer, upprätthålla regelefterlevnad osv. Det kan du definiera och konfigurera särskilda kategorier av händelser som du tror behöver granskning och baserat på att du kan få förkonfigurerade rapporter och en instrumentpanel för att få en översikt över händelser som inträffar i din databas. Du kan tillämpa principerna granskning på databasnivå eller på servernivå. En guide för hur du aktiverar granskning för servern/databasen, se: [Aktivera SQL Database Auditing](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Hotidentifiering

Med [hotidentifiering](sql-database-threat-detection.md), får du möjlighet att agera på säkerhets- eller princip överträdelser som identifierats av granskning väldigt enkelt. Du behöver inte vara säkerhetsexpert att bemöta potentiella hot eller säkerhetsöverträdelser i systemet. Hotidentifiering har också vissa inbyggda funktioner som SQL Injection identifiering. SQL-inmatning är ett försök att ändra eller skada data och ett vanligt sätt för att angripa ett databasprogram i allmänhet. SQL Database Threat Detection körs flera uppsättningar av algoritmer för att identifierar potentiella säkerhetsrisker och SQL-inmatningsattacker samt avvikande mönster i databasåtkomst (t.ex åtkomst från en ovanlig plats eller av ett bekant huvudnamn). Security införlivande eller andra avsedda administratörer får du ett e-postmeddelande om ett hot har identifierats på databasen. Varje avisering innehåller information om misstänkt aktivitet och rekommendationer om hur du ytterligare undersöka och åtgärda hot. Information om hur du aktiverar identifiering av hot finns: [Aktivera SQL Database Threat Detection](sql-database-security-tutorial.md#enable-sql-database-threat-detection).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hur kan jag skydda Mina data i allmänhet på SQL Database

Kryptering ger en stark mekanism för att skydda och säkra dina känsliga data från inkräktare. Krypterade data är någon användning för inkräktaren utan krypteringsnyckel. Därför det lägger till ett extra lager av säkerhet ovanpå de befintliga inbyggda säkerhetsnivåer i SQL-databas. Det finns två aspekter att skydda dina data i SQL-databas:

- Dina data som är i vila i data-och log
- Dina data som är pågående

I SQL-databas som standard krypteras dina data i vila i data och loggfiler lagras på underlagringssystemet helt och alltid [Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Säkerhetskopiorna krypteras också. Det finns inga ändringar som krävs på dina program på klientsidan som har åtkomst till dessa data med transparent Datakryptering. Kryptering och dekryptering ske transparent; Därför namnet.
För att skydda känsliga data pågående och i vila, SQL-databasen innehåller en funktion som kallas [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE är en typ av kryptering på klientsidan som krypterar känsliga kolumner i databasen (så att de är i chiffertext databasadministratörer och obehöriga användare). Servern tar emot att krypterade data. Nyckeln för Always Encrypted lagras också på klientsidan, så att endast auktoriserade klienter kan dekryptera känsliga kolumner. Server- och dataadministratörer kan inte se känsliga data eftersom krypteringsnycklarna som lagras på klienten. AE krypterar känsliga kolumner i tabellen från slutpunkt till slutpunkt, från obehöriga klienter till den fysiska disken. AE stöder likhet jämförelser idag, så att databasadministratörer kan fortsätta att fråga krypterade kolumner som en del av sin SQL-kommandon. Alltid krypterad kan användas med en mängd nyckelarkivet alternativ, till exempel [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows-certifikatarkivet och lokala HSM.

|**Egenskaper**|**Alltid krypterad**|**Transparent datakryptering**|
|---|---|---|
|**Kryptering span**|Slutpunkt till slutpunkt|Vilande data|
|**Databasservern kan komma åt känsliga data**|Nej|Ja, eftersom kryptering av vilande data|
|**Tillåtna T-SQL-åtgärder**|Likhetsjämförelse|Alla T-SQL-ytan är tillgänglig|
|**Appändringar som krävs för att använda funktionen**|Minimalt|Minimal|
|**Kryptering kornighet**|Kolumnnivå|Databasnivå|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hur kan jag begränsa åtkomsten till känsliga data i databasen

Varje program har en viss del känsliga data i databasen som måste skyddas från att vara synlig för alla. Vissa personal i organisationen behöver för att visa dessa data, men andra bör inte att kunna visa dessa data. Ett exempel är medarbetares löner. En chef behöver åtkomst till löneinformation för hans/hennes direktrapporter men, de enskilda gruppmedlemmarna inte ska ha åtkomst till löneinformation av de. Ett annat scenario är data utvecklare som kan interagera med känsliga data under stegen för utveckling eller testning, till exempel personnummer kunder. Den här informationen igen behöver inte exponeras för utvecklare. I sådana fall kan måste känsliga data antingen vara dold eller inte exponeras alls. SQL Database erbjuder två sådana metoder för att förhindra att obehöriga användare från att kunna visa känsliga data:

[Dynamisk Datamaskning](sql-database-dynamic-data-masking-get-started.md) är en data-maskning-funktion som låter dig begränsa exponering av känsliga data genom att maskera den för icke-privilegierade användare på programnivån. Du definierar en maskningsregel som kan skapa ett maskning-mönster (till exempel de sista fyra siffrorna i en nationell ID-SSN att endast visa: XXX-xx-0000 och markera de flesta av den som Xs) och identifiera vilka användare som ska uteslutas från maskering regeln. Maskeringen händer på direkt och det finns olika maskeringsfunktioner för olika datakategorier. Dynamisk datamaskning kan du identifiera känsliga data i databasen och tillämpa maskning på den automatiskt.

[Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) gör att du kan styra åtkomsten på radnivå. Vilket innebär att, är vissa rader i en databastabell baserat på användaren som kör fråga (grupp grupmedlemskap eller körningskontext kontext) dolda. Åtkomstbegränsning görs på databasnivån i stället för i en programnivå för att förenkla din applogik. Börja med att skapa ett filterpredikat, filtrerar ut rader som inte exponeras och den security policy nästa definiera vem som har åtkomst till dessa rader. Slutligen slutanvändaren kör sina fråga och, beroende på användarens behörigheter de visa dessa begränsade rader eller inte kan se dem alls.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hur gör jag för att hantera krypteringsnycklar i molnet

Det finns alternativ för nyckelhantering för både Always Encrypted (client side encryption) och Transparent datakryptering (kryptering i vila). Vi rekommenderar att du regelbundet rotera krypteringsnycklar. Rotation frekvensen ska justeras med både din interna organisationen och efterlevnadskrav.

#### <a name="transparent-data-encryption-tde"></a>Transparent datakryptering (TDE)

Det finns en två-key-hierarki i TDE – data i varje användardatabas krypteras med en symmetrisk AES-256 databasen unika databaskrypteringsnyckel (DEK), som i sin tur är krypterat med en server unika asymmetrisk RSA 2048 huvudnyckeln. Huvudnyckeln kan vara hanterade antingen:

- Automatiskt av plattformen, SQL-databas.
- Eller genom att använda [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) som nyckelarkivet.

Som standard hanteras huvudnyckeln för Transparent datakryptering av tjänsten SQL Database i informationssyfte. Om din organisation vill ha kontroll över huvudnyckeln, finns det ett alternativ för att använda Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) som nyckelarkivet. Förutsätter att kontroll över viktiga etablering, rotering och behörigheten kontroller med hjälp av Azure Key Vault kan din organisation. [Rotation eller växla typ av en huvudnyckel för TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) är snabbt, eftersom det endast återkrypterar DEK. För organisationer med åtskillnad av roller mellan säkerhet och hantering av kan en säkerhetsadministratör etablera nyckelmaterial för TDE huvudnyckeln i Azure Key Vault och förse databasadministratören för en Azure Key Vault nyckelidentifierare kryptering i vila på en server. Key Vault är utformat så att Microsoft varken se eller extrahera krypteringsnycklar. Du kan också få en central hantering av nycklar för din organisation.

#### <a name="always-encrypted"></a>Alltid krypterad

Det finns också en [två nycklar hierarkin](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) vid Always Encrypted – en kolumn med känsliga data krypteras med en AES 256-kolumnen krypteringsnyckel (CEK), som i sin tur krypteras med huvudnyckeln för en kolumn (CMK). Klientdrivrutiner som angetts för Always Encrypted har inga begränsningar för längden på CMKs. Det krypterade värdet för CEK lagras i databasen och CMK lagras i en betrodd nyckel lager, till exempel Windows Certificate Store, Azure Key Vault eller en maskinvarusäkerhetsmodul.

- Både den [CEK och CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kan roteras.
- CEK rotation är en storlek på data igen och kan vara tidskrävande beroende på storleken på tabellerna som innehåller krypterade kolumner. Därför är det klokt att planera CEK byta på lämpligt sätt.
- CMK rotation, men stör inte databasprestanda och kan göras med avgränsas roller.

Följande diagram visar nyckelarkivet alternativen för kolumnen huvudnycklar i Always Encrypted

![Alltid krypterad CMK lagra providers](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hur kan jag för att optimera och skydda trafiken mellan min organisation och SQL-databas

Nätverkstrafiken mellan din organisation och SQL-databas skulle vanligtvis dirigeras via det offentliga nätverket. Om du väljer att optimera den här sökvägen och blir säkrare, kan du titta på Express Route. Expressroute i stort sett kan du utöka företagets nätverk till Azure-plattformen via en privat anslutning. Då kan överskrider du inte det offentliga Internet. Du får också högre säkerhet, tillförlitlighet och routning optimering som översätts till färre nätverksfördröjningar och mycket snabbare hastigheter än du normalt skulle uppleva går via det offentliga internet. Om du planerar att överföra en betydande del av data mellan din organisation och Azure, kan med hjälp av Express Route ge kostnadsfördelar. Du kan välja mellan tre olika anslutningsmodeller för anslutningen från din organisation till Azure:

- [Samplacering av molnet Exchange](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Alla-till-alla](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Expressroute kan du utöka upp till 2 x Bandbreddsgräns som du köper för utan extra kostnad. Det är också möjligt att konfigurera mellan region-anslutning med expressroute. Om du vill se en lista över anslutningsleverantörer som ER, se: [Express Route-Partners och Peeringplatser](../expressroute/expressroute-locations.md). I följande artiklar beskriver Express Route i detalj:

- [Introduktion till Expressroute](../expressroute/expressroute-introduction.md)
- [Förutsättningar](../expressroute/expressroute-prerequisites.md)
- [Arbetsflöden](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Är SQL-databas som är kompatibel med alla efterlevnadskrav och vad som bidrar med min egen organisation efterlevnad

SQL Database är kompatibla med en mängd efterlevnadskraven. Om du vill visa den senaste uppsättningen efterlevnadskraven som har uppfyllts, Besök den [Microsoft Trust Center](https://microsoft.com/trustcenter/compliance/complianceofferings) och öka detaljnivån för de efterlevnadskraven som är viktiga för din organisation att se om SQL-databas ingår under kompatibla Azure-tjänster. Det är viktigt att Observera att även om SQL Database kan vara certifierat kompatibla tjänster, det hjälper till med kompatibiliteten för tjänsten för din organisation, men inte automatiskt garanterar den.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligent database-övervakning och underhåll efter migreringen

När du har migrerat din databas till SQL-databas måste du är ska du övervaka din databas (till exempel kontrollera hur resursanvändningen liknar eller DBCC kontrollerar) och utföra regelbundet underhåll (till exempel återskapa eller reorganize indexerar, statistik osv.). SQL Database är som tur är kan Intelligent i den mening att den använder historiska trender och inspelade mätvärden och statistik för att proaktivt hjälpa dig att övervaka och underhålla din databas, så att ditt program körs optimalt alltid. I vissa fall kan Azure SQL DB automatiskt utföra underhållsaktiviteter beroende på din konfiguration. Det finns tre aspekter för övervakning av din databas i SQL Database:

- Prestandaövervakning och optimering.
- Optimering av säkerhet.
- Kostnad optimering.

### <a name="performance-monitoring-and-optimization"></a>Övervaka prestanda och optimering

Med frågan Prestandainsikter får du skräddarsydda rekommendationer för din databas-arbetsbelastning så att dina program kan köra på optimal nivå – alltid. Du kan också konfigurera den så att de här rekommendationerna tillämpas automatiskt och du behöver inte bry dig utföra underhållsaktiviteter. Du kan automatiskt implementera indexrekommendationer baserat på din arbetsbelastning med Index Advisor – kallas automatisk justering. Rekommendationerna utvecklas som ditt program arbetsbelastningen ändras för att förse dig med de mest relevanta förslag. Du får också möjlighet att manuellt granska dessa rekommendationer och tillämpa dem själv.  

### <a name="security-optimization"></a>Optimering av säkerhet

SQL Database ger handlingsbara rekommendationer för att skydda dina data och Hotidentifiering för att identifiera och undersöka misstänkta databasaktiviteter som kan utgöra en potentiell tråd till databasen. [Sårbarhetsbedömning för SQL](sql-vulnerability-assessment.md) är en databas genomsökning och tjänsten där du kan övervaka säkerhetstillståndet för dina databaser i stor skala och identifiera säkerhetsrisker och avviker från en säkerhetsbaslinje som du definierar. Efter varje skanning tillhandahålls en anpassad lista med lämpliga åtgärder och skript samt en rapport som kan användas för att uppfylla efterlevnadskrav.

Med Azure Security Center kan du identifiera säkerhetsrekommendationerna på tavlan och koppla dem med ett enda klick.

### <a name="cost-optimization"></a>Kostnad optimering

Azure SQL-plattformen analyserar användning historiken över databaser på en server för att utvärdera och rekommenderar kostnadsoptimeringar alternativ för dig. Den här analysen tar vanligtvis en tvåveckorsperiod att analysera och bygga upp användbara rekommendationer. Elastisk pool är ett alternativ. Rekommendationen visas på portalen som en banderoll:

![elastiska pooler](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Du kan också visa den här analysen under avsnittet ”rådgivare”:

![elastisk pool rekommendationer-advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hur övervakar jag prestanda och resursanvändningen i SQL-databas

Du kan använda intelligenta insikter i plattformen för att övervaka prestanda och finjustera därefter i SQL-databas. Du kan övervaka prestanda och resursanvändningen i SQL-databas med hjälp av följande metoder:

#### <a name="azure-portal"></a>Azure Portal

Azure-portalen visar en enskild databas resursutnyttjning genom att välja databasen och klicka på diagrammet i översiktsfönstret i. Du kan ändra diagram om du vill visa flera mått, som CPU-procent, DTU-procent, Data-IO-procent, sessioner procent och databasstorlek i procent.

![Övervakningsdiagrammet](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Övervaka chart2](./media/sql-database-manage-after-migration/chart.png)

I det här diagrammet kan du också konfigurera aviseringar efter resurs. Dessa aviseringar kan du svara på resursen villkor med ett e-postmeddelande, skriva till en HTTPS/HTTP-slutpunkt eller utföra en åtgärd. Mer information finns i [skapa aviseringar](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Dynamiska hanteringsvyer

Du kan fråga den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamisk hanteringsvy att returnera resource förbrukning statistik historik från den senaste timmen och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) system katalogvy ska returneras Historik för de senaste 14 dagarna.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) kan du se en historik över de vanligaste resursintensiva frågor och tidskrävande frågor för en viss databas. Du kan snabbt identifiera de viktigaste frågorna av Resursanvändning, varaktighet och körningsfrekvensen. Du kan spåra frågor och identifiera regression. Den här funktionen kräver [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) är aktiverade och aktiv för databasen.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-log-analytics"></a>Azure SQL-analys (förhandsversion) i Log Analytics

[Azure Log Analytics](../azure-monitor/insights/azure-sql.md) kan du samla in och visualisera viktiga Azure SQL Azure-prestandamått, stöd för upp till 150 000 SQL-databaser och 5 000 elastiska SQL-pooler per arbetsyta. Du kan använda den för att övervaka och ta emot meddelanden. Du kan övervaka SQL-databas och elastisk pool mått över flera Azure-prenumerationer och elastiska pooler och kan användas för att identifiera problem på varje lager i en programstack.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jag märker prestandaproblem: Hur skiljer sig Mina felsökning metoder för SQL Database från SQL Server

En större del av felsökningsmetoder som du skulle använda för att diagnostisera fråga och prestandaproblem i databasen förblir desamma. När du har alla samma SQL Server Driver motor för molnet. Plattformen, Azure SQL DB har dock inbyggda 'intelligence ”. Det kan du felsöka och diagnostisera prestandaproblem ännu enklare. Det kan också utföra några av dessa åtgärder för din räkning och i vissa fall, åtgärda dem - proaktivt automatiskt.

Din strategi för felsökning av problem med prestanda avsevärt förbättra prestanda genom att använda intelligenta funktioner som [fråga prestanda Insight(QPI)](sql-database-query-performance.md) och [Database Advisor](sql-database-advisor.md) tillsammans och så skillnaden i metod skiljer sig att respekt – inte längre behöver du manuellt arbete i och ut de viktigaste uppgifterna som kan hjälpa dig att felsöka problemet till hands. Plattformen gör jobbet åt dig. Ett exempel på som är QPI. Med QPI, du öka detaljnivån hela vägen till nivån för frågan och tittar på historiska trender och ta reda på när exakt frågan försämrat. Database Advisor ger dig rekommendationer om saker som kan hjälpa dig förbättra dina övergripande prestanda i allmänhet så här: index som saknas, släpper index, Parameterisera frågorna osv.

Med prestandafelsökning av är det viktigt att identifiera om det är bara program eller säkerhetskopiera databasen, som påverkar programmets prestanda. Ofta ligger till prestandaproblemet i programlagret. Det kan vara arkitekturen eller dataåtkomstmönstret. Anta exempelvis att du har en trafikintensiva program som är känsliga för fördröjningar i nätverket. I det här fallet eftersom det skulle finnas många kort begäranden som kommer fram och tillbaka drabbas i ditt program (”trafikintensiva”) mellan programmet och servern och på ett överbelastat nätverk, dessa görs Lägg till snabbt. Du kan använda för att förbättra prestanda i det här fallet [Batchfrågekörning](sql-database-performance-guidance.md#batch-queries). Med hjälp av batchar hjälper dig att prestandaincident eftersom nu dina begäranden bearbetas i en batch; Därför hjälper dig att minska svarstiden i tur och RETUR och förbättra programmets prestanda.

Även om du märker en försämring av prestanda i din databas kan du övervaka den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamiska hanteringsvyn vyer för att Förstå förbrukning av CPU, IO och minne. Prestanda påverkas kanske eftersom databasen är har för få resurser. Det kan vara att du kan behöva ändra beräkningsstorleken och/eller tjänstnivån baserat på den växer och krymper arbetsbelastning.

En omfattande uppsättning med rekommendationer för justering prestandaproblem finns här: [Finjustera din databas](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hur undviker jag att jag använder rätt tjänstnivån och beräkna storleken

SQL Database erbjuder olika tjänstnivåerna Basic, Standard och Premium. Varje tjänstnivå du får en garanterad förutsebara prestanda som är kopplad till den tjänstnivån. Beroende på din arbetsbelastning kan du ha toppar där din användning av resurser kan orsaka taket för den aktuella compute-storleken som du tillhör. Det är bra att börja genom att utvärdera i sådana fall, om någon justering hjälper (till exempel att lägga till eller ändra ett index osv.). Om du fortfarande stöter på problem med gränsen kan du överväga att flytta till en högre tjänstnivå eller compute storlek.

|**Tjänstenivå**|**Vanliga användningsfall**|
|---|---|
|**Basic**|Program med ett fåtal användare och en databas som inte har höga krav för samtidighet, skala och prestanda. |
|**Standard**|Program med en betydande krav för samtidighet, skala och prestanda tillsammans med låg till medelhög i/o-kraven. |
|**Premium**|Program med många samtidiga användare, hög processor/minne och höga i/o-kraven. Hög samtidighet, högt dataflöde och svarstid känsliga appar kan utnyttja Premium-nivå. |
|||

För att se till att du använder rätt beräkningsstorleken, kan du övervaka din fråga och databasen resursförbrukning genom någon av ovan nämnda sätt i ”hur övervakar jag prestanda och Resursanvändning i SQL-databas”. Du bör märka att dina frågor/databaser konsekvent kör frekvent på processor/minne etc. kan du skala till en högre beräkningsstorleken. På samma sätt, om du Observera att även under din belastning du verkar inte använda resurserna så mycket; Överväg att skala från den aktuella beräkningsstorleken.

Om du har ett mönster för SaaS-app eller ett scenario för konsolidering av databasen kan du använda en elastisk pool för kostnadsoptimeringar. Elastisk pool är ett bra sätt att uppnå databasen konsoliderings- och kostnadsoptimeringar. Läsa mer om hur du hanterar flera databaser med hjälp av elastisk Pool, se: [Hantera pooler och databaser](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Hur ofta behöver jag kör databasen integritetskontroller min databas

SQL Database använder vissa smart tekniker för att den kan hantera vissa klasser av skadade data automatiskt och utan dataförlust. Dessa metoder är inbyggda i tjänsten och utnyttjas av tjänsten när behöver uppstår. Med jämna mellanrum testas din databassäkerhetskopior för tjänsten, genom att återställa dem och köra DBCC CHECKDB på den. Om det finns problem, hanterar SQL-databas proaktivt dem. [Automatisk reparation](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) utnyttjas för att åtgärda sidor som är skadad eller har problem med dataintegriteten. Sidorna databasen verifieras alltid med KONTROLLSUMMA standardinställningen som kontrollerar integriteten för sidan. SQL Database proaktivt övervakar och granskar dataintegriteten för din databas och, om det uppstår fel bemöta dem med högst prioritet. Förutom dessa kan du om du vill köra dina egna integritetskontroller när du din vill.  Mer information finns i [dataintegriteten i SQL-databas](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Dataförflyttning efter migreringen

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hur jag för att exportera och importera data som BACPAC-filer från SQL-databas

- **Exportera**: Du kan exportera din Azure SQL-databas som en BACPAC-fil från Azure portal

   ![databasexport](./media/sql-database-export/database-export.png)

- **Importera**: Du kan även importera data som en BACPAC-fil till databasen med hjälp av Azure portal.

   ![databasimport](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hur jag för att synkronisera data mellan SQL Database och SQL Server

Du har flera olika sätt att åstadkomma detta:

- **[Datasynkronisering](sql-database-sync-data.md)**  – den här funktionen kan du synkronisera data dubbelriktat mellan flera lokala SQL Server-databaser och SQL-databas. Om du vill synkronisera med en lokal SQL Server-databaser, måste du installera och konfigurera sync-agenten på en lokal dator och öppna utgående TCP-port 1433.
- **[Transaktionsreplikering](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – med replikering av transaktioner kan du synkronisera dina data från en lokal plats till Azure SQL DB med den lokala utgivaren och Azure SQL-databas som prenumeranten. För närvarande stöds endast den här installationen. Mer information om hur du migrerar dina data från en lokal plats till Azure SQL med minimal avbrottstid finns: [Använd Transaktionsreplikering](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [SQL-databas](sql-database-technical-overview.md).
