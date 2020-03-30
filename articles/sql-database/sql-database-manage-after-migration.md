---
title: Hantera enstaka och poolade databaser efter migreringen
description: Lär dig hur du hanterar databasen efter migreringen till Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: ebb512fee0186bed3cc7f49f0525dac43e57da3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256190"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Ny DBA i molnet – Hantera dina enstaka och poolade databaser i Azure SQL Database

Att gå från den traditionella självförvaltade, självstyrda miljön till en PaaS-miljö kan verka lite överväldigande i början. Som apputvecklare eller DBA vill du veta plattformens kärnfunktioner som hjälper dig att hålla ditt program tillgängligt, performant, säkert och motståndskraftigt - alltid. Denna artikel syftar till att göra just detta. Artikeln organiserar kortfattat resurser och ger dig lite vägledning om hur du bäst använder de viktigaste funktionerna i SQL Database med enstaka och poolade databaser för att hantera och hålla ditt program igång effektivt och uppnå optimala resultat i molnet. Typisk publik för den här artikeln skulle vara de som:

- Utvärderar migrering av sina program till Azure SQL Database – Modernisera dina program.
- Är i färd med att migrera sina program (s) - Pågående migrering scenario.
- Har nyligen slutfört migreringen till Azure SQL DB – New DBA i molnet.

I den här artikeln beskrivs några av de viktigaste egenskaperna hos Azure SQL Database som en plattform som du enkelt kan utnyttja när du arbetar med enskilda databaser och poolade databaser i elastiska pooler. De är följande:

- Övervaka databasen med Azure-portalen
- Affärskontinuitet och haveriberedskap (BCDR)
- Säkerhet och efterlevnad
- Intelligent databasövervakning och underhåll
- Dataförflyttning

> [!NOTE]
> Den här artikeln gäller följande distributionsalternativ i Azure SQL Database: enskilda databaser och elastiska pooler. Det gäller inte för distributionsalternativet för hanterade instanser i SQL Database.

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen

