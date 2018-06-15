---
title: Översikt över säkerheten i Azure-databas | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure-databas säkerhetsfunktioner.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 44abf7a4fc24893146179b34d3357f54450decab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365152"
---
# <a name="azure-database-security-overview"></a>Översikt över säkerheten i Azure-databas

Säkerhet är ett viktigt mål för att hantera databaser och har alltid en prioritet för Azure SQL Database. Azure SQL Database stöder anslutningssäkerhet med brandväggsregler och krypterad anslutning. Den stöder autentisering med användarnamn och lösenord och autentisering för Azure Active Directory (Azure AD), som använder identiteter som hanteras av Azure Active Directory. Auktorisering använder rollbaserad åtkomstkontroll.

Azure SQL Database stöder kryptering genom att utföra realtid kryptering och dekryptering av databaser, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.

Microsoft tillhandahåller många möjligheter att kryptera företagsdata:

-   Kryptering på cellnivå är tillgänglig för att kryptera specifika kolumner eller även dataceller med olika krypteringsnycklar.
-   Om du behöver en maskinvarusäkerhetsmodul eller central hantering av hierarkin kryptering kan du använda Azure Key Vault med SQL Server på en Azure virtuell dator (VM).
-   Alltid gör krypterat (för närvarande under förhandsgranskning) kryptering transparent för program. Det gör också klienter att kryptera känsliga data i klientprogram utan att dela krypteringsnycklarna med SQL-databas.

Azure SQL Database Auditing kan företag post till en granskningslogg händelselogg i Azure Storage. SQL Database Auditing är integrerat med Microsoft Power BI för att underlätta ingående rapporter och analyser.

