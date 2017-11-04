---
title: "Översikt över säkerheten i Azure-databas | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över Azure-databas säkerhetsfunktioner."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 3c83645f61cd42c8c2b46f787c9e7531726d3fbb
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="azure-database-security-overview"></a>Översikt över säkerheten i Azure-databas

Säkerhet är ett viktigt mål vid hantering av databaser och det har alltid en prioritet för Azure SQL Database. Azure SQL Database stöder anslutningssäkerhet med brandväggsregler och krypterad anslutning. Den stöder autentisering med användarnamn och lösenord och Azure Active Directory-autentisering, som använder identiteter som hanteras av Azure Active Directory. Auktorisering använder rollbaserad åtkomstkontroll.

Azure SQL Database stöder kryptering genom att utföra realtid kryptering och dekryptering av databaser, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.

Microsoft tillhandahåller många möjligheter att kryptera företagsdata:

-   Cellnivå kryptering för att kryptera specifika kolumner eller även dataceller med olika krypteringsnycklar.
-   Om du behöver en maskinvarusäkerhetsmodul eller central hantering av hierarkin kryptering kan du använda Azure Key Vault med SQL Server i en Azure VM.
-   Alltid krypterat (för närvarande under förhandsgranskning) gör kryptering är transparent för program och tillåter klienter att kryptera känsliga data i klientprogram utan att dela krypteringsnycklarna med SQL-databas.

