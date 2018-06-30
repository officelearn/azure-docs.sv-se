---
title: Hantera efter migreringen - Azure SQL Database | Microsoft Docs
description: Lär dig hur du hanterar din databas efter migrering till Azure SQL Database.
services: sql-database
author: joesackmsft
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: josack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: ab6a66821905901515258842176ce24e485a54e3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110620"
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Ny DBA i molnet – hantera en databas i Azure SQL Database

Flytta från den traditionella själva hanteras själva kontrollerad miljö till en PaaS-miljö kan det verka lite överväldigande först. Som en app-utvecklare eller DBA, skulle du vill veta grundfunktionerna i plattformen som hjälper dig att hålla ditt program som är tillgängliga, performant, säker och flexibel - alltid. Det syftet är att göra just den här artikeln. Artikeln kortfattat ordnar resurser och ger vägledning om hur du bäst använder de viktigaste funktionerna i SQL-databas att hantera och att programmet fungerar smidigt och uppnå bästa resultat i molnet. Vanliga målgruppen för den här artikeln är de som: 
- Utvärderar migrering av associerade program till Azure SQL DB – Modernizing dina program.
- Håller på att migrera sina program – pågående Migreringsscenario.
- Nyligen har slutförts migrering till Azure SQL DB – nya DBA i molnet.