Azure SQL-databaser kan skyddas nära för att uppfylla de flesta reglerande eller säkerhetskrav, inklusive HIPAA, ISO 27001/27002 och PCI DSS nivå 1. En lista över efterlevnad Säkerhetscertifieringar är tillgänglig på den [Microsoft Azure Trust Center plats](http://azure.microsoft.com/support/trust-center/services/).

Den här artikeln beskriver hur grunderna i att skydda Microsoft Azure SQL-databaser för strukturerade tabular och relationella data. Den här artikeln i synnerhet, hjälper dig att komma igång med resurser för att skydda data, kontrollera åtkomst och proaktiv övervakning.

## <a name="protection-of-data"></a>Skydd av data

SQL-databas kan du skydda dina data genom att tillhandahålla kryptering:

- För data i rörelse via [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- För data i vila via [transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242).
- För data i via [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

För andra sätt att kryptera dina data, kan du överväga:

-   [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   [Azure Key Vault med SQL Server i en Azure VM](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), om du behöver en maskinvarusäkerhetsmodul eller central hantering av hierarkin kryptering.

### <a name="encryption-in-motion"></a>Kryptering i rörelse

Ett vanligt problem för alla klient/server-program är behovet av sekretess eftersom data flyttas via offentliga och privata nätverk. Om data flyttas över ett nätverk inte är krypterade, är det en risk att den kan skapas och blir stulen av obehöriga användare. När du hantera databastjänster, se till att data krypteras mellan databasen klienten och servern. Kontrollera också att krypteras data mellan database-servrar som kommunicerar med varandra och med mellannivå program.

Ett problem när du administrerar ett nätverk är att säkra data som skickas mellan program över ett osäkrat nätverk. Du kan använda [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna.

I autentiseringsprocessen skickar TLS/SSL-klienten ett meddelande till en TLS/SSL-server. Servern svarar med informationen att servern måste autentisera sig själv. Klienten och servern utför ytterligare ett utbyte av sessionsnycklar och autentiseringsdialogen avslutas. När autentiseringen är slutförd kan startas SSL-skyddad kommunikation mellan servern och klienten via de symmetriska krypteringsnycklar som fastställts under autentiseringsprocessen.

Alla anslutningar till Azure SQL Database Kräv kryptering TLS/SSL () vid alla tidpunkter när data är ”under överföring” till och från databasen. SQL-databasen använder TLS/SSL för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna. 

Du måste ange parametrar för att kryptera anslutningen och inte till förtroende för servercertifikatet i anslutningssträngen för ditt program. (Det gör du om du kopierar anslutningssträngen utanför Azure-portalen.) Annars kan anslutningen kommer inte att verifiera identiteten för servern och kommer att utsättas för angrepp med ”man-in-the-middle”. För ADO.NET-drivrutinen exempelvis dessa anslutning string-parametrar är `Encrypt=True` och `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Vilande kryptering
Du kan utföra flera åtgärder för att skydda databasen. Till exempel utforma ett säkert system, kryptera konfidentiell tillgångar och skapa en brandvägg runt databasservrar. Men i ett scenario där det fysiska mediet (till exempel enheter eller band) blir stulna, en skadlig part kan bara återställa eller koppla databasen och bläddra igenom informationen.

En lösning är att kryptera känsliga data i databasen och skydda nycklar som används för att kryptera data med ett certifikat. Den här lösningen förhindrar att någon utan nycklar med hjälp av data, men den här typen av skydd måste planeras.

Du löser problemet, SQL Server och SQL Database-stöd [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). Transparent datakryptering krypterar SQL Server och SQL Database-datafiler, kallas även krypteringsdata i vila.

Transparent datakryptering skyddar mot hot från skadlig aktivitet. Den genomför realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.  

Transparent datakryptering krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln skyddas av en inbyggd servercertifikat i SQL-databas. Inbyggda certifikatet är unikt för varje SQL Database-server.

Om en databas är i en relation för Geo-Katastrofåterställning, skyddas den av en annan nyckel på varje server. Om två databaser är anslutna till samma server, delar de samma inbyggda certifikat. Microsoft roteras automatiskt dessa certifikat minst var 90: e dag. 

Mer information finns i [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Kryptering används (klient)

De flesta dataintrång omfattar stöld av kritiska data, till exempel kreditkortsnummer eller personligt identifierbar information. Databaser kan vara skatt troves känslig information. De kan innehålla kundernas personliga data (till exempel nationella identifikationsnummer) konkurrenskraftiga konfidentiell information och immateriell egendom. Borttappade eller stulna data, särskilt kundinformation kan resultera i varumärken skador och konkurrenskraftiga nackdelen allvarliga böter--även rättegångar.

![Funktionen Always Encrypted visas som ett lås och nyckel](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) är en funktion som utformats för att skydda känsliga data som lagras i Azure SQL Database eller SQL Server-databaser. Alltid tillåter krypterat klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server).

Krypterad innehåller alltid en åtskillnad mellan personer som äger data (och visa det) och personer som hanterar data (men bör har ingen åtkomst). Det säkerställer att lokala databasen administratörer, databasen molnoperatörer eller andra privilegierad men obehöriga användare inte tillgång till krypterade data.

Dessutom blir Always Encrypted kryptering transparent för program. En Always Encrypted-aktiverade drivrutin är installerad på klientdatorn så att den automatiskt kan kryptera och dekryptera känsliga data i klientprogrammet. Drivrutinen krypteras data i känsliga kolumner innan du skickar data till databasmotorn. Drivrutinen skriver frågor automatiskt om så att semantik till programmet bevaras. På liknande sätt dekrypterar drivrutinen transparent data som lagras i krypterade databaskolumner som ingår i frågeresultatet.

## <a name="access-control"></a>Åtkomstkontroll
För att tillhandahålla säkerhet, kontrollerar SQL-databas åtkomsten med hjälp av:

- Brandväggsregler som begränsar anslutning med IP-adress.
- Autentiseringsmekanismer som kräver att användare att bevisa sin identitet.
- Auktorisering mekanismer som begränsa användare till specifika åtgärder och data.

### <a name="database-access"></a>Databasåtkomst

Dataskydd börjar med att kontrollera åtkomst till dina data. Det datacenter som värd för dina data hanterar fysisk åtkomst. Du kan konfigurera en brandvägg för att hantera säkerhet på nätverksnivå. Du kan också styra åtkomsten genom att konfigurera inloggningar för autentisering och definiera rättigheter för servern och databasen roller.

#### <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) en relationsdatabas tjänst för Azure och andra internet-baserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Azure SQL Database-tjänsten är tillgänglig endast via TCP-port 1433. Se till att klienten datorn brandväggen tillåter att utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL-databas från datorn. Om inkommande anslutningar inte behövs för andra program, kan du blockera dem på TCP-port 1433.

#### <a name="authentication"></a>Autentisering

Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:

-   **SQL Server-autentisering**: en enkel inloggningskontot skapas när en logisk SQL-instans skapas med namnet konto för SQL Database-prenumerant. Det här kontot ansluter med hjälp av [SQL Server-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (användarnamn och lösenord). Kontot är administratör på den logiska server-instansen och på alla användardatabaser kopplade till denna instans. Behörigheterna för prenumerations-konto kan inte begränsas. Endast ett av dessa konton kan finnas.
-   **Azure Active Directory-autentisering**: [Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) är en mekanism för anslutning till Azure SQL Database och Azure SQL Data Warehouse med hjälp av identiteter i Azure AD. Du kan använda den för att centralt hantera identiteter för databasanvändare.

![Azure AD-autentisering med SQL-databas](./media/azure-databse-security-overview/azure-database-fig2.png)

 Fördelarna med Azure AD-autentisering är:
  - Det ger ett alternativ till SQL Server-autentisering.
  - Den stoppa spridning av användaridentiteter över databasservrar och tillåter lösenord rotation i en enda plats.
  - Du kan hantera databasbehörigheter med hjälp av externa (Azure AD)-grupper.
  - Men du kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som har stöd för Azure AD.

#### <a name="authorization"></a>Auktorisering
[Auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) refererar till en användare kan göra i en Azure SQL database. Den styrs av ditt användarkonto databasen [rollmedlemskap](https://msdn.microsoft.com/library/ms189121) och [på objektnivå behörigheter](https://msdn.microsoft.com/library/ms191291.aspx). Auktorisering är processen att avgöra vilka skyddbara resurser som en huvudansvarig kan komma åt och vilka åtgärder tillåts för dessa resurser.

### <a name="application-access"></a>Programåtkomst

#### <a name="dynamic-data-masking"></a>Dynamisk datamaskning
En representant på ett Callcenter kan identifiera anropare med flera siffrorna i sina personnummer eller kreditkortsnummer. Men de data som ska inte visas helt till representanten.

Du kan definiera en maskningsregel som döljer alla utom de sista fyra siffrorna i ett personnummer eller kreditkortsnummer i resultatet av en fråga.

![Maskning på ett nummer som skickas mellan SQL-databasen och företag appar](./media/azure-databse-security-overview/azure-database-fig3.png)

Ett annat exempel är kan en lämplig mask definieras för att skydda personligt identifierbar information. En utvecklare kan sedan fråga produktionsmiljöer i felsökningssyfte utan brott mot förordningar.

[SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk datamaskning stöds för V12-versionen av Azure SQL Database.

[Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) hjälper till att förhindra obehörig åtkomst till känsliga data genom att du kan ange hur mycket av känsliga data för att avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.


> [!Note]
> Dynamisk datamaskning kan konfigureras med Azure Database admin, serveradministratör eller som säkerhetsroller.

#### <a name="row-level-security"></a>Säkerhet på radnivå
Ett annat vanliga säkerhetskrav för flera databaser är [säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131.aspx). Du kan använda den här funktionen för att styra åtkomsten till rader i en databastabell på grund av egenskaper för den användare som kör en fråga. (Exempel egenskaper är grupp medlemskap och körningen kontext.)

![Radnivå säkerhet att tillåta att en användare åtkomst till rader i en tabell med ett klientprogram](./media/azure-databse-security-overview/azure-database-fig4.png)

Logik för åtkomst-begränsning är finns i databasnivån i stället för avstånd från data i ett annat program skikt. Databasen tillämpas åtkomstbegränsningarna varje gång ett försök gjordes att dataåtkomst från alla skikt. Detta gör ditt säkerhetssystem mer tillförlitlig och stabil genom att minska ytan på ditt säkerhetssystem.

Säkerhet på radnivå introducerar predikat-baserad åtkomstkontroll. Den har en flexibel centraliserad utvärderingsversion som kan ta i beaktande metadata eller andra kriterier som administratören anger efter behov. Predikatet används som ett villkor för att avgöra om användaren har lämplig åtkomst till data baserat på användarattribut. Du kan implementera etikett-baserad åtkomstkontroll med hjälp av predikat-baserad åtkomstkontroll.

## <a name="proactive-monitoring"></a>Proaktiv övervakning
SQL-databas, hjälper till att skydda dina data genom att tillhandahålla *granskning* och *hotidentifiering* funktioner.

### <a name="auditing"></a>Granskning
[Azure SQL Database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) ökar möjligheten att få insyn i händelser och ändringar som görs i databasen. Exempel är uppdateringar och frågor mot data.

SQL Database auditing spårar databashändelser och skriver dem till en granskningslogg logga i Azure storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser. Granskning kan och underlättar anslutning till efterlevnadsstandarder men garantera inte efterlevnad.

Du kan använda SQL-databas granskning:

-   **Behåll** redovisningsspårning markerade händelser. Du kan definiera typer av databasåtgärder som ska granskas.
-   **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktivitet och rapportera händelser.
-   **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

Det finns två metoder för granskning:

-   **Blobbgranskning**: loggarna skrivs till Azure Blob storage. Detta är en senare granskning metod. Det ger högre prestanda, stöder högre granularitet på objektnivå granskning och är mer kostnadseffektivt.
-   **Tabell granskning**: loggarna skrivs till Azure Table storage.

### <a name="threat-detection"></a>Hotidentifiering
[Azure SQL Database-hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) upptäcker misstänkta aktiviteter som indikerar potentiella säkerhetshot. Du kan använda hotidentifiering för att svara på misstänkt händelser i databasen, till exempel SQL injektioner när de inträffar. Den ger aviseringar och tillåter användning av Azure SQL Database auditing om du vill utforska misstänkta händelser.

![Hotidentifiering för SQL-databas och ett webbprogram med en extern angripare och en intern](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Till exempel är SQL injection en av de vanliga säkerhetsproblem för webbprogram. Den används till att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen.

Säkerhet polis eller andra avsedda administratörer kan få en omedelbar avisering om misstänkt databasaktiviteter när de inträffar. Varje meddelande innehåller information om misstänkt aktivitet och rekommenderas så att ytterligare undersöka och minska risken.        

## <a name="centralized-security-management"></a>Centraliserad hantering

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer. Det hjälper dig att identifiera hot som annars kanske skulle förbli oupptäckta och den fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) kan du skydda data i SQL-databas genom att tillhandahålla insyn i säkerheten för servrar och databaser. Med Security Center kan du:

-   Definiera principer för kryptering av SQL-databas och granskning.
-   Övervaka säkerheten i SQL-databas resurser över alla prenumerationer.
-   Snabbt identifiera och åtgärda säkerhetsproblem.
-   Integrera aviseringar från [Azure SQL Database hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Security Center stöder rollbaserad åtkomst.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace är en onlinemarknadsplats för program och tjänster där nystartade företag och oberoende programvaruleverantörer (ISV:er) har möjlighet att erbjuda sina lösningar till Azure-kunder över hela världen.
Azure Marketplace kombinerar Microsoft Azure partner ekosystem till en enhetlig plattform för att bättre fungera kunder och partner. Du kan [köra en sökning](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) Visa databasen säkerhetsprodukter som är tillgängliga i Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

- [Säkra din Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center och Azure SQL Database-tjänsten](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Hotidentifiering för SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Förbättra prestanda för SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
