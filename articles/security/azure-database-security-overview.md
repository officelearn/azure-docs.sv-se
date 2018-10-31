---
title: Översikt över Azure database-säkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure-databasen säkerhetsfunktioner.
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
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: a61f3572037b1c62ea5ed4e0ac4496b057e2b96d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249059"
---
# <a name="azure-database-security-overview"></a>Översikt över Azure database-säkerhet

Säkerhet är ett viktigt mål för hantering av databaser och den har alltid varit en prioritet för Azure SQL Database. Azure SQL Database stöder anslutningssäkerhet med brandväggsregler och krypterad anslutning. Det stöder autentisering med användarnamn och lösenord och Azure Active Directory (Azure AD)-autentisering, som använder identiteter som hanteras av Azure Active Directory. Auktorisering använder rollbaserad åtkomstkontroll.

Azure SQL Database stöder kryptering genom att utföra i realtid kryptering och dekryptering av databaser, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.

Microsoft tillhandahåller ytterligare sätt att kryptera företagsdata:

-   Kryptering på cellnivå är tillgänglig för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   Om du behöver en hardware security module eller central hantering av krypteringsnyckelns hierarki kan du använda Azure Key Vault med SQL Server i en Azure-dator (VM).
-   Alltid gör krypterad (för närvarande i förhandsversion) kryptering transparent för program. Det gör också klienter att kryptera känsliga data i klientprogram utan att dela krypteringsnycklarna med SQL Database.

Azure SQL Database-granskning kan företag post i en granskningslogg händelselogg i Azure Storage. SQL Database Auditing är integrerat med Microsoft Power BI för att underlätta ingående rapporter och analyser.