I [Azure-portalen](https://portal.azure.com/)kan du övervaka en enskild databasanvändning genom att välja databasen och klicka på **övervakningsdiagrammet.** Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

- CPU-procent
- DTU-procent
- Data IO-procent
- Databasstorlek i procent

När du har lagt till dessa mått kan du fortsätta att visa dem i **övervakningsdiagrammet** med mer information om **fönstret Mått.** Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Mer information om tjänstnivåer finns i [de DTU-baserade inköpsmodellartiklarna](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellartiklar.](sql-database-service-tiers-vcore.md)  

![Tjänstnivå-övervakning av databasprestanda.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Du kan också konfigurera aviseringar på prestandamåtten. Klicka på knappen **Lägg till avisering** i **mått**-fönstret. Följ guiden för att konfigurera aviseringen. Du har möjlighet att avisera om måttet överskrider ett visst tröskelvärde eller om måttet faller under ett visst tröskelvärde.

Om du exempelvis förväntar dig att arbetsbelastningen på din databas kommer att öka, kan du välja att konfigurera en e-postavisering när din databas kommer upp i 80 % för något av prestandamåtten. Du kan använda detta som en tidig varning för att ta reda på när du kanske måste växla till den näst högsta beräkningsstorleken.

Prestandamåtten kan också hjälpa dig att avgöra om du kan nedgradera till en lägre beräkningsstorlek. Anta att du använder en Standard S2-databas och alla prestandamått visar att databasen i snitt inte använder mer än 10 % vid något tillfälle. Det är då troligt att databasen skulle fungera bra i Standard S1. Tänk dock på arbetsbelastningar som ökar eller varierar innan du fattar beslutet att flytta till en lägre beräkningsstorlek.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Affärskontinuitet och haveriberedskap (BCDR)

Med kontinuitet och katastrofåterställning kan du fortsätta ditt företag, som vanligt, i händelse av en katastrof. Katastrofen kan vara en databasnivåhändelse (till exempel tappar någon av misstag en viktig tabell) eller en datacenternivåhändelse (regional katastrof, till exempel en tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hur skapar och hanterar jag säkerhetskopior i SQL Database

Du skapar inte säkerhetskopior på Azure SQL DB och det beror på att du inte behöver. SQL Database säkerhetskopierar automatiskt databaser åt dig, så du behöver inte längre oroa dig för schemaläggning, hantering och hantering av säkerhetskopior. Plattformen tar en fullständig säkerhetskopiering varje vecka, differentiell säkerhetskopiering med några timmars mellanrum och en loggsäkerhetskopiering var 5:e minut för att säkerställa att haveriberedskapen är effektiv och dataförlusten minimal. Den första fullständiga säkerhetskopian sker så fort du skapar en databas. Dessa säkerhetskopior är tillgängliga för dig under en viss period som kallas "Kvarhållningsperiod" och varierar beroende på vilken tjänstnivå du väljer. SQL Database ger dig möjlighet att återställa till någon tidpunkt inom denna kvarhållningsperiod med hjälp av [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Tjänstenivå|Kvarhållningsperiod i dagar|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Dessutom kan du med funktionen [för långsiktig kvarhållning (LTR)](sql-database-long-term-retention.md) hålla fast vid dina säkerhetskopior under en mycket längre period specifikt, i upp till 10 år, och återställa data från dessa säkerhetskopior när som helst inom den perioden. Dessutom sparas säkerhetskopieringarna av databasen i geore replikerad lagring för att säkerställa motståndskraft från regional katastrof. Du kan också återställa dessa säkerhetskopior i alla Azure-regioner när som helst inom kvarhållningsperioden. Se [Översikt över kontinuitet i verksamheten](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hur säkerställer jag kontinuitet i händelse av en katastrof på datacenternivå eller regional katastrof

Eftersom dina säkerhetskopieringar av databasen lagras i geore replikerad lagring för att säkerställa att du i händelse av en regional katastrof kan återställa säkerhetskopian till en annan Azure-region. Detta kallas geo-återställning. RPO (Recovery Point Mål) för detta är i allmänhet < 1 timme och ERT (Beräknad återhämtningstid - några minuter till timmar).

För verksamhetskritiska databaser erbjuder Azure SQL DB, aktiv geo-replikering. Vad detta i huvudsak gör är att det skapar en geo-replikerad sekundär kopia av den ursprungliga databasen i en annan region. Till exempel om din databas är ursprungligen värd i Azure West US-regionen och du vill ha regional katastrofresiliens. Du skulle skapa en aktiv geo replik av databasen i västra USA att säga Östra USA. När katastrofen slår mot västra USA, kan du växla över till östra USA-regionen. Konfigurera dem i en automatisk redundansgrupp är ännu bättre eftersom detta säkerställer att databasen automatiskt växlar över till den sekundära i östra USA i händelse av en katastrof. RPO för detta är < 5 sekunder och ERT < 30 sekunder.

Om en grupp för automatisk redundans inte är konfigurerad måste programmet aktivt övervaka en katastrof och initiera en redundans till den sekundära. Du kan skapa upp till 4 sådana aktiva georepliker i olika Azure-regioner. Det blir ännu bättre. Du kan också komma åt dessa sekundära aktiva georepliker för skrivskyddad åtkomst. Detta är mycket praktiskt för att minska svarstiden för ett geo-distribuerat programscenario.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hur ändras min katastrofåterställningsplan från lokalt till SQL-databas

Sammanfattningsvis krävde den traditionella lokala SQL Server-konfigurationen att du aktivt skulle hantera din tillgänglighet med hjälp av funktioner som redundanskluster, databasspegling, transaktionsreplikering eller loggfraktning och underhåller och hanterar säkerhetskopior för att säkerställa Affärskontinuitet. Med SQL Database hanterar plattformen dessa åt dig, så att du kan fokusera på att utveckla och optimera ditt databasprogram och inte oroa dig för katastrofhantering lika mycket. Du kan ha säkerhetskopierings- och haveriberedskapsplaner konfigurerade och arbetar med bara några klick på Azure-portalen (eller några kommandon med PowerShell-API:erna).

Mer information om haveriberedskap finns [i: Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Säkerhet och efterlevnad

SQL Database tar säkerhet och sekretess på största allvar. Säkerhet inom SQL Database är tillgänglig på databasnivå och på plattformsnivå och förstås bäst när den kategoriseras i flera lager. Vid varje lager får du styra och ge optimal säkerhet för ditt program. Lagren är:

- Identitet & autentisering ([SQL-autentisering och Azure Active Directory [AAD] autentisering](sql-database-manage-logins.md)).
- Övervakningsaktivitet ([Granskning](sql-database-auditing.md) och [hotdetektering).](sql-database-threat-detection.md)
- Skydda faktiska data[(Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) och [alltid krypterad [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Styra åtkomsten till känsliga och privilegierade data ([säkerhet på radnivå](/sql/relational-databases/security/row-level-security) och [dynamisk datamaskering](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) erbjuder centraliserad säkerhetshantering över arbetsbelastningar som körs i Azure, lokalt och i andra moln. Du kan visa om viktigt SQL Database-skydd, till exempel Granskning och [transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) är konfigurerade på alla resurser och skapar principer baserat på dina egna behov. [Auditing](sql-database-auditing.md)

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Vilka metoder för användarautentisering som erbjuds i SQL Database

Det finns två autentiseringsmetoder som erbjuds i SQL Database:

- [Azure Active Directory-autentisering](sql-database-aad-authentication.md)
- [SQL-autentisering](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Den traditionella windows-autentiseringen stöds inte. Azure Active Directory (AD) är en centraliserad identitets- och åtkomsthanteringstjänst. Med detta kan du mycket bekvämt ge en enkel inloggningsåtkomst (SSO) till all personal i din organisation. Vad detta innebär är att autentiseringsuppgifterna delas över alla Azure-tjänster för enklare autentisering. AAD stöder [MFA (Multi Factor Authentication)](sql-database-ssms-mfa-authentication.md) och med [några klick](../active-directory/hybrid/how-to-connect-install-express.md) kan AAD integreras med Active Directory i Windows Server. SQL-autentisering fungerar precis som du har använt den tidigare. Du anger ett användarnamn/lösenord och du kan autentisera användare till valfri databas på en viss SQL Database-server. Detta gör det också möjligt för SQL Database och SQL Data Warehouse att erbjuda multifaktorautentisering och gästanvändarkonton inom en Azure AD-domän. Om du redan har en lokal Active Directory kan du federera katalogen med Azure Active Directory för att utöka katalogen till Azure.

|**Om du...**|**SQL-databas / SQL-datalager**|
|---|---|
|Föredrar att inte använda Azure Active Directory (AD) i Azure|Använda [SQL-autentisering](sql-database-security-overview.md)|
|Används AD på lokal SQL Server|[Federera AD med Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)och använd Azure AD-autentisering. Med detta kan du använda Enkel inloggning.|
|Behov av att framtvinga MFA (Multi Factor Authentication)|Kräv MFA som en princip via [Microsoft Conditional Access](sql-database-conditional-access.md)och använd Azure AD [Universal-autentisering med MFA-stöd](sql-database-ssms-mfa-authentication.md).|
|Har gästkonton från Microsoft-konton (live.com, outlook.com) eller andra domäner (gmail.com)|Använd [Azure AD Universal-autentisering](sql-database-ssms-mfa-authentication.md) i SQL Database/Data Warehouse, som utnyttjar [Azure AD B2B Collaboration](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Loggas in i Windows med dina Azure AD-autentiseringsuppgifter från en federerad domän|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|Loggas in i Windows med autentiseringsuppgifter från en domän som inte federerats med Azure|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|Ha tjänster på mellannivå som måste ansluta till SQL Database eller SQL Data Warehouse|Använd [Azure AD-integrerad autentisering](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hur begränsar eller kontrollerar jag åtkomsten till anslutningen till databasen

Det finns flera tekniker till ditt förfogande som du kan använda för att uppnå optimal anslutningsorganisation för ditt program.

- Brandväggsregler
- Slutpunkter för VNet-tjänst
- Reserverade ip-adresser

#### <a name="firewall"></a>Brandvägg

En brandvägg förhindrar åtkomst till servern från en extern entitet genom att endast ge specifika entiteter åtkomst till SQL Database-servern. Som standard tillåts inte alla anslutningar och databaser i SQL Database-servern, förutom anslutningar som kommer in från andra Azure Services. Med en brandväggsregel kan du endast öppna åtkomsten till servern till entiteter (till exempel en utvecklardator) som du godkänner, genom att tillåta datorns IP-adress via brandväggen. Du kan också ange ett intervall med IP-adresser som du vill tillåta åtkomst till SQL Database-servern. Till exempel kan utvecklarmaskinens IP-adresser i organisationen läggas till samtidigt genom att ange ett intervall på sidan Brandväggsinställningar.

Du kan skapa brandväggsregler på servernivå eller på databasnivå. IP-brandväggsregler på servernivå kan antingen skapas med Hjälp av Azure-portalen eller med SSMS. Mer information om hur du anger en brandväggsregel på servernivå och brandvägg på databasnivå finns [i: Skapa IP-brandväggsregler i SQL Database](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Tjänstslutpunkter

Som standard är DIN SQL-databas konfigurerad för att "Tillåt Azure-tjänster att komma åt servern" – vilket innebär att alla virtuella datorer i Azure kan försöka ansluta till databasen. Dessa försök måste fortfarande autentiseras. Men om du inte vill att databasen ska vara tillgänglig för alla Azure-IPs kan du inaktivera "Tillåt Azure-tjänster att komma åt servern". Dessutom kan du konfigurera [slutpunkter för VNet-tjänst](sql-database-vnet-service-endpoint-rule-overview.md).

Med tjänstslutpunkter (SE) kan du bara exponera dina kritiska Azure-resurser för ditt eget privata virtuella nätverk i Azure. På så sätt eliminerar du i huvudsak allmänhetens tillgång till dina resurser. Trafiken mellan det virtuella nätverket till Azure finns kvar i Azure-stamnätet. Utan SE får du forcerad tunnelpaketdragning. Ditt virtuella nätverk tvingar internettrafiken till din organisation och Azure Service-trafiken att gå över samma väg. Med Tjänstslutpunkter kan du optimera detta eftersom paketen flödar direkt från ditt virtuella nätverk till tjänsten på Azure-stamnätet.

![VNet-tjänstslutpunkter](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Reserverade ip-adresser

Ett annat alternativ är att etablera [reserverade IP-adresser](../virtual-network/virtual-networks-reserved-public-ip.md) för dina virtuella datorer och lägga till dessa specifika VM-IP-adresser i serverbrandväggsinställningarna. Genom att tilldela reserverade IP-adresser sparar du problemet med att behöva uppdatera brandväggsreglerna med ändrade IP-adresser.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Vilken port ansluter jag till SQL Database på

Hamn 1433. SQL Database kommunicerar över den här porten. Om du vill ansluta från ett företagsnätverk måste du lägga till en utgående regel i organisationens brandväggsinställningar. Som en riktlinje bör du undvika att exponera port 1433 utanför Azure-gränsen.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hur kan jag övervaka och reglera aktivitet på min server och databas i SQL Database

#### <a name="sql-database-auditing"></a>SQL Database Auditing

Med SQL Database kan du aktivera granskning för att spåra databashändelser. [SQL Database Auditing](sql-database-auditing.md) registrerar databashändelser och skriver dem i en granskningsloggfil i ditt Azure Storage-konto. Granskning är särskilt användbart om du tänker få insikt i potentiella säkerhets- och policyöverträdelser, upprätthålla regelefterlevnad etc. Det gör att du kan definiera och konfigurera vissa kategorier av händelser som du tror behöver granskas och baserat på att du kan få förkonfigurerade rapporter och en instrumentpanel för att få en översikt över händelser som inträffar i databasen. Du kan använda dessa granskningsprinciper antingen på databasnivå eller på servernivå. En guide om hur du aktiverar granskning för servern/databasen finns i: [Aktivera SQL Database Auditing](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Hotidentifiering

Med [hotidentifiering](sql-database-threat-detection.md)får du möjlighet att agera på säkerhets- eller policyöverträdelser som upptäcks av Granskning mycket enkelt. Du behöver inte vara säkerhetsexpert för att hantera potentiella hot eller överträdelser i systemet. Hotidentifiering har också några inbyggda funktioner som SQL Injection detection. SQL Injection är ett försök att ändra eller äventyra data och ett ganska vanligt sätt att attackera ett databasprogram i allmänhet. Hotidentifiering kör flera uppsättningar algoritmer som identifierar potentiella sårbarheter och SQL-injektionsattacker, samt avvikande databasåtkomstmönster (till exempel åtkomst från en ovanlig plats eller av ett okänt huvudnamn). Säkerhetstjänstemän eller andra utsedda administratörer får ett e-postmeddelande om ett hot upptäcks i databasen. Varje meddelande innehåller information om den misstänkta aktiviteten och rekommendationer om hur du ytterligare undersöker och minskar hotet. Mer information om hur du aktiverar identifiering av hot finns i: [Aktivera hotidentifiering](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hur skyddar jag mina data i allmänhet på SQL Database

Kryptering ger en stark mekanism för att skydda och skydda dina känsliga data från inkräktare. Dina krypterade data är till någon nytta för inkräktaren utan dekrypteringsnyckeln. Således lägger det till ett extra lager av skydd ovanpå de befintliga lagren av säkerhet som byggts i SQL Database. Det finns två aspekter för att skydda dina data i SQL Database:

- Dina data som vilar i data- och loggfilerna
- Dina data som är under flygning

I SQL Database, som standard, dina data i vila i data och loggfiler på lagring delsystemet är helt och alltid krypterad via [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Dina säkerhetskopior är också krypterade. Med TDE krävs inga ändringar på programsidan som använder dessa data. Krypteringen och dekrypteringen sker öppet. därav namnet.
För att skydda känsliga data under flygning och i vila tillhandahåller SQL Database en funktion som kallas [Alltid krypterad (AE).](/sql/relational-databases/security/encryption/always-encrypted-database-engine) AE är en form av kryptering på klientsidan som krypterar känsliga kolumner i databasen (så att de är i chiffertext till databasadministratörer och obehöriga användare). Servern tar emot krypterade data till att börja med. Nyckeln för Alltid krypterad lagras också på klientsidan, så endast auktoriserade klienter kan dekryptera känsliga kolumner. Servern och dataadministratörerna kan inte se känsliga data eftersom krypteringsnycklarna lagras på klienten. AE krypterar känsliga kolumner i tabellen från ände till, från obehöriga klienter till den fysiska disken. AE stöder likhetsjämlikhetsjämlikhetsjämlikhet idag, så att DBAs kan fortsätta att fråga krypterade kolumner som en del av sina SQL-kommandon. Alltid krypterad kan användas med en mängd olika viktiga butiksalternativ, till exempel [Azure Key Vault,](sql-database-always-encrypted-azure-key-vault.md)Windows-certifikatarkiv och lokala maskinvarusäkerhetsmoduler.

|**Kännetecken **|**Alltid krypterad**|**Transparent datakryptering**|
|---|---|---|
|**Kryptering span**|Från på slutet|Efter vila data|
|**Databasservern kan komma åt känsliga data**|Inga|Ja, eftersom kryptering är för data i vila|
|**Tillåtna T-SQL-åtgärder**|Jämförelse av jämställdhet|Alla T-SQL-ytor är tillgängliga|
|**Appändringar som krävs för att använda funktionen**|Minimal|Mycket minimal|
|**Krypteringsgranularitet**|Kolumnnivå|databasnivå|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hur kan jag begränsa åtkomsten till känsliga data i min databas

Varje program har en viss bit av känsliga data i databasen som måste skyddas från att vara synlig för alla. Viss personal inom organisationen måste visa dessa data, men andra bör inte kunna visa dessa data. Ett exempel är anställdas löner. En chef skulle behöva tillgång till löneinformation för sina direkta rapporter men de enskilda gruppmedlemmarna bör inte ha tillgång till löneinformation av sina kamrater. Ett annat scenario är datautvecklare som kan interagera med känsliga data under utvecklingsstadier eller testning, till exempel SSN-nätverk för kunder. Den här informationen behöver inte exponeras för utvecklaren igen. I sådana fall måste dina känsliga uppgifter antingen maskeras eller inte exponeras alls. SQL Database erbjuder två sådana metoder för att förhindra att obehöriga användare kan visa känsliga data:

[Dynamisk datamaskering](sql-database-dynamic-data-masking-get-started.md) är en datamaskeringsfunktion som gör att du kan begränsa känslig dataexponering genom att maskera den till icke-privilegierade användare i programlagret. Du definierar en maskeringsregel som kan skapa ett maskeringsmönster (till exempel för att bara visa de fyra sista siffrorna i ett nationellt ID SSN: XXX-XX-0000 och markera det mesta som Xs) och identifiera vilka användare som ska uteslutas från maskeringsregeln. Maskering sker i farten och det finns olika maskeringsfunktioner tillgängliga för olika datakategorier. Med dynamisk datamaskering kan du automatiskt identifiera känsliga data i databasen och använda maskering på den.

[Med säkerhet på radnivå](/sql/relational-databases/security/row-level-security) kan du styra åtkomsten på radnivå. Det innebär att vissa rader i en databastabell baserat på användaren som kör frågan (gruppmedlemskap eller körningskontext) döljs. Åtkomstbegränsningen görs på databasnivån i stället för på en programnivå för att förenkla applogiken. Du börjar med att skapa ett filter predikat, filtrera bort rader som inte är exponerade och säkerhetsprincipen nästa definiera vem som har tillgång till dessa rader. Slutligen kör slutanvändaren sin fråga och, beroende på användarens behörighet, de antingen visa dessa begränsade rader eller inte kan se dem alls.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hur hanterar jag krypteringsnycklar i molnet

Det finns viktiga hanteringsalternativ för både Alltid krypterad (kryptering på klientsidan) och transparent datakryptering (kryptering i vila). Vi rekommenderar att du regelbundet roterar krypteringsnycklar. Rotationsfrekvensen bör anpassas till både dina interna organisationsregler och efterlevnadskrav.

#### <a name="transparent-data-encryption-tde"></a>Transparent datakryptering (TDE)

Det finns en tvånyckelhierarki i TDE – data i varje användardatabas krypteras av en symmetrisk AES-256-databasunika databaskrypteringsnyckel (DEK), som i sin tur krypteras av en serverunika asymmetrisk RSA 2048-huvudnyckel. Huvudnyckeln kan hanteras antingen:

- Automatiskt av plattformen - SQL Database.
- Eller genom att du använder [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) som nyckelbutik.

Som standard hanteras huvudnyckeln för transparent datakryptering av SQL Database-tjänsten för enkelhetens skull. Om din organisation vill ha kontroll över huvudnyckeln finns det ett alternativ för att använda Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) som nyckelbutik. Genom att använda Azure Key Vault tar din organisation kontroll över nyckeletablering, rotation och behörighetskontroller. [Rotation eller byte av typ av TDE-huvudnyckel](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) är snabbt, eftersom det bara krypterar om DEK. För organisationer med åtskillnad mellan roller mellan säkerhet och datahantering kan en säkerhetsadministratör etablera nyckelmaterialet för TDE-huvudnyckeln i Azure Key Vault och tillhandahålla en Azure Key Vault-nyckelidentifierare till databasadministratören som ska användas för kryptering i vila på en server. Key Vault är utformad så att Microsoft inte ser eller extraherar några krypteringsnycklar. Du får också en centraliserad hantering av nycklar för din organisation.

#### <a name="always-encrypted"></a>Alltid krypterad

Det finns också en [tvånyckelhierarki](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) i Always Encrypted - en kolumn med känsliga data krypteras av en AES 256-kolumn krypteringsnyckel (CEK), som i sin tur krypteras av en kolumn huvudnyckel (CMK). Klientdrivrutinerna som tillhandahålls för Alltid krypterade har inga begränsningar för längden på CMK:er. Det krypterade värdet för CEK lagras i databasen och CMK lagras i ett tillförlitligt nyckelarkiv, till exempel Windows Certificate Store, Azure Key Vault eller en maskinvarusäkerhetsmodul.

- Både [CEK och CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kan roteras.
- CEK-rotation är en storlek på dataåtgärd och kan vara tidsintensiv beroende på storleken på de tabeller som innehåller de krypterade kolumnerna. Därför är det klokt att planera CEK rotationer därefter.
- CMK-rotation stör dock inte databasens prestanda och kan göras med separerade roller.

I följande diagram visas alternativen för nyckellagring för kolumnhuvudtangenterna i Alltid krypterad

![Alltid krypterade CMK-butiksleverantörer](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hur kan jag optimera och säkra trafiken mellan min organisation och SQL Database

Nätverkstrafiken mellan organisationen och SQL Database dirigeras i allmänhet över det offentliga nätverket. Men om du väljer att optimera den här sökvägen och göra den säkrare kan du titta på Express Route. Med expressrutten kan du utöka ditt företagsnätverk till Azure-plattformen via en privat anslutning. På så sätt går du inte över det offentliga Internet. Du får också högre säkerhet, tillförlitlighet och routning optimering som översätts till lägre nätverk svarstider och mycket snabbare hastigheter än du normalt skulle uppleva att gå över det offentliga internet. Om du planerar att överföra en betydande del av data mellan din organisation och Azure kan det ge kostnadsfördelar med Express Route. Du kan välja mellan tre olika anslutningsmodeller för anslutningen från din organisation till Azure:

- [Samlokalisering för molnutbyte](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Alla som helst](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route kan du också spränga upp till 2x bandbreddsgränsen du köper utan extra kostnad. Det är också möjligt att konfigurera anslutning mellan regioner med hjälp av Express-vägen. En lista över ER-anslutningsleverantörer finns i: [Express Route Partners och Peering Locations](../expressroute/expressroute-locations.md). I följande artiklar beskrivs Express Route mer i detalj:

- [Introduktion på Express Route](../expressroute/expressroute-introduction.md)
- [Krav](../expressroute/expressroute-prerequisites.md)
- [Arbetsflöden](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Är SQL Database kompatibel med eventuella regulatoriska krav, och hur hjälper det till med min egen organisations efterlevnad

SQL Database är kompatibel med en rad regulatoriska compliancies. Om du vill visa den senaste uppsättningen compliancies som har mötts av SQL Database besöker du [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) och går nedåt i de compliancies som är viktiga för din organisation för att se om SQL Database ingår i de kompatibla Azure-tjänsterna. Det är viktigt att notera att även om SQL Database kan certifieras som en kompatibel tjänst, hjälper den till att uppfylla organisationens tjänst men garanterar den inte automatiskt.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligent databasövervakning och underhåll efter migreringen

När du har migrerat databasen till SQL Database, kommer du att vilja övervaka databasen (till exempel kontrollera hur resursutnyttjandet är som eller DBCC kontroller) och utföra regelbundet underhåll (till exempel återskapa eller omorganisera index, statistik etc.). Lyckligtvis är SQL Database intelligent i den meningen att den använder historiska trender och inspelade mått och statistik för att proaktivt hjälpa dig att övervaka och underhålla din databas, så att ditt program körs optimalt alltid. I vissa fall kan Azure SQL DB automatiskt utföra underhållsuppgifter beroende på konfigurationskonfigurationen. Det finns tre aspekter för att övervaka databasen i SQL Database:

- Prestandaövervakning och optimering.
- Säkerhetsoptimering.
- Kostnadsoptimering.

### <a name="performance-monitoring-and-optimization"></a>Övervakning och optimering av prestanda

Med Query Performance Insights kan du få skräddarsydda rekommendationer för din databasarbetsbelastning så att dina program kan fortsätta att köras på en optimal nivå - alltid. Du kan också ställa in den så att dessa rekommendationer tillämpas automatiskt och du behöver inte bry dig om att utföra underhållsuppgifter. Med Index Advisor kan du automatiskt implementera indexrekommendationer baserat på din arbetsbelastning - det kallas automatisk justering. Rekommendationerna utvecklas när programarbetsbelastningen ändras för att ge dig de mest relevanta förslagen. Du får också möjlighet att manuellt granska dessa rekommendationer och tillämpa dem efter eget gottfinnande.  

### <a name="security-optimization"></a>Optimering av säkerhet

SQL Database innehåller användbara säkerhetsrekommendationer som hjälper dig att skydda dina data och hotidentifiering för att identifiera och undersöka misstänkta databasaktiviteter som kan utgöra en potentiell tråd till databasen. [Sårbarhetsbedömning](sql-vulnerability-assessment.md) är en databasskannings- och rapporteringstjänst som gör att du kan övervaka säkerhetstillståndet för dina databaser i stor skala och identifiera säkerhetsrisker och glida från en säkerhetsbaslinje som definierats av dig. Efter varje genomsökning tillhandahålls en anpassad lista över användbara steg och reparationsskript, samt en utvärderingsrapport som kan användas för att uppfylla efterlevnadskraven.

Med Azure Security Center identifierar du säkerhetsrekommendationerna över hela linjen och tillämpar dem med ett enda klick.

### <a name="cost-optimization"></a>Kostnadsoptimering

Azure SQL-plattformen analyserar användningshistoriken i databaserna på en server för att utvärdera och rekommendera kostnadsoptimeringsalternativ för dig. Denna analys tar vanligtvis två veckor att analysera och bygga upp användbara rekommendationer. Elastisk pool är ett sådant alternativ. Rekommendationen visas på portalen som en banner:

![elastiska poolrekommendationer](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Du kan också visa den här analysen under avsnittet "Advisor":

![elastisk pool rekommendationer-rådgivare](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hur övervakar jag prestanda- och resursanvändningen i SQL Database

I SQL Database kan du utnyttja plattformens intelligenta insikter för att övervaka prestanda och ställa in därefter. Du kan övervaka prestanda och resursanvändning i SQL Database med hjälp av följande metoder:

#### <a name="azure-portal"></a>Azure Portal

Azure-portalen visar en databass användning genom att välja databasen och klicka på diagrammet i fönstret Översikt. Du kan ändra diagrammet för att visa flera mått, inklusive CPU-procent, DTU-procent, data-IO-procent, sessionsprocent och databasstorleksprocent.

![Övervakningsdiagram](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Övervakningsdiagram2](./media/sql-database-manage-after-migration/chart.png)

I det här diagrammet kan du också konfigurera aviseringar efter resurs. Med dessa aviseringar kan du svara på resursvillkor med ett e-postmeddelande, skriva till en HTTPS/HTTP-slutpunkt eller utföra en åtgärd. Mer information finns i [Skapa aviseringar](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Dynamiska hanteringsvyer

Du kan fråga [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamisk hanteringsvy för att returnera resursförbrukningsstatistikhistorik från den senaste timmen och [systemkatalogvyn sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) för att returnera historiken för de senaste 14 dagarna.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Med Frågeprestanda insikt](sql-database-query-performance.md) kan du se en historik över de viktigaste resurskrävande frågorna och tidskrävande frågor för en viss databas. Du kan snabbt identifiera TOP-frågor efter resursutnyttjande, varaktighet och körningsfrekvens. Du kan spåra frågor och identifiera regression. Den här funktionen kräver att [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) är aktiverat och aktivt för databasen.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (förhandsversion) i Azure Monitor-loggar

[Med Azure Monitor-loggar](../azure-monitor/insights/azure-sql.md) kan du samla in och visualisera viktiga prestandamått för Azure SQL-databas, med stöd för upp till 150 000 SQL-databaser och 5 000 SQL-elastiska pooler per arbetsyta. Du kan använda den för att övervaka och ta emot meddelanden. Du kan övervaka SQL Database och elastiska poolmått över flera Azure-prenumerationer och elastiska pooler och kan användas för att identifiera problem på varje lager i en programstack.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jag märker prestandaproblem: Hur skiljer sig felsökningsmetoden för SQL Database från SQL Server

En stor del av felsökningsteknikerna som du skulle använda för att diagnostisera problem med fråga- och databasprestanda förblir desamma. När alla samma SQL Server-motor driver molnet. Plattformen - Azure SQL DB har dock byggt in "intelligens". Det kan hjälpa dig att felsöka och diagnostisera prestandaproblem ännu enklare. Det kan också utföra några av dessa korrigerande åtgärder för din räkning och i vissa fall proaktivt åtgärda dem - automatiskt.

Din metod för felsökning av prestandaproblem kan dra stor nytta av att använda intelligenta funktioner som [Query Performance Insight (QPI)](sql-database-query-performance.md) och [Database Advisor](sql-database-advisor.md) tillsammans och så skillnaden i metod skiljer sig åt i det avseendet - du behöver inte längre göra det manuella arbetet med att slipa ut de viktigaste detaljerna som kan hjälpa dig att felsöka problemet. Plattformen gör det hårda arbetet för dig. Ett exempel på detta är QPI. Med QPI kan du öka detaljnivån hela vägen ner till frågenivån och titta på de historiska trenderna och ta reda på exakt när frågan gick tillbaka. Database Advisor ger dig rekommendationer om saker som kan hjälpa dig att förbättra din totala prestanda i allmänhet som - saknade index, släppa index, parameterisera dina frågor etc.

Med felsökning av prestanda är det viktigt att identifiera om det bara är programmet eller databasen som stöder det, som påverkar programmets prestanda. Ofta ligger prestandaproblemet i programlagret. Det kan vara arkitekturen eller dataåtkomstmönstret. Tänk dig till exempel att du har ett pratsamt program som är känsligt för nätverksfördröjning. I det här fallet lider ditt program eftersom det skulle finnas många korta förfrågningar som går fram och tillbaka ("pratsam") mellan programmet och servern och på ett överbelastat nätverk, dessa tur och returresor lägga upp snabbt. Om du vill förbättra prestanda i det här fallet kan du använda [Batchfrågor](sql-database-performance-guidance.md#batch-queries). Använda batchar hjälper dig oerhört eftersom nu dina förfrågningar få bearbetas i en batch; vilket hjälper dig att skära ner på svarstiden för tur och retur och förbättra programmets prestanda.

Om du märker en försämring av databasens övergripande prestanda kan du dessutom övervaka [system.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamiska hanteringsvyer för att förstå CPU-, IO- och minnesförbrukning. Din prestanda kanske påverkas eftersom din databas är utsvulten på resurser. Det kan vara så att du kan behöva ändra beräkningsstorlek och/eller tjänstnivå baserat på de växande och krympande arbetsbelastningskraven.

En omfattande uppsättning rekommendationer för att justera prestandaproblem finns i: [Justera databasen](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hur säkerställer jag att jag använder lämplig tjänstnivå och beräkningsstorlek

SQL Database erbjuder olika tjänstnivåer Basic, Standard och Premium. Varje tjänstnivå får du en garanterad förutsägbar prestanda knuten till den tjänstnivån. Beroende på din arbetsbelastning kan du ha utbrott av aktivitet där resursutnyttjandet kan nå taket för den aktuella beräkningsstorleken som du befinner dig i. I sådana fall är det bra att först börja med att utvärdera om någon justering kan hjälpa (till exempel lägga till eller ändra ett index etc.). Om du fortfarande stöter på begränsningsproblem bör du överväga att flytta till en högre tjänstnivå eller beräkningsstorlek.

|**Tjänstenivå**|**Vanliga användningsfallsscenarier**|
|---|---|
|**Basic**|Program med en handfull användare och en databas som inte har höga samtidighets-, skalnings- och prestandakrav. |
|**Standard**|Applikationer med betydande samtidighet, skala och prestandakrav i kombination med låga till medelstora IO-krav. |
|**Premium**|Program med många samtidiga användare, hög CPU / minne, och höga IO krav. Känsliga appar med hög samtidighet, högt dataflöde och latens kan utnyttja Premium-nivån. |
|||

För att se till att du är på rätt beräkningsstorlek, kan du övervaka din fråga och databas resursförbrukning genom ett av de ovan nämnda sätten i "Hur övervakar jag prestanda och resursutnyttjande i SQL Database". Om du upptäcker att dina frågor / databaser konsekvent körs snabbt på CPU / minne etc. kan du överväga att skala upp till en högre beräkningsstorlek. På samma sätt, om du noterar att även under dina rusningstid, du verkar inte använda resurserna så mycket; överväga att skala ned från den aktuella beräkningsstorleken.

Om du har ett SaaS-appmönster eller ett databaskonsolideringsscenario kan du använda en elastisk pool för kostnadsoptimering. Elastisk pool är ett bra sätt att uppnå databaskonsolidering och kostnadsoptimering. Mer information om hur du hanterar flera databaser med elastisk pool finns i: [Hantera pooler och databaser](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Hur ofta måste jag köra databasintegritetskontroller för min databas

SQL Database använder vissa smarta tekniker som gör det möjligt att hantera vissa klasser av data korruption automatiskt och utan förlust av data. Dessa tekniker är inbyggda i tjänsten och utnyttjas av tjänsten när det behövs. Regelbundet testas säkerhetskopior av databasen över tjänsten genom att återställa dem och köra DBCC CHECKDB på den. Om det finns problem åtgärdar SQL Database dem proaktivt. [Automatisk sidreparation](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) utnyttjas för att åtgärda sidor som är skadade eller har problem med dataintegritet. Databassidorna verifieras alltid med standardinställningen CHECKSUM som verifierar sidans integritet. SQL Database övervakar och granskar databasens dataintegritet proaktivt och åtgärdar dem med högsta prioritet om problem uppstår. Utöver dessa kan du välja att eventuellt köra dina egna integritetskontroller efter din vilja.  Mer information finns [i Dataintegritet i SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Datarörelse efter migreringen

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hur exporterar och importerar jag data som BACPAC-filer från SQL Database

- **Exportera**: Du kan exportera din Azure SQL-databas som en BACPAC-fil från Azure-portalen

   ![databasexport](./media/sql-database-export/database-export1.png)

- **Importera**: Du kan också importera data som en BACPAC-fil till databasen med Hjälp av Azure-portalen.

   ![import av databas](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hur synkroniserar jag data mellan SQL Database och SQL Server

Du har flera sätt att uppnå detta:

- **[Datasynkronisering](sql-database-sync-data.md)** – Den här funktionen hjälper dig att synkronisera data bi-directionally mellan flera lokala SQL Server-databaser och SQL Database. Om du vill synkronisera med lokala SQL Server-databaser måste du installera och konfigurera synkroniseringsagenten på en lokal dator och öppna den utgående TCP-porten 1433.
- **[Transaktionsreplikering](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – Med transaktionsreplikering kan du synkronisera dina data från lokala till Azure SQL DB med att den lokala är utgivaren och Azure SQL DB som prenumerant. För tillfället stöds bara den här inställningen. Mer information om hur du migrerar data från lokalt till Azure SQL med minimal stilleståndstid finns i: [Använd transaktionsreplikering](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [SQL Database](sql-database-technical-overview.md).