Den här artikeln beskrivs några av de grundläggande egenskaperna för Azure SQL-databas som en plattform som du lätt kan utnyttja. De är följande:- 
- Företag affärskontinuitet och haveriberedskap återställning (BCDR)
- Säkerhet och efterlevnad
- Intelligent databasen övervakning och underhåll
- Dataförflyttning

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Företag affärskontinuitet och haveriberedskap återställning (BCDR)
Funktioner för företag affärskontinuitet och haveriberedskap återställning kan du fortsätta ditt företag som vanligt, vid en katastrof. Haveriet kan vara en nivå händelse för databasen (till exempel någon av misstag släpper en avgörande tabell) eller en nivå Datacenter-händelse (regionala allvarlig händelse, till exempel en tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hur jag för att skapa och hantera säkerhetskopior på SQL-databasen?
Du skapa inte säkerhetskopior på Azure SQL DB och det beror på att du inte behöver. SQL-databas säkerhetskopierar automatiskt databaser, så att du inte längre måste oroa schemaläggning, tar och hantera säkerhetskopior. Plattformen som tar en fullständig säkerhetskopiering varje vecka, differentiell säkerhetskopiering några timmars mellanrum och en logg säkerhetskopiering var femte minut så disaster recovery är effektivt och den minimal förlusten av data. Den första kompletta säkerhetskopian händer när du skapar en databas. Dessa säkerhetskopior är tillgängliga för dig för en viss period som kallas ”Kvarhållningsperioden” och varierar beroende på vilken prestandanivå du väljer.  SQL Database ger dig möjlighet att återställa till valfri punkt i tiden i det här kvarhållning period med [punkt i tiden återställning (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Prestandanivå|Kvarhållningsperioden i dagar|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Dessutom kan den [långsiktiga Kvarhållning (LTR)](sql-database-long-term-retention.md) funktionen kan du hålla till säkerhetskopiorna under en mycket längre specifikt för upp till 10 år och återställa data från dessa säkerhetskopior när som helst inom denna period. Dessutom sparas databassäkerhetskopiorna på en georeplikerad lagring så återhämtning från regionala allvarlig händelse. Du kan även återställa dessa säkerhetskopior i alla Azure-region vid någon tidpunkt inom kvarhållningsperioden. Se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hur jag för att garantera kontinuitet för företag vid en datacenter-nivå katastrofer och regionala allvarlig händelse?
Eftersom databassäkerhetskopiorna lagras på ett geo-replikerade underlagringssystem så att om regional och kan du återställa säkerhetskopian till en annan Azure-region. Detta kallas för geo-återställning. RPO (mål för återställningspunkt) för det här är vanligtvis < 1 timme och infoga (uppskattade tiden för återställning – några minuter att timmar).

För verksamhetskritiska databaser erbjuder Azure SQL DB, aktiv geo-replikering. Detta i stort sett händer är att en georeplikerad sekundär kopia av den ursprungliga databasen skapas i en annan region. Till exempel om databasen först finns i Azure västra regionen USA och du vill att regionala återhämtning. Du skapar en aktiv geo-replikering av databasen i västra USA att säga östra USA. När calamity träffar på västra USA, kan du gå över till östra USA. Konfigurera dem i en grupp med automatisk växling vid fel är ännu bättre eftersom detta innebär att databasen automatiskt flyttas över till sekundärt i östra USA vid en katastrof. Återställningspunktmålet för det här är < 5 sekunder och infoga < 30 sekunder.

Om en grupp med automatisk växling vid fel inte är konfigurerad, måste programmet att övervaka för en katastrofåterställning och påbörja en växling till sekundärt aktivt. Du kan skapa upp till 4 sådana aktiv geo-repliker i olika Azure-regioner. Det blir ännu bättre. Du kan också komma åt dessa sekundär aktiv geo-repliker för skrivskyddad åtkomst. Detta är mycket praktiskt att minska svarstiden för ett scenario med geo-distribuerade programmet. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hur min plan för katastrofåterställning ändras från lokal till SQL Database?
Sammanfattningsvis måste traditionella lokala SQL Server-installationen du aktivt hantera din tillgänglighet med hjälp av funktioner som redundanskluster databasspegling Transaktionsreplikering loggen leverans etc. och underhålla och hantera säkerhetskopior så Kontinuitet för företag. Med SQL Database hanterar plattformen dessa för dig, så att du kan fokusera på att utveckla och optimera din databasprogram och oroa dig inte om katastrofhantering av så mycket. Du kan ha säkerhetskopiering och katastrofåterställning återställningsplaner konfigurerats och arbeta med bara några klick på Azure-portalen (eller några kommandon med hjälp av PowerShell APIs). 

Mer information om återställning finns: [Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Säkerhet och efterlevnad
SQL-databas tar allvar säkerhet och sekretess. Säkerheten i SQL-databasen är tillgänglig på databasnivå och nivån plattform och är bäst att förstå när kategoriseras i flera lager. På varje nivå hämta att styra och ge optimala säkerhet för ditt program. Lagren är:
- Identitet & autentisering ([Windows/SQL-autentisering och autentisering för Azure Active Directory [AAD]](sql-database-control-access.md)).
- Övervakning av aktivitet ([granskning](sql-database-auditing.md) och [hotidentifiering](sql-database-threat-detection.md)).
- Skydda faktiska data ([Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) och [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Kontrollera åtkomst till känsliga och Privilegierade data ([säkerhet på radnivå](/sql/relational-databases/security/row-level-security) och [dynamisk Datamaskering](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) tillhandahåller centraliserad hantering över arbetsbelastningar som körs i Azure, lokalt och i andra moln. Du kan visa om grundläggande skydd för SQL-databas som [granskning](sql-database-auditing.md) och [Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) konfigureras på alla resurser och skapa principer baserat på dina egna behov.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Vilka autentiseringsmetoder erbjuds i SQL-databasen?
Det finns [två autentiseringsmetoder](sql-database-control-access.md#authentication) erbjuds i SQL-databas: 
- [Azure Active Directory-autentisering](sql-database-aad-authentication.md)
- SQL-autentisering. 

Traditionella windows-autentisering stöds inte. Azure Active Directory (AD) är en centraliserad tjänst för identitets- och hantering. Med den här ge du mycket enkelt en enkel inloggning åtkomst (SSO) till alla personer i din organisation. Det innebär att autentiseringsuppgifterna som är gemensamma för alla Azure-tjänster för enklare autentisering. Har stöd för AAD [MFA (Multi-Factor Authentication)](sql-database-ssms-mfa-authentication.md) och en med en [några klickningar](../active-directory/connect/active-directory-aadconnect-get-started-express.md) AAD kan integreras med Windows Server Active Directory. SQL-autentisering fungerar på exakt samma sätt som du har använt den tidigare. Du anger ett användarnamn/lösenord och du kan autentisera användare till en databas på en viss logisk server. Detta kan också SQL Database och SQL Data Warehouse att erbjuda Multi-Factor authentication och Gäst användarkonton i Azure AD-domän. Om du redan har ett Active Directory lokalt kan du federera katalogen med Azure Active Directory för att utöka din katalog till Azure.

|**Om du...**|**SQL-databas / SQL Data Warehouse**|
|---|---|
|Inte vill använda Azure Active Directory (AD) i Azure|Använd [SQL-autentisering](sql-database-security-overview.md)|
|Använda AD på SQLServer lokalt|[Federera AD med Azure AD](../active-directory/connect/active-directory-aadconnect.md), och använda Azure AD-autentisering. Med den här, kan du använda enkel inloggning.|
|Om du behöver använda multifaktorautentisering (MFA)|Kräva MFA som en princip via [villkorlig åtkomst för Microsoft](sql-database-conditional-access.md), och använda [Azure AD Universal autentisering med stöd för MFA](sql-database-ssms-mfa-authentication.md).|
|Har gästkonton från Microsoft-konton (live.com, outlook.com) eller andra domäner (gmail.com)|Använd [Azure AD Universal autentisering](sql-database-ssms-mfa-authentication.md) i databasen /-informationslager, som använder [Azure AD B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Loggas Windows med dina autentiseringsuppgifter för Azure AD från en federerad domän|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|Loggas Windows med autentiseringsuppgifter från en domän som inte är federerat med Azure|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|Tjänster för mellannivå som behöver ansluta till SQL Database eller SQL Data Warehouse|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hur jag begränsa eller Kontrollera nätverksanslutningen åtkomst till databasen?
Det finns flera metoder för din tillgång som du kan använda för att uppnå bästa möjliga anslutning organisation för ditt program. 
- Brandväggsregler
- Slutpunkter för virtuella nätverk
- Reserverade ip-adresser

#### <a name="firewall"></a>Brandvägg
En brandvägg förhindrar åtkomst till servern från en extern entitet genom att tillåta att endast specifika enheter åtkomst till din logiska server. Som standard tillåts alla anslutningar och databaser i den logiska servern inte, förutom anslutningar som kommer från andra Azure-tjänster. Du kan använda en brandväggsregel för att öppna åtkomst till servern endast för enheter (till exempel en utvecklare-dator) som du godkänner, genom att tillåta att datorns IP-adress via brandväggen. Du kan också ange ett intervall av IP-adresser som du vill ge åtkomst till den logiska servern. Utvecklare datorns IP-adresser i din organisation kan till exempel läggas på en gång genom att ange ett intervall på inställningssidan för brandväggen. 

Du kan skapa brandväggsregler på servernivå eller på databasnivå. Serverbrandväggsreglerna för nivån kan antingen skapas via portalen eller genom SSMS. För mer information om hur du ställer in en server och databas-nivå brandväggsregler, se: [skapa brandväggsregler i SQL-databas](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Tjänstslutpunkter
SQL-databasen är som standard konfigurerad att ”Tillåt alla Azure-tjänster” – vilket innebär att en virtuell dator i Azure kan försöka att ansluta till databasen. Dessa försök fortfarande behöver få autentiseras. Men om du inte vill att databasen ska vara tillgängliga för alla Azure IP-adresser, kan du inaktivera ”Tillåt alla Azure-tjänster”. Du kan också konfigurera [VNET Tjänsteslutpunkter](sql-database-vnet-service-endpoint-rule-overview.md).

Slutpunkter (SE) kan du exponera viktiga Azure-resurser endast till ditt eget virtuella privata nätverk i Azure. Då kan att du i stort sett offentlig åtkomst till resurser. Trafiken mellan det virtuella nätverket till Azure finns kvar i Azure stamnät nätverket. Utan att SE dig Tvingad tunneltrafik paketroutning. Det virtuella nätverket tvingar internet-trafik för din organisation och Azure Service-trafik att gå igenom samma flöde. Med Tjänsteslutpunkter kan du optimera detta sedan paket-flöde direkt från det virtuella nätverket till tjänsten i Azure stamnät nätverk.

![Slutpunkter för virtuella nätverk](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>Reserverade ip-adresser
Ett annat alternativ är att etablera [reserverade IP-adresser](../virtual-network/virtual-networks-reserved-public-ip.md) för virtuella datorer och godkända de specifika VM IP-adresser i servern brandväggsinställningar. Genom att tilldela reserverade IP-adresser kan spara du att uppdatera brandväggsreglerna ändrar IP-adresser.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Vilken port ansluter till SQL-databas på?

Port 1433. SQL-databas kommunicerar via den här porten. Du måste lägga till en utgående regel i brandväggsinställningarna för din organisation för att ansluta från ett företagsnätverk. Som en riktlinje bör undvika att exponera port 1433 utanför gränsen för Azure. Du kan köra SSMS i Azure med hjälp av [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Detta behöver du inte att öppna port 1433 utgående anslutningar, IP-Adressen är statisk, så att databasen kan vara öppen till endast RemoteApp och Multi Factor Authentication (MFA) stöds.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hur kan övervaka och reglera aktivitet på Min server och databas i SQL-databasen?
#### <a name="sql-database-auditing"></a>SQL Database Auditing
Du kan stänga av granskning spårar databashändelser med SQL-databas. [SQL Database Auditing](sql-database-auditing.md) registrerar databashändelser och skriver dem till en granskningsloggfil i Azure Storage-konto. Granskning är särskilt användbart om du vill få insyn i potentiella överträdelser av säkerhets- och upprätthålla regelefterlevnad osv. Det kan du definiera och konfigurera vissa typer av händelser som du tror att måste granskning och baserat på att du kan få förkonfigurerade rapporter och en instrumentpanel om du vill få en översikt över händelser som inträffar i databasen. Du kan använda dessa granskningsprinciper på databasen eller på servernivå. En guide om hur du aktiverar granskning för servern/databasen, se: [Aktivera SQL Database Auditing](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Hotidentifiering
Med [hotidentifiering](sql-database-threat-detection.md), du får möjlighet att vidta åtgärder säkerhets- eller överträdelser identifieras genom att granska mycket enkelt. Du behöver inte vara en säkerhets-och expert att adressera potentiella hot eller överträdelser i systemet. Hotidentifiering har även vissa inbyggda funktioner som SQL Injection identifiering. SQL Injection är ett försök att ändra eller skada data och ett vanligt sätt att attackera ett databasprogram i allmänhet. Hotidentifiering för SQL-databasen körs flera uppsättningar av algoritmer som identifierar potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasen åtkomstmönster (till exempel åtkomst från en ovanlig plats eller av en okänd huvudnamn). Säkerhet polis eller andra avsedda administratörer får ett e-postmeddelande om ett hot upptäcks på databasen. Varje meddelande innehåller information om misstänkt aktivitet och rekommendationer om hur du ytterligare undersöka och minska risken. Information om hur du aktiverar hotidentifiering finns: [aktivera Hotidentifiering för SQL-databasen](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hur kan jag skydda data i allmänhet på SQL-databasen?
Kryptering ger en stark mekanism för att skydda och skydda känsliga data från obehöriga användare. Krypterade data har ingen användning för inkräktaren utan krypteringsnyckel. Därför läggs ett extra skyddslager ovanpå de befintliga säkerhetsnivåer som skapats i SQL-databas. Det finns två aspekter att skydda dina data i SQL-databasen: 
- Dina data är i vila i data-och logg 
- Data som är relä. 

I SQL-databas som standard data i vila i data och loggfilen filerna på underlagringssystemet helt och alltid krypteras [Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Säkerhetskopiorna är också krypterade. Det finns inga ändringar som krävs på dina program på klientsidan som har åtkomst till dessa data med TDE. Kryptering och dekryptering inträffa transparent; Därför namnet. För att skydda känsliga data relä och i vila SQL-databasen innehåller en funktion som kallas [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE är en typ av kryptering på klientsidan som krypterar känsliga kolumner i databasen (så att de är i ciphertext databasadministratörer och obehöriga användare). Servern tar emot börja med krypterade data. Nyckeln för Always Encrypted lagras också på klientsidan, så att endast auktoriserade klienter kan dekryptera känsliga kolumner. Server och dataadministratörer kan inte se känsliga data eftersom krypteringsnycklarna lagras på klienten. AE krypterar känsliga kolumner i tabellen slutpunkt till slutpunkt, från obehörig klienter till den fysiska disken. AE stöder likheten jämförelser idag så DBAs kan fortsätta att fråga krypterade kolumner som en del av sin SQL-kommandon. Alltid krypterat kan användas med en mängd olika KeyStore-alternativ som [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows-certifikatarkivet och lokala maskinvarusäkerhetsmoduler.

|**Egenskaper**|**Always Encrypted**|**Transparent datakryptering**|
|---|---|---|
|**Kryptering span**|Slutpunkt till slutpunkt|Data i vila|
|**Databasservern kan komma åt känsliga data**|Nej|Ja, eftersom kryptering av vilande data|
|**Tillåtna T-SQL-funktioner**|Likhetsjämförelse|Alla T-SQL-yta är tillgänglig|
|**App-ändringar som krävs för att använda funktionen**|Minimalt|Minimala|
|**Kryptering granularitet**|Kolumnnivå|Databasnivå|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hur kan jag begränsa åtkomsten till känsliga data i databasen?
Varje program har lite vissa känsliga data i databasen som måste skyddas från att vara synliga för alla. Vissa personalen i organisationen behöver för att visa dessa data, men andra bör inte att kunna visa dessa data. Ett exempel är medarbetare lön. En chef behöver åtkomst till hans/hennes direktrapporter löneinformationen men har de enskilda gruppmedlemmarna får inte åtkomst till löneinformation med varandra. Ett annat scenario är data utvecklare som kan interagera med känsliga data under utveckling steg eller testning, till exempel personnummer av kunder. Den här informationen igen behöver inte exponeras för utvecklare. I sådana fall måste känsliga data antingen maskeras eller visas inte alls. SQL-databasen innehåller båda dessa metoder för att förhindra att obehöriga användare att kunna visa känsliga data:

[Dynamisk Datamaskering](sql-database-dynamic-data-masking-get-started.md) är en funktion för maskering av data som gör att du kan begränsa exponering av känsliga data genom att maskera till icke-privilegierade användare på programnivån. Du definierar en maskningsregel som kan skapa ett mönster för maskering (till exempel att endast visa sista fyra siffrorna i ett nationella ID SSN: XXX-XX-0000 och markera de flesta av den som Xs) och identifiera vilka användare som ska uteslutas från maskningsregel. Maskeringen sker på avbrott och olika maskeringsfunktioner är tillgängliga för olika datakategorier. Dynamisk datamaskning kan du automatiskt identifiera känsliga data i databasen och tillämpa maskering till den.

[Rad säkerhet på radnivå](/sql/relational-databases/security/row-level-security) gör att du kan styra åtkomsten på nivån raden. Vilket innebär att, är vissa rader i en databastabell baserat på användaren som kör frågan (grupp medlemskap eller köra sammanhang) dolda. Begränsningen åtkomst görs på databasnivå i stället för i en program-nivå, att förenkla app-logiken. Börja med att skapa ett filterpredikat, filtrera bort rader som inte exponeras och den säkerhet princip nästa definiera vem som har åtkomst till dessa rader. Slutligen slutanvändaren kör sina frågan och, beroende på användarens behörigheter antingen visa dessa begränsade rader eller inte kan se dem alls.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hur hanterar krypteringsnycklar i molnet?

Det finns alternativ för nyckelhantering för Always Encrypted (kryptering på klientsidan) och Transparent datakryptering (kryptering i vila). Vi rekommenderar att du regelbundet rotera krypteringsnycklar. Frekvensen rotation ska justeras med både din interna organisationen och krav på efterlevnad.

**Transparent Data kryptering (TDE)**: det finns en två-key-hierarki i TDE – data i varje användardatabas krypteras med en symmetrisk AES 256 databasen unika databaskrypteringsnyckel (DEK), som i sin tur är krypterad med en unik server asymmetriska RSA 2048 huvudnyckel. Huvudnyckeln kan vara hanterade antingen:
- Automatiskt av platform - SQL-databas.
- Eller genom att använda [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) som nyckelarkivet.

Som standard hanteras huvudnyckeln för Transparent datakryptering av tjänsten SQL-databas i informationssyfte. Om organisationen vill ha kontroll över huvudnyckeln, finns det ett alternativ för att använda Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) som nyckelarkivet. Med hjälp av Azure Key Vault förutsätter organisationen kontroll över viktiga etablering, rotation och behörigheten kontroller. [Rotation eller växla typ av en huvudnyckel TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) är snabb, eftersom det endast krypterar DEK. För organisationer med åtskillnad av roller mellan säkerhet och hantering av kan en säkerhets-administratör etablera nyckelmaterial för huvudnyckeln TDE i Azure Key Vault och förse databasadministratören för ett Azure Key Vault nyckelidentifierare kryptering i vila på en server. Key Vault är utformat så att Microsoft inte kan se eller extrahera alla krypteringsnycklar. Du kan också få en centraliserad hantering av nycklar för din organisation. 

**Always Encrypted**: det finns också en [två nyckel hierarkin](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) i Always Encrypted – en kolumn av känsliga data krypteras med en AES 256 kolumnkrypteringsnyckeln (CEK), som i sin tur är krypterad med kolumnhuvudnyckeln (CMK). Klientdrivrutinerna för Always Encrypted har inga begränsningar CMKs längd. Det krypterade värdet för CEK lagras i databasen och CMK lagras i en betrodd KeyStore, till exempel Windows certifikatarkiv, Azure Key Vault eller en maskinvarusäkerhetsmodul. 
- Både den [CEK och CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kan roteras. 
- CEK rotation är en storlek på data igen och kan vara tidskrävande beroende på storleken på tabellerna som innehåller krypterade kolumner. Därför är det klokt att planera CEK rotationer på lämpligt sätt. 
- CMK rotation, men stör inte databasprestanda och kan göras med åtskilda roller.
Följande diagram visar KeyStore-alternativen för huvudnycklar kolumn i Always Encrypted

![Alltid krypterad CMK lagra providers](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hur kan optimera och säkra trafiken mellan min organisation och SQL-databasen?
Nätverkstrafiken mellan din organisation och SQL-databasen skulle normalt hämta dirigeras via ett offentligt nätverk. Om du väljer att optimera den här sökvägen och göra den säkrare, kan du söka i Expressroute. Expressroute i stort sett kan du utöka företagets nätverk till Azure-plattformen över en privat anslutning. Då kan överskrider du inte det offentliga Internet. Du får också högre säkerhet, tillförlitlighet och routning optimering som översätts till lägre nätverksfördröjningar och mycket högre hastighet än vad du skulle normalt får gå via det offentliga internet. Om du planerar att överföra en betydande del av data mellan din organisation och Azure, kan med hjälp av Express Route ge kostnadsfördelar. Du kan välja mellan tre olika anslutningen modeller för anslutningen från din organisation till Azure: 
- [Molnet Exchange samplacering](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Alla-till-alla](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Expressroute kan du också burst upp till 2 x gränsen som du köper utan extra kostnad. Du kan också konfigurera mellan region anslutning med hjälp av expressroute. Om du vill se en lista över leverantörer för anslutning av ER, se: [Express Route-Partners och Peering platser](../expressroute/expressroute-locations.md). I följande artiklar beskriver Express Route i detalj:
- [Introduktion på Express Route](../expressroute/expressroute-introduction.md)
- [Förutsättningar](../expressroute/expressroute-prerequisites.md)
- [Arbetsflöden](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Är SQL-databas som är kompatibel med eventuella krav och som sätt med min egen organisation efterlevnad?
SQL-databasen är kompatibel med en mängd regelverk compliances. Om du vill visa den senaste uppsättningen compliances har uppfyllts, finns det [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/compliance/complianceofferings) och detaljgranska compliances som är viktiga för din organisation för att se om SQL-databas som ingår i kompatibla Azure-tjänster. Det är viktigt att Observera att även om SQL-databasen kan vara certifierat kompatibla tjänster, det hjälper till att kompatibiliteten för organisationens tjänst men garanterar inte att automatiskt den.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligent databasen övervakning och underhåll efter migreringen

När du har migrerat databasen till SQL-databas, du ska du övervaka din databas (till exempel kontrollera hur resursanvändningen är som eller DBCC kontrollerar) och utföra regelbundet underhåll (till exempel återskapa eller organisera om index statistik osv.). SQL-databasen är lyckligtvis Intelligent i den mening att den använder historiska trender och inspelade mått och statistik för att proaktivt hjälpa dig att övervaka och underhålla din databas så att programmet körs alltid optimalt. I vissa fall kan Azure SQL DB automatiskt utföra underhållsaktiviteter beroende på din konfiguration. Det finns tre aspekter att övervaka din databas i SQL-databas:
- Övervakning av programprestanda och optimering.
- Säkerhet optimering.
- Kostnad optimering.

**Övervakning av programprestanda och optimering**: med insikter i frågeprestanda, du kan hämta skräddarsydd rekommendationer för din arbetsbelastning i databasen så att dina program kan fortsätta att köras på en optimal nivå - alltid. Du kan också konfigurera den så att de här rekommendationerna gäller automatiskt och du behöver inte bry dig utför underhållsaktiviteter. Du kan implementera index-rekommendationer baserat på din arbetsbelastning automatiskt med Index Advisor - kallas automatisk justering. Rekommendationerna utvecklas som din arbetsbelastning programändringar ger dig de mest relevanta förslag. Du får också alternativet för att granska de här rekommendationerna och Använd dem önskar manuellt.  

**Säkerhet optimering**: SQL-databasen innehåller tillämplig säkerhetsrekommendationer som hjälper dig att skydda dina data och Hotidentifiering för att identifiera och undersöka misstänkt databasaktiviteter som kan utgöra en potentiell tråd till den databas. [SQL Vulnerability Assessment](sql-vulnerability-assessment.md) är en databas genomsöka och rapportera tjänst som gör att du kan övervaka säkerhetstillståndet hos dina databaser i större skala och identifiera säkerhetsrisker och driva från en säkerhetsbaslinje som du definierar. Efter varje skanning tillhandahålls en egen lista över tillämplig steg och reparation skript samt en rapport som kan användas för att hjälpa för att uppfylla efterlevnad.

Med Azure Security Center identifiera säkerhetsrekommendationerna i alla och använda dem med en enda klickning. 

**Kostnad optimering**: Azure SQL-plattformen analyserar användning historiken över databaser i en server för att utvärdera och rekommenderar kostnaden optimering du alternativ för. Den här analysen tar vanligtvis en tvåveckorsperiod att analysera och bygga upp rekommenderade åtgärder. Elastiska poolen är ett alternativ. Rekommendationen visas på portalen som en banderoll:

![rekommendationer för elastiska pooler](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

Du kan också visa denna analys under avsnittet ”Advisor”:

![rekommendationer för klassificering av elastisk pool](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hur övervakar jag prestanda och resursanvändningen i SQL-databas?

Du kan utnyttja intelligent insikter för att övervaka prestanda och finjustera därefter-plattformen i SQL-databas. Du kan övervaka prestanda och resursanvändningen i SQL-databas med hjälp av följande metoder:
- **Azure-portalen**: Azure-portalen visar en enskild databas resursutnyttjning genom att välja databasen och klicka på diagrammet i översiktsfönstret. Du kan ändra diagrammet så att flera mått, inklusive CPU-procent, DTU-procent, Data IO-procent, sessioner procent och procent av databasen.

   ![Övervakning av diagram](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![Övervaka diagram 2](./media/sql-database-manage-after-migration/chart.png)

Du kan också konfigurera aviseringar efter resurs från det här diagrammet. Dessa aviseringar kan du svara på resursen villkor med ett e-postmeddelande, skriva till en HTTPS/HTTP-slutpunkt eller utföra en åtgärd. Finns det [övervaka databasprestanda i SQL-databas](sql-database-single-database-monitor.md) detaljerade anvisningar.

- **Dynamiska hanteringsvyer**: du kan fråga efter den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamisk hanteringsvy att returnera resurs förbrukning tidigare från den senaste timmen och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) system katalogvyn att returnera historik för de senaste 14 dagarna.
- **Query Performance Insight**: [Query Performance Insight](sql-database-query-performance.md) kan du se en historik över de vanligaste frågorna för förbrukning av resursen och tidskrävande frågor för en viss databas. Du kan snabbt identifiera de vanligaste frågorna av resursutnyttjande, varaktighet och frekvens för körning. Du kan spåra frågor och identifiera regression. Den här funktionen kräver [Frågearkivet](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) ska vara aktiverad och aktiv för databasen.

   ![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Azure SQL analyser (förhandsgranskning) i logganalys**: [Azure logganalys](../log-analytics/log-analytics-azure-sql.md) kan du samla in och visualisera viktiga Azure SQL Azure-prestandamått, stöd för upp till 150 000 SQL-databaser och 5 000 SQL elastiska pooler per arbetsytan. Du kan använda den för att övervaka och ta emot meddelanden. Du kan övervaka SQL-databas och elastisk pool mått över flera Azure-prenumerationer och elastiska pooler och kan användas för att identifiera problem på varje nivå i en programstack.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jag meddela prestandaproblem: hur min SQL-databas felsökning metoder skiljer sig från SQL Server?
En betydande del av felsökningsmetoder du använder för att diagnostisera frågan och problem med databasprestanda förblir oförändrade. När alla samma SQL Server används motorn i molnet. Plattform - Azure SQL DB har emellertid inbyggda 'intelligence'. Det kan hjälpa dig att felsöka och diagnostisera prestandaproblem även enklare. Det kan också utföra en del av dessa åtgärder för din räkning och i vissa fall kan åtgärda dem - proaktivt automatiskt. 

Din strategi för felsökning av prestandaproblem med avsevärt kan dra nytta av med smarta funktioner, till exempel [frågan prestanda Insight(QPI)](sql-database-query-performance.md) och [Database Advisor](sql-database-advisor.md) tillsammans och så skillnaden i metoder skiljer sig att avseende – du inte längre behöver manuellt arbete och viktig information som kan hjälpa dig att felsöka problemet till hands. Plattformen gör det tunga arbetet. Ett exempel som är QPI. QPI, du öka detaljnivån ända till nivån fråga och titta på historiska trender och ta reda på när exakt frågan regressed. Database Advisor ger dig rekommendationer på saker som kan hjälpa dig att förbättra din övergripande prestanda i allmänhet som - index som saknas, släpper index, Parameterisera dina frågor osv. 

Med felsökning av prestanda är det viktigt att identifiera om det är bara program eller databasen säkerhetskopiera dem, som påverkar programmets prestanda. Ofta är prestandaproblemet Applikationsnivån. Det kan vara arkitekturen eller mönstret för dataåtkomst. Anta till exempel att du har en chatty program som är känsliga för nätverks-svarstid. I det här fallet drabbas av ditt program eftersom det skulle vara många kort förfrågningar fram och tillbaka (”chatty”) mellan programmet och servern och på ett överbelastat nätverk, dessa görs summera fast. Du kan använda för att förbättra prestanda i det här fallet [Batch frågor](sql-database-performance-guidance.md#batch-queries). Med hjälp av journaler hjälper dig att oerhört mycket eftersom nu dina begäranden bearbetas i en batch; Därför hjälper dig att minska tur och RETUR-svarstid och förbättra programmets prestanda. 

Dessutom, om du märker en försämring i den allmänna prestandan för din databas, kan du övervaka den [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) hantering av dynamiska vyer för att Förstå förbrukning av CPU, IO och minne. Prestandan påverkas kanske eftersom databasen har för lite resurser. Det kan bero på att du kan behöva ändra prestandanivå och servicenivå baserat på den växande och minska storleken på arbetsbelastning. 

En omfattande uppsättning rekommendationer för att finjustera prestandaproblem, se: [finjustera din databas](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Hur kontrollerar jag att jag använder rätt tjänstnivå och prestandanivå servicenivån?
SQL-databas har olika tjänstnivåerna Basic, Standard och Premium. Varje tjänstnivå du får en garanterad förutsägbar prestanda som är knutna till den servicenivån. Beroende på din arbetsbelastning kanske belastning för aktiviteten där din resursutnyttjande kan träffa tak på det nuvarande prestandanivå du befinner dig i. Det är bra att första start genom att utvärdera i sådana fall om en inställning kan hjälpa (till exempel att lägga till eller ändra ett index osv.). Överväg att övergå till en högre prestandanivå eller servicenivå om det fortfarande uppstår problem med gränsen. 

|**Servicenivå**|**Vanliga exempel på scenarier**|
|---|---|
|**Basic**|Program med ett fåtal användare och en databas som inte har höga krav på samtidighet, skalning och prestanda. |
|**Standard**|Program med en stor samtidighet, skalning och prestanda kraven tillsammans med låg till medelhög i/o-krav. |
|**Premium**|Program med många samtidiga användare, hög CPU-minne och hög i/o-krav. Hög samtidighet, högt genomflöde och svarstid känsliga program kan använda Premium-nivå. |
|||

Du kan övervaka din fråga och databasen resursförbrukning genom en av ovan nämnda olika sätt ”hur övervakar jag prestanda- och -användning i SQL-databas” för att göra att du är på rätt prestandanivå. Du bör upptäcker att dina frågor/databaser konsekvent kör varm på processorminne/etc. kan du skala upp till en högre prestandanivå. På samma sätt om du Observera att även under vissa tider verkar du inte använda resurser så mycket; Överväg att skala från nuvarande prestandanivå. 

Om du har ett mönster för SaaS-program eller en databas konsolidering scenario, Överväg att använda en elastisk Pool för kostnadsoptimering. Elastiska poolen är ett bra sätt att uppnå databasen konsolidering och kostnaden optimering. Du kan läsa mer om hur du hanterar flera databaser med elastisk Pool, se: [hantera pooler och databaser](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Hur ofta behöver köra databasen integritetskontroller databasen?
SQL-databasen använder vissa smart metoder som gör att den kan hantera vissa typer av skadade data automatiskt och utan dataförlust. Dessa tekniker är inbyggda i tjänsten och utnyttjas av tjänsten när behöver uppstår. Med jämna mellanrum testas säkerhetskopiering av databaser för tjänsten, genom att återställa dem och köra DBCC CHECKDB på den. Om det uppstår problem kan åtgärdas SQL Database proaktivt dem. [Automatisk reparation](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) utnyttjas för att åtgärda sidor som är skadad eller har problem med dataintegriteten. Sidorna databasen verifieras alltid med KONTROLLSUMMA standardinställningen som kontrollerar integriteten för sidan. SQL-databas proaktivt övervakar och granskar dataintegriteten för din databas och, om det uppstår fel löser dem med högst prioritet. Förutom dessa kan du också köras egna integritetskontroller på din kommer.  Mer information finns i [dataintegriteten i SQL-databas](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Flytt av data efter migreringen

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hur jag för att exportera och importera data som BACPAC filer från SQL-databas?

- **Exportera**: du kan exportera din Azure SQL-databas som en BACPAC-fil från Azure-portalen

   ![databasexport](./media/sql-database-export/database-export.png)

- **Importera**: du kan även importera data som en BACPAC-fil i databasen med hjälp av Azure portal.

   ![databasimport](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hur jag för att synkronisera data mellan SQL-databas och SQL Server?
Du har flera olika sätt att åstadkomma detta: 
- **[Datasynkronisering](sql-database-sync-data.md)**  – den här funktionen kan du synkronisera data båda riktningarna mellan flera lokala SQL Server-databaser och SQL-databas. För att synkronisera med lokal SQL Server-databaser, måste du installera och konfigurera synkronisering agent på en lokal dator och öppna utgående TCP-port 1433.
- **[Transaktionsreplikering](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – med Transaktionsreplikering kan du synkronisera dina data från lokalt till Azure SQL DB med lokal som utgivaren och Azure SQL DB som prenumeranten. För närvarande stöds endast den här installationen. Läs mer om hur du migrerar data från lokala till SQL Azure med minimal avbrottstid: [Använd Transaktionsreplikering](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [SQL-databas](sql-database-technical-overview.md).