Azure SQL-databaser kan skyddas nära för att du uppfyller de flesta juridiska eller säkerhetskrav, inklusive HIPAA, ISO 27001/27002 och PCI DSS Level 1. En aktuell lista över security efterlevnadscertifieringar är tillgänglig på den [Microsoft Azure Trust Center plats](http://azure.microsoft.com/support/trust-center/services/).

Den här artikeln beskriver grunderna för att skydda Microsoft Azure SQL-databaser för strukturerade, tabular och relationella data. Den här artikeln i synnerhet, hjälper dig att komma igång med resurser för att skydda data, kontrollera åtkomst och proaktiv övervakning.

## <a name="protection-of-data"></a>Skydd av data

SQL Database skyddar dina data genom att tillhandahålla kryptering:

- För data i rörelse via [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- För data i vila med [transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242).
- För data som används via [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

För andra sätt att kryptera dina data, kan du överväga:

-   [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   [Azure Key Vault med SQL Server i en Azure VM](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)om du behöver en hardware security module eller central hantering av krypteringsnyckelns hierarki.

### <a name="encryption-in-motion"></a>Kryptering i rörelse

Ett vanligt problem för alla klient/server-program är behovet av sekretess eftersom data flyttas via offentliga och privata nätverk. Om data som flyttas över ett nätverk inte är krypterade, finns en risk att den kan skapas och blir stulen av obehöriga användare. När du hanterar databastjänster, se till att data krypteras mellan databasen klienten och servern. Kontrollera också att data krypteras mellan database-servrar som kommunicerar med varandra och med mellannivå-program.

Ett problem när du administrerar ett nätverk är att säkra data som skickas mellan program över ett osäkrat nätverk. Du kan använda [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna.

I autentiseringsprocessen skickar TLS/SSL-klienten ett meddelande till en TLS/SSL-server. Servern svarar med den information som servern måste autentisera sig själv. Klienten och servern utför ytterligare ett utbyte av sessionsnycklar och autentiseringsdialogen avslutas. När autentiseringen är slutförd kan startas SSL-skyddad kommunikation mellan servern och klienten via de symmetriska krypteringsnycklar som fastställts under autentiseringsprocessen.

Alla anslutningar till Azure SQL Database kräver kryptering (TLS/SSL) på hela tiden medan data är ”under överföringen” till och från databasen. SQL Database använder TLS/SSL för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna. 

I anslutningssträngen för ditt program, måste du ange parametrar att kryptera anslutningen och inte lita på servercertifikatet. (Detta görs för dig om du kopierar anslutningssträngen utanför Azure-portalen.) Annars kan anslutningen kommer inte att verifiera identiteten för servern och kommer vara sårbar för ”man-in-the-middle”-attacker. ADO.NET-drivrutinen exempelvis dessa parametrar för anslutningssträngen är `Encrypt=True` och `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Vilande kryptering

Du kan ta flera försiktighetsåtgärder för att skydda databasen. Till exempel att skapa säkra system, kryptera känsliga tillgångar och skapa en brandvägg runt database-servrar. Men i ett scenario där det fysiska mediet (till exempel enheter eller band) blir stulna, en obehörig part kan bara återställa eller koppla databasen och bläddra efter data.

En lösning är att kryptera känsliga data i databasen och skydda nycklar som används för att kryptera data med ett certifikat. Den här lösningen förhindrar att någon utan nycklar med hjälp av data, men den här typen av skydd måste planeras.

Du kan lösa det här problemet, SQL Server och SQL Database stöd [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparent datakryptering krypteras SQL Server och SQL Database-datafiler, kallas krypteringsdata i vila.

Transparent datakryptering skyddar mot skadlig aktivitet. Den utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.  

Transparent datakryptering krypteras lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln är skyddat av ett certifikat för inbyggd i SQL-databas. Inbyggda servercertifikatet är unikt för varje SQL Database-server.

Om en databas är i en Geo-DR-relation, är den skyddad med en annan nyckel på varje server. Om två databaser är ansluten till samma server kan dela de samma inbyggda certifikat. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. 

Mer information finns i [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Kryptering används (klient)

De flesta dataintrång omfattar stöld av viktiga data, till exempel kreditkortsnummer eller personligt identifierbar information. Databaser kan vara värdefull troves av känslig information. De kan innehålla kunders personliga data (till exempel nationella identifikationsnummer), konfidentiell information som konkurrenskraftiga och immateriell egendom. Förlorad eller stulen data, särskilt kunddata, kan resultera i varumärke skador och konkurrenskraftiga nackdel allvarligt böter--även rättstvister.

![Always Encrypted-funktionen visas som ett lås och nyckel](./media/azure-databse-security-overview/azure-database-fig1.png)

[Alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx) är en funktion som utformats för att skydda känsliga data som lagras i Azure SQL Database eller SQL Server-databaser. Always kan Encrypted klienter kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server).

Always ger Encrypted en åtskillnad mellan personer som äger data (och kan visa den) och personer som hanterar data (men inte har åtkomsträttigheter). Det säkerställer att den lokala databasen administratörer, molnoperatörer för databasen eller andra privilegierad men obehöriga användare kan inte komma åt krypterade data.

Dessutom gör Always Encrypted kryptering transparent för program. En drivrutin för Always Encrypted-aktiverade är installerad på klientdatorn så att den automatiskt kan kryptera och dekryptera känsliga data i klientprogrammet. Drivrutinen krypteras data i känsliga kolumnerna innan data skickas till databasmotorn. Drivrutinen automatiskt skriver om frågor så att semantik för programmet bevaras. Drivrutinen på samma sätt kan dekrypterar transparent data som lagras i krypterade databaskolumner som ingår i frågeresultatet.

## <a name="access-control"></a>Åtkomstkontroll

För att ge säkerhet, kontrollerar SQL Database åtkomsten med hjälp av:

- Brandväggsregler som begränsar anslutning efter IP-adress.
- Autentiseringsmekanismer som kräver att användare bevisar sin identitet.
- Auktoriseringsmekanismer som begränsar användare till specifika åtgärder och data.

### <a name="database-access"></a>Databasåtkomst

Dataskydd börjar med att styra åtkomst till dina data. Datacentret som är värd för dina data hanterar fysisk åtkomst. Du kan konfigurera en brandvägg för att hantera säkerhet på nätverksnivå. Du kan också styra åtkomst genom att konfigurera inloggningar för autentisering och definiera behörigheter för servern och databasen roller.

#### <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Azure SQL Database-tjänsten är bara tillgänglig via TCP-port 1433. Se till att klientdatorns brandvägg tillåter utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL-databas från datorn. Om inkommande anslutningar inte krävs för andra program, blockera dem på TCP-port 1433.

#### <a name="authentication"></a>Autentisering

Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:

-   **SQL Server-autentisering**: ett enskilt inloggningskonto skapas när en logisk SQL-instans skapas, kallas SQL Database-prenumerationskonto. Det här kontot ansluter med hjälp av [SQL Server-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (användarnamn och lösenord). Kontot är administratör på den logiska server-instansen och på alla användardatabaser kopplade till denna instans. Behörigheterna för prenumerationskontot kan inte begränsas. Endast ett av dessa konton kan finnas.
-   **Azure Active Directory-autentisering**: [Azure AD-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) är en mekanism för att ansluta till Azure SQL Database och Azure SQL Data Warehouse med hjälp av identiteter i Azure AD. Du kan använda den för att centralt hantera identiteter för databasanvändare.

![Azure AD-autentisering med SQL Database](./media/azure-databse-security-overview/azure-database-fig2.png)

 Fördelarna med Azure AD-autentisering är:
  - Det är ett alternativ till SQL Server-autentisering.
  - Det hjälper till att stoppa spridning av användaridentiteter över databasservrar och tillåter lösenordsrotation i en och samma plats.
  - Du kan hantera databasbehörigheter med hjälp av externa (Azure AD)-grupper.
  - Men kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som har stöd för Azure AD.

#### <a name="authorization"></a>Auktorisering

[Auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) hänvisar till vad en användare kan göra inom en Azure SQL-databas. Det styrs av ditt användarkontos databas [rollmedlemskap](https://msdn.microsoft.com/library/ms189121) och [objektnivå behörigheter](https://msdn.microsoft.com/library/ms191291.aspx). Auktorisering är processen att avgöra vilken skyddsbara resurser som ett huvudkonto kan komma åt och vilka åtgärder som tillåts för dessa resurser.

### <a name="application-access"></a>Programåtkomst

#### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

En representant på ett Callcenter kan identifiera anropare genom flera siffrorna i sina e-postadress eller kreditkortsnummer. Men de dataobjekt bör inte exponeras fullständigt för servicerepresentant.

Du kan definiera en maskningsregel som döljer alla utom de sista fyra siffrorna i ett personnummer eller kreditkortsnummer i resultatet av en fråga.

![Maskera på ett tal som skickas mellan en SQL-databas och företag appar](./media/azure-databse-security-overview/azure-database-fig3.png)

Ett annat exempel kan du definiera en lämplig data-mask för att skydda personligt identifierbar information. Utvecklare kan sedan söka i produktionsmiljöer för felsökning utan brott mot kompatibilitetsföreskrifter.

[SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk datamaskning stöds för V12-versionen av Azure SQL Database.

[Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) hjälper till att förhindra obehörig åtkomst till känsliga data genom att ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

> [!Note]
> Dynamisk datamaskning kan konfigureras med Azure Database-administratör, serveradministratör eller officer säkerhetsroller.

#### <a name="row-level-security"></a>Säkerhet på radnivå

Ett annat gemensamma säkerhetskrav för flera innehavare databaser är [säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131.aspx). Du kan använda den här funktionen för att styra åtkomsten till rader i en databastabell baserat på egenskaperna för den användare som kör en fråga. (Exempel egenskaper är gruppen medlemskap och körning kontexten.)

![Säkerhet på radnivå som tillåter en användare att åtkomst rader i en tabell med en klientapp](./media/azure-databse-security-overview/azure-database-fig4.png)

Logiken för begränsning av är placerad på databasnivån i stället för in från data i en annan programnivå. Databasen tillämpas åtkomstbegränsningarna varje gång den dataåtkomsten görs från valfri nivå. Detta gör att säkerhetssystemet mer tillförlitlig och stabil genom att minska ytan systemets säkerhet.

Säkerhet på radnivå introducerar predikat-baserad åtkomstkontroll. Den har en flexibel centraliserad utvärderingsversion som kan ta med i beräkningen metadata eller andra kriterier som administratören anger efter behov. Predikatet används som ett villkor för att avgöra om användaren har lämplig åtkomst till data baserat på användarattribut. Du kan implementera etikett-baserad åtkomstkontroll genom att använda predikat-baserad åtkomstkontroll.

## <a name="proactive-monitoring"></a>Proaktiv övervakning

SQL-databas kan du skydda dina data genom att tillhandahålla *granskning* och *hotidentifiering* funktioner.

### <a name="auditing"></a>Granskning

[Azure SQL Database-granskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) ökar dina möjligheter att få insyn i händelser och ändringar som sker i databasen. Exempel är uppdateringar och frågor mot data.

SQL-databasgranskning spårar databashändelser och skriver dem till en granskningslogg i ditt Azure storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser. Granskning kan och underlättar infört efterlevnadsstandarder men garanterar inte efterlevnad.

Du kan använda SQL Database auditing till att:

- **Behåll** granskningsspårning av valda händelser. Du kan definiera kategorier av databasåtgärder som ska granskas.
- **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktiviteten och rapportera händelser.
- **Analysera** rapporter. Du hittar misstänkta händelser, ovanliga aktiviteter och trender.

Det finns två metoder för granskning:

-   **Blobbgranskning**: loggarna skrivs till Azure Blob storage. Det här är en metod för senare granskning. Det ger högre prestanda, stöder högre kornighet på objektnivå granskning och kostnadseffektivt alternativ mer.
-   **Tabellgranskning**: loggarna skrivs till Azure Table storage.

### <a name="threat-detection"></a>Hotidentifiering

[Avancerat skydd för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) identifierar misstänkta aktiviteter som kan innebära potentiella säkerhetshot. Du kan använda hotidentifiering för att reagera på misstänkta händelser i databasen, som SQL-injektioner, när de inträffar. Det ger aviseringar och tillåter användning av Azure SQL Database-granskning för att utforska misstänkta händelser.

![Hotidentifiering för SQL Database och en webbapp med en extern angripare och en](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL avancerade Threat Protection (ATP) tillhandahåller en uppsättning avancerade funktioner för SQL security, inklusive Dataidentifiering och klassificering, Sårbarhetsbedömning och Hotidentifiering. 

- [Dataidentifiering och klassificering](../sql-database/sql-database-data-discovery-and-classification.md)
- [Utvärdering av säkerhetsrisker](../sql-database/sql-vulnerability-assessment.md)  
- [Identifiering av hot](../sql-database/sql-database-threat-detection.md)

[Azure Database for PostgreSQL Advanced Threat Protection](../postgresql/concepts-data-access-and-security-threat-protection.md) ger ett nytt lager av säkerhet som gör det möjligt att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användarna får en avisering när misstänkta databasaktiviteter, potentiella sårbarheter, samt och avvikande mönster för åtkomst och frågor. Avancerat skydd för Azure Database for PostgreSQL integrerar aviseringar med Azure Security Center. Typ av aviseringar är:

- Åtkomst från ovanlig plats
- Åtkomst från ovanligt Azure-Datacenter 
- Åtkomst från okänd huvudnamn 
- Åtkomst från ett potentiellt skadligt program 
- Brute force Azure database för PostgreSQL-autentiseringsuppgifter 

[Azure Database for MySQL Advanced Threat Protection](/mysql/concepts-data-access-and-security-threat-protection.md) skyddar liknar PostgreSQL Avancerat skydd.  

## <a name="centralized-security-management"></a>Centraliserad säkerhetshantering

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer. Det hjälper dig att identifiera hot som kan annars oupptäckta och de fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) kan du skydda data i SQL-databas genom att ge bättre inblick om säkerheten för dina servrar och databaser. Med Security Center kan du:

- Definiera principer för SQL Database-kryptering och granskning.
- Övervaka säkerheten i SQL Database-resurser för alla dina prenumerationer.
- Snabbt identifiera och åtgärda säkerhetsproblem.
- Integrera varningar från [Azure SQL Database-hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Security Center stöder rollbaserad åtkomst.

## <a name="sql-information-protection"></a>SQL-informationsskydd

[SQL-informationsskydd](../sql-database/sql-database-data-discovery-and-classification.md) automatiskt identifierar och klassificerar potentiellt känsliga data, tillhandahåller en mekanism för etikettering för beständigt taggar känsliga data med klassificering attribut och ger en detaljerad instrumentpanel som visar klassificering tillståndet för databasen.  

Dessutom beräknas resultatet känslighet för SQL-frågor, så att frågor som hämtar känsliga data kan granskas uttryckligen och data kan skyddas. Mer information om SQL Information Protection finns i Azure SQL Database-Dataidentifiering och klassificering.

Du kan konfigurera [SQL Information Protection-principer](/security-center/security-center-info-protection-policy.md) i Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace är en onlinemarknadsplats för program och tjänster där nystartade företag och oberoende programvaruleverantörer (ISV:er) har möjlighet att erbjuda sina lösningar till Azure-kunder över hela världen.
Azure Marketplace kombinerar Microsoft Azures partnerekosystem till en enhetlig plattform för att bättre fungera kunder och partner. Du kan [kör en sökning](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) att visa databasen säkerhetsprodukter som är tillgängliga på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

- [Skydda din Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center och Azure SQL Database-tjänsten](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Hotidentifieringen i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Förbättra prestanda för SQL-databasen](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