Azure SQL Database Auditing gör att företag kan registrera händelser till en audit inloggning Azure Storage. SQL Database Auditing är integrerat med Microsoft Power BI för att underlätta ingående rapporter och analyser.

 SQL Azure-databaser kan skyddas nära för att uppfylla de flesta reglerande eller säkerhetskrav, bland annat HIPAA, ISO 27001/27002 och PCI DSS nivå 1. En lista över efterlevnad Säkerhetscertifieringar är tillgänglig på den [Microsoft Azure Trust Center plats](http://azure.microsoft.com/support/trust-center/services/).

Den här artikeln beskriver hur grunderna i att skydda Microsoft Azure SQL-databaser för Structured, tabeller och relationella Data. Den här artikeln i synnerhet, hjälper dig att komma igång med resurser för att skydda data, kontrollera åtkomst och proaktiv övervakning.

Översikt över säkerheten i Azure-databas artikeln fokuserar på följande områden:

-   Skydda data
-   Åtkomstkontroll
-   Proaktiv övervakning
-   Centraliserad hantering
-   Azure Marketplace

## <a name="protect-data"></a>Skydda data

SQL-databas skyddar dina data genom att tillhandahålla kryptering för data i rörelse med [Transport Layer Security](https://support.microsoft.com/kb/3135244)för rest-data med hjälp av [Transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242), och för data i användning med [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

I det här avsnittet pratar vi om:

-   Kryptering i rörelse
-   Vilande kryptering
-   Kryptering används (klient)

För andra sätt att kryptera dina data, kan du överväga:

-   [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   Om du behöver en Hardware Security Module eller central hantering av krypteringsnyckelns hierarki, bör du använda [Azure Key Vault med SQL Server i en virtuell Azure-dator](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Kryptering i rörelse

Ett vanligt problem för alla klient/server-program är behovet av sekretess eftersom data flyttas via offentliga och privata nätverk. Om data flyttas över ett nätverk inte är krypterade, är risken att den kan skapas och blir stulen av obehöriga användare. När du hanterar databastjänster, måste du se till att data krypteras mellan databasen klienten och servern och mellan database-servrar som kommunicerar med varandra och med mellannivå program.

Ett problem när du administrerar ett nätverk är att säkra data som skickas mellan program över ett osäkrat nätverk. Du kan använda [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna.

Under autentiseringsprocessen, en TLS/SSL-klient skickar ett meddelande till en TLS/SSL-server och servern svarar med informationen att servern måste autentisera sig själv. Klienten och servern utför ytterligare ett utbyte av sessionsnycklar och autentiseringsdialogen avslutas. När autentiseringen är slutförd kan startas SSL-skyddad kommunikation mellan servern och klienten med hjälp av de symmetriska krypteringsnycklar som fastställts under autentiseringsprocessen.

Alla anslutningar till Azure SQL Database kräver filkryptering (SSL/TLS) hela tiden medan data är "under överföringen" till och från databasen. SQL Azure använder TLS/SSL för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna. I anslutningssträngen för ditt program, måste du ange parametrar för att kryptera anslutningen och inte till förtroende för certifikatet (det gör du om du kopierar anslutningssträngen utanför Azure-portalen), annars anslutningen inte kommer att verifiera den identiteten för servern och kommer att utsättas för angrepp med ”man-in-the-middle”. För ADO.NET-drivrutinen exempelvis dessa anslutning string-parametrar är Encrypt = True och TrustServerCertificate = False.

### <a name="encryption-at-rest"></a>Vilande kryptering
Du kan utföra flera åtgärder för att skydda databasen som en säker system utformas, kryptera konfidentiell tillgångar och skapa en brandvägg runt databasservrar. Dock i ett scenario där det fysiska mediet (till exempel enheter eller band) blir stulna, kan en skadlig part bara återställa eller koppla databasen och bläddra igenom informationen.

En lösning är att kryptera känsliga data i databasen och skyddar de nycklar som används för att kryptera data med ett certifikat. Detta förhindrar att någon utan nycklar med hjälp av data, men den här typen av skydd måste planeras.

Lös problemet, SQL Server och Azure SQL stöder [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). TDE krypterar SQL Server och Azure SQL Database-datafiler, kallas även krypteringsdata i vila.

Azure SQL Database transparent datakryptering skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.  

TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln skyddas av en inbyggd servercertifikat i SQL-databas. Inbyggda certifikatet är unikt för varje SQL Database-server.

Om en databas finns i någon GeoDR-relation, skyddas den av en annan nyckel på varje server. Om två databaser är anslutna till samma server, delar de samma inbyggda certifikat. Microsoft roteras automatiskt dessa certifikat minst var 90: e dag. En allmän beskrivning av TDE finns [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Kryptering används (klient)

De flesta dataintrång omfattar stöld av kritiska data, till exempel kreditkortsnummer eller personligt identifierbar information. Databaser kan vara skatt troves känslig information. De kan innehålla kundernas personliga data, konkurrenskraftiga konfidentiell information och immateriell egendom. Borttappade eller stulna data, särskilt kundinformation kan resultera i varumärken skador och konkurrenskraftiga nackdelen allvarliga böter – även rättegångar.

![Alltid krypterad](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) är en funktion som utformats för att skydda känsliga data, till exempel kreditkortsnummer eller nationella ID-nummer (till exempel USA personnummer), lagras i Azure SQL Database eller SQL Server-databaser. Alltid tillåter krypterat klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server).

Krypterad ger alltid en åtskillnad mellan dem som äger data (och visa det) och de som hanterar data (men bör har ingen åtkomst). Genom att kontrollera lokal databasadministratörer molnet ansvariga för databasen eller andra privilegierad, men obehöriga användare kan inte komma åt krypterade data.

Dessutom blir Always Encrypted kryptering transparent för program. En Always Encrypted-aktiverade drivrutin som installerats på klientdatorn så att den kan kryptera och dekryptera känsliga data i klientprogrammet automatiskt. Drivrutinen krypteras data i känsliga kolumner innan du skickar data till databasmotorn och skriver frågor automatiskt så att semantik till programmet bevaras. På liknande sätt dekrypterar drivrutinen transparent data som lagras i krypterade databaskolumner som ingår i frågeresultatet.

## <a name="access-control"></a>Åtkomstkontroll
För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data.

### <a name="database-access"></a>Åtkomst till databasen

Dataskydd börjar med att kontrollera åtkomst till dina data. Datacenter som värd för dina data hanterar fysisk åtkomst medan du kan konfigurera en brandvägg för att hantera säkerhet på nätverksnivå. Du kan också styra åtkomsten genom att konfigurera inloggningar för autentisering och definiera rättigheter för servern och databasen roller.

I det här avsnittet pratar vi om:

-   Brandvägg och brandväggsregler
-   Autentisering
-   Auktorisering

#### <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler

Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tjänsten finns endast tillgängliga via TCP-port 1433. Se till att klientdatorns brandvägg tillåter utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL Database från datorn. Om det inte behövs för andra program, blockera inkommande anslutningar på TCP-port 1433.

#### <a name="authentication"></a>Autentisering

SQL-databasautentisering refererar till hur du styrker din identitet när du ansluter till databasen. SQL Database stöder två typer av autentisering:

-   **SQL-autentisering:** en enkel inloggningskontot skapas när en logisk SQL-instans skapas med namnet konto för SQL Database-prenumerant. Det här kontot ansluter med hjälp av [SQL Server-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (användarnamn och lösenord). Kontot är administratör på den logiska server-instansen och på alla användardatabaser kopplade till denna instans. Behörigheterna för prenumerationskontot kan inte begränsas. Endast ett av dessa konton kan finnas.
-   **Azure Active Directory-autentisering:** [Azure Active Directory-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) är en mekanism för anslutning till Microsoft Azure SQL Database och SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD Azure). På så sätt kan du centralt hantera identiteter för databasanvändare.

![Autentisering](./media/azure-databse-security-overview/azure-database-fig2.png)

 Fördelarna med Azure Active Directory-autentisering är:
  - Det ger ett alternativ till SQL Server-autentisering.
  - Även stoppa spridning av användaridentiteter över databasservrar & tillåter lösenord rotation i en enda plats.
  - Du kan hantera databasen med hjälp av grupper för externa (Azure Active Directory).
  - Men du kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.

#### <a name="authorization"></a>Auktorisering
[Auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) refererar till vad en användare kan utföra i en Azure SQL Database och detta styrs av ditt användarkonto databasen [rollmedlemskap](https://msdn.microsoft.com/library/ms189121) och [på objektnivå behörigheter](https://msdn.microsoft.com/library/ms191291.aspx). Auktorisering är processen att avgöra vilka skyddbara resurser som en huvudansvarig kan komma åt och vilka åtgärder tillåts för dessa resurser.

### <a name="application-access"></a>Programåtkomst

I det här avsnittet pratar vi om:

-   Dynamisk datamaskning
-   Säkerhet på radnivå

#### <a name="dynamic-data-masking"></a>Dynamisk datamaskning
En representant på ett Callcenter kan identifiera anropare med flera siffrorna i sina personnummer eller kreditkortsnummer, men de data som ska inte visas helt till representanten.

En maskningsregel kan definieras att masker alla, men de sista fyra siffrorna för personnummer eller kreditkortsnummer i resultatet som angetts av en fråga.

![Dynamisk datamaskning](./media/azure-databse-security-overview/azure-database-fig3.png)

Ett annat exempel kan en lämplig mask definieras för att skydda personligt identifierbar information (PII) data så att utvecklare kan fråga produktionsmiljöer i felsökningssyfte utan brott mot förordningar.

[SQL-databas dynamisk Datamaskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera till icke-privilegierade användare. Dynamisk datamaskning stöds för V12-versionen av Azure SQL Database.

[Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) hjälper till att förhindra obehörig åtkomst till känsliga data genom att du kan ange hur mycket av känsliga data för att avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.


> [!Note]
> Dynamisk datamaskning kan konfigureras med Azure Database admin, serveradministratör eller som säkerhetsroller.

#### <a name="row-level-security"></a>Säkerhet på radnivå
Ett annat vanliga säkerhetskrav för flera databaser är [säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131.aspx). Den här funktionen kan du styra åtkomsten till rader i en databastabell på grund av egenskaper för den användare som kör en fråga (t.ex. gruppen medlemskap eller köra sammanhang).

![-Säkerhet på radnivå](./media/azure-databse-security-overview/azure-database-fig4.png)

Logik för åtkomst-begränsning är finns i databasnivån i stället för avstånd från data i ett annat program skikt. Databasen tillämpas åtkomstbegränsningarna varje gång ett försök gjordes att dataåtkomst från alla skikt. Detta gör ditt säkerhetssystem mer tillförlitlig och stabil genom att minska ytan på ditt säkerhetssystem.

Säkerhet på radnivå introducerar predikat åtkomstkontroll. Den har en flexibel, centraliserad predikat-baserade utvärderingsversion som kan ta i beaktande metadata eller andra kriterier som administratören anger efter behov. Predikatet används som ett villkor för att avgöra om användaren har lämplig åtkomst till data baserat på användarattribut. Etikett-baserad åtkomstkontroll kan implementeras med hjälp av predikat-baserad åtkomstkontroll.

## <a name="proactive-monitoring"></a>Proaktiv övervakning
SQL-databas skyddar dina data genom att tillhandahålla **granskning** och **hotidentifiering** funktioner.

### <a name="auditing"></a>Granskning
SQL Database Auditing ökar möjligheten att få insyn i händelser och ändringar som görs i databasen, inklusive uppdateringar och frågor mot data.

[Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in i ditt Azure Storage-konto-databas. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser. Granskning kan och underlättar anslutning till efterlevnadsstandarder men garantera inte efterlevnad.

SQL Database Auditing kan du:

-   **Behåll** redovisningsspårning markerade händelser. Du kan definiera typer av databasåtgärder som ska granskas.
-   **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktivitet och rapportera händelser.
-   **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

Det finns två metoder för granskning:

-   **Blobbgranskning** -loggarna skrivs till Azure Blob Storage. Detta är en senare granskning metod som ger högre prestanda, stöder högre granularitet på objektnivå granskning och är mer kostnadseffektivt.
-   **Tabell granskning** -loggarna skrivs till Azure Table Storage.

### <a name="threat-detection"></a>Hotidentifiering
[Azure SQL Database-hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) upptäcker misstänkta aktiviteter som indikerar potentiella säkerhetshot. Hotidentifiering kan du svara på misstänkt händelser i databasen, till exempel SQL injektioner när de inträffar. Den ger aviseringar och tillåter användning av Azure SQL Database Auditing att utforska misstänkta händelser.

![Hotidentifiering](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Till exempel är SQL injection en av säkerhetsproblem för vanliga Web program på Internet, används till att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen.

Säkerhet polis eller andra avsedda administratörer kan få en omedelbar avisering om misstänkt databasaktiviteter när de inträffar. Varje meddelande innehåller information om misstänkt aktivitet och rekommenderas så att ytterligare undersöka och minska risken.        

## <a name="centralized-security-management"></a>Centraliserad hantering

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) kan du skydda data i SQL-databas genom att tillhandahålla insyn i säkerheten för servrar och databaser. Med Security Center kan du:

-   Definiera principer för kryptering av SQL-databas och granskning.
-   Övervaka säkerheten i SQL-databas resurser över alla prenumerationer.
-   Snabbt identifiera och åtgärda säkerhetsproblem.
-   Integrera aviseringar från [Azure SQL Database hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Security Center stöder rollbaserad åtkomst.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace är en onlinemarknadsplats för program och tjänster där nystartade företag och oberoende programvaruleverantörer (ISV:er) har möjlighet att erbjuda sina lösningar till Azure-kunder över hela världen.
Azure Marketplace kombinerar Microsoft Azures partnerekosystem till en enhetlig plattform, så att vi kan betjäna våra kunder och partner på ett bättre sätt. Klicka på [här](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) att snabbt säkerhetsprodukter på databasen finns på Azure-marknadsplatsen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [skydda din Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Lär dig mer om [Azure Security Center och Azure SQL Database-tjänsten](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Läs mer om hotidentifiering i [Hotidentifiering för SQL-databasen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Läs mer i [förbättra SQL databasprestanda](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
