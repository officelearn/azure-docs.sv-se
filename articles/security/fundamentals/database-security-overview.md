---
title: Översikt över Azure Database-säkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över säkerhetsfunktionerna i Azure-databasen.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 9053866b76ec1cc409c3f00d0af59bf3761d3936
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927987"
---
# <a name="azure-database-security-overview"></a>Översikt över Azure Database-säkerhet

Säkerhet är viktigt för att hantera databaser och har alltid varit en prioritet för Azure SQL Database. Azure SQL Database stöder anslutnings säkerhet med brand Väggs regler och anslutnings kryptering. Det stöder autentisering med användar namn och lösen ord och Azure Active Directory (Azure AD)-autentisering, som använder identiteter som hanteras av Azure Active Directory. Auktorisering använder rollbaserad åtkomst kontroll.

Azure SQL Database stöder kryptering genom att utföra kryptering och dekryptering i real tid av databaser, tillhör ande säkerhets kopior och transaktionsloggfiler i vila utan att det krävs några ändringar i programmet.

Microsoft tillhandahåller ytterligare sätt att kryptera företags data:

-   Kryptering på cell nivå är tillgängligt för att kryptera vissa kolumner eller till och med data celler med olika krypterings nycklar.
-   Om du behöver en modul för maskin varu säkerhet eller en central hantering av din-hierarki för krypterings nyckel bör du överväga att använda Azure Key Vault med SQL Server i en virtuell Azure-dator (VM).
-   Always Encrypted (för närvarande i för hands version) gör krypteringen transparent för program. Det gör det också möjligt för klienter att kryptera känsliga data i klient program utan att dela krypterings nycklarna med SQL Database.

Azure SQL Database granskning gör det möjligt för företag att registrera händelser till en Gransknings logg i Azure Storage. SQL Database Auditing är integrerat med Microsoft Power BI för att underlätta ingående rapporter och analyser.

Azure SQL-databaser kan vara tätt säkra för att uppfylla de flesta lagar eller säkerhets krav, inklusive HIPAA, ISO 27001/27002 och PCI DSS nivå 1. En aktuell lista över certifieringar för säkerhetskompatibilitet finns på [webbplatsen för Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/services/).

Den här artikeln vägleder dig genom grunderna för att skydda Microsoft Azure SQL-databaser för strukturerade, tabell-och Relations data. Den här artikeln i synnerhet, hjälper dig att komma igång med resurser för att skydda data, kontrollera åtkomst och proaktiv övervakning.

## <a name="protection-of-data"></a>Skydd av data

SQL Database hjälper till att skydda dina data genom att tillhandahålla kryptering:

- För data i rörelse genom [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- För data i vila genom [transparent data kryptering](https://go.microsoft.com/fwlink/?LinkId=526242).
- För data som används via [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

För andra sätt att kryptera dina data, kan du överväga:

-   [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   [Azure Key Vault med SQL Server i en virtuell Azure-dator](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), om du behöver en modul för maskin varu säkerhet eller en central hantering av din nyckel hierarki för kryptering.

### <a name="encryption-in-motion"></a>Kryptering i rörelse

Ett vanligt problem för alla klient/server-program är behovet av sekretess när data flyttas över offentliga och privata nätverk. Om data som flyttas över ett nätverk inte är krypterade, finns det en risk att det kan fångas och stjäls av obehöriga användare. Se till att data är krypterade mellan databas klienten och servern när du arbetar med databas tjänster. Se också till att data är krypterade mellan databas servrar som kommunicerar med varandra och med program på mellan nivå.

Ett problem när du administrerar ett nätverk är att säkra data som skickas mellan program över ett icke-betrott nätverk. Du kan använda [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna.

I autentiseringsprocessen skickar TLS/SSL-klienten ett meddelande till en TLS/SSL-Server. Servern svarar med den information som servern behöver för att autentisera sig själv. Klienten och servern utför ytterligare ett utbyte av sessionsnycklar, och dialog rutan för autentisering upphör. När autentiseringen är klar kan SSL-skyddad kommunikation starta mellan servern och klienten via de symmetriska krypterings nycklar som upprättas under autentiseringsprocessen.

Alla anslutningar till Azure SQL Database Kräv kryptering (TLS/SSL) alltid när data är "under överföring" till och från databasen. SQL Database använder TLS/SSL för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna. 

I programmets anslutnings sträng måste du ange parametrar för att kryptera anslutningen och inte lita på Server certifikatet. (Detta görs för dig om du kopierar anslutnings strängen från Azure Portal.) Annars kommer anslutningen inte att verifiera serverns identitet och kommer att vara sårbar för angrepp med "man-in-the-Middle". För ADO.net-drivrutinen, till exempel, är `Encrypt=True` dessa parametrar för anslutnings strängen och. `TrustServerCertificate=False`

### <a name="encryption-at-rest"></a>Vilande kryptering

Du kan vidta flera försiktighets åtgärder för att skydda databasen. Du kan till exempel utforma ett säkert system, Kryptera konfidentiella till gångar och bygga en brand vägg runt databas servrarna. Men i ett scenario där det fysiska mediet (t. ex. enheter eller säkerhets kopierings band) blir stulna kan en angripare bara återställa eller koppla databasen och bläddra i data.

En lösning är att kryptera känsliga data i-databasen och skydda nycklar som används för att kryptera data med ett certifikat. Den här lösningen förhindrar att någon utan nycklarna använder data, men den här typen av skydd måste planeras.

För att lösa det här problemet, SQL Server och SQL Database stöd för [transparent data kryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparent data kryptering krypterar SQL Server och SQL Database datafiler, så kallade krypterings data i vila.

Transparent data kryptering skyddar mot hot från skadlig aktivitet. Den utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler i vila utan att det krävs några ändringar i programmet.  

Transparent data kryptering krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databas krypterings nyckel. I SQL Database skyddas databas krypterings nyckeln av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje SQL Database Server.

Om en databas finns i en geo-DR-relation skyddas den av en annan nyckel på varje server. Om två databaser är anslutna till samma server, delar de samma inbyggda certifikat. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. 

Mer information finns i [transparent data kryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Kryptering används (klient)

De flesta data överträdelser innebär stöld av kritiska data som kreditkorts nummer eller personligt identifierbar information. Databaser kan vara troves av känslig information. De kan innehålla kunders personliga data (t. ex. nationella identifierings nummer), konfidentiell information och immateriell egendom. Borttappade eller stulna data, särskilt kund information, kan leda till varumärkes skada, konkurrens nack delar och allvarliga böter – till och med rättegångar.

![Always Encrypted funktionen illustreras som ett lås och en nyckel](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) är en funktion som har utformats för att skydda känsliga data som lagras i Azure SQL Database eller SQL Server databaser. Always Encrypted tillåter klienter att kryptera känsliga data i klient program och aldrig avslöja krypterings nycklarna till databas motorn (SQL Database eller SQL Server).

Always Encrypted ger en separation mellan personer som äger data (och kan visa det) och personer som hanterar data (men inte har åtkomst). Det hjälper till att säkerställa att lokala databas administratörer, moln databas operatörer eller andra privilegierade användare, men obehöriga användare inte kan komma åt krypterade data.

Dessutom gör Always Encrypted kryptering transparent för program. En Always Encrypted-aktiverad driv rutin installeras på klient datorn så att den automatiskt kan kryptera och dekryptera känsliga data i klient programmet. Driv rutinen krypterar data i känsliga kolumner innan data skickas till databas motorn. Driv rutinen skriver automatiskt om frågor så att semantiken till programmet bevaras. På samma sätt dekrypterar driv rutinen data som lagras i krypterade databas kolumner, som finns i frågeresultaten.

## <a name="access-control"></a>Åtkomstkontroll

SQL Database kontrollerar åtkomsten genom att använda:

- Brand Väggs regler som begränsar anslutningarna med IP-adress.
- Autentiseringsmekanismer som kräver att användare bekräftar sin identitet.
- Auktoriserings metoder som begränsar användare till vissa åtgärder och data.

### <a name="database-access"></a>Databasåtkomst

Data skydd börjar med att kontrol lera åtkomsten till dina data. Data centret som är värd för dina data hanterar fysisk åtkomst. Du kan konfigurera en brand vägg för att hantera säkerhet på nätverks lagret. Du styr också åtkomst genom att konfigurera inloggningar för autentisering och definiera behörigheter för Server-och databas roller.

#### <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tillhandahåller en Relations databas tjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](/azure/sql-database/sql-database-firewall-configure).

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. För att få åtkomst till en SQL-databas från datorn måste du kontrol lera att klient datorns brand vägg tillåter utgående TCP-kommunikation på TCP-port 1433. Om inga inkommande anslutningar behövs för andra program blockerar du dem på TCP-port 1433.

#### <a name="authentication"></a>Authentication

Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:

-   **SQL Server autentisering**: Ett enskilt inloggningskonto skapas när en logisk SQL-instans skapas, med namnet SQL Database-prenumerationskonto. Det här kontot ansluter med [SQL Server autentisering](/azure/sql-database/sql-database-security-overview) (användar namn och lösen ord). Kontot är administratör på den logiska server-instansen och på alla användardatabaser kopplade till denna instans. Behörigheterna för prenumerations kontot kan inte begränsas. Endast ett av dessa konton kan finnas.
-   **Azure Active Directory autentisering**: [Azure AD-autentisering](/azure/sql-database/sql-database-aad-authentication) är en mekanism för att ansluta till Azure SQL Database och Azure SQL Data Warehouse med hjälp av identiteter i Azure AD. Du kan använda den för att centralt hantera identiteter för databas användare.

![Azure AD-autentisering med SQL Database](./media/database-security-overview/azure-database-fig2.png)

 Fördelarna med Azure AD-autentisering är:
  - Det är ett alternativ till att SQL Server autentisering.
  - Det hjälper till att stoppa spridningen av användar identiteter mellan databas servrar och tillåter lösen ords rotation på en enda plats.
  - Du kan hantera databas behörigheter med hjälp av externa (Azure AD) grupper.
  - Det kan eliminera lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure AD.

#### <a name="authorization"></a>Authorization

[Auktorisering](/azure/sql-database/sql-database-manage-logins) syftar på vad en användare kan göra inom en Azure SQL-databas. Det styrs av ditt användar kontos databas [roll medlemskap](https://msdn.microsoft.com/library/ms189121) och [behörigheter på objekt nivå](https://msdn.microsoft.com/library/ms191291.aspx). Auktorisering är en process för att fastställa vilka skydds bara resurser som ett huvud konto kan komma åt och vilka åtgärder som tillåts för dessa resurser.

### <a name="application-access"></a>Programåtkomst

#### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

En service representant i ett Call Center kan identifiera anropare med flera siffror i social security number eller kreditkorts nummer. Men dessa data objekt bör inte vara helt exponerade för tjänst representanten.

Du kan definiera en masknings regel som maskerar alla utom de sista fyra siffrorna i ett social security number eller kreditkorts nummer i resultat uppsättningen för en fråga.

![Maskering av ett tal som skickas mellan en SQL-databas och företags-appar](./media/database-security-overview/azure-database-fig3.png)

Ett annat exempel är att en lämplig data mask kan definieras för att skydda personligt identifierbar information. En utvecklare kan sedan fråga produktions miljöer i fel söknings syfte utan att överskrida reglerna för efterlevnad.

[SQL Database dynamisk datamaskning](/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk data maskning stöds för V12-versionen av Azure SQL Database.

[Dynamisk](/sql/relational-databases/security/dynamic-data-masking) datamaskering bidrar till att förhindra obehörig åtkomst till känsliga data genom att göra det möjligt att ange hur mycket av känsliga data som ska visas med minimal påverkan på program nivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

> [!Note]
> Dynamisk datamaskering kan konfigureras av rollerna Azure Database-administratör, Server administratör eller säkerhets chef.

#### <a name="row-level-security"></a>Säkerhet på radnivå

Ett annat vanligt säkerhets krav för flera klient databaser är [säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131.aspx). Du kan använda den här funktionen för att kontrol lera åtkomsten till rader i en databas tabell baserat på egenskaperna för användaren som kör en fråga. (Exempel på egenskaper är grupp medlemskap och körnings kontext.)

![Säkerhet på radnivå som gör att en användare får åtkomst till rader i en tabell via en klient app](./media/database-security-overview/azure-database-fig4.png)

Logiken för åtkomst begränsning finns i databas nivån i stället för bort från data i en annan program nivå. Databas systemet tillämpar åtkomst begränsningar varje gång som data åtkomsten görs från vilken nivå som helst. Detta gör ditt säkerhets system mer tillförlitligt och robust genom att minska arbets ytan i säkerhets systemet.

Säkerhet på radnivå inför en predikat åtkomst kontroll. Den innehåller en flexibel, centraliserad utvärderings version som kan ta hänsyn till metadata eller andra villkor som administratören bestämmer efter behov. Predikatet används som ett villkor för att avgöra om användaren har lämplig åtkomst till data baserat på användarattribut. Du kan implementera etikettbaserad åtkomst kontroll genom att använda predikat-baserad åtkomst kontroll.

## <a name="proactive-monitoring"></a>Proaktiv övervakning

SQL Database skyddar dina data genom att tillhandahålla funktioner för *granskning* och *hot identifiering* .

### <a name="auditing"></a>Granskning

[Azure SQL Database granskning](/azure/sql-database/sql-database-auditing-get-started) ökar din möjlighet att få inblick i händelser och ändringar som sker i databasen. Exempel är uppdateringar och frågor mot data.

SQL Database granskning spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databas aktivitet och få insikt i avvikelser och avvikelser som kan tyda på affärs problem eller misstänkta säkerhets överträdelser. Granskning möjliggör och underlättar efterlevnad av efterlevnads standarder men garanterar inte efterlevnad.

Du kan använda SQL Database granskning för att:

- **Behåll** en gransknings historik för markerade händelser. Du kan definiera kategorier av databas åtgärder som ska granskas.
- **Rapport** om databas aktivitet. Du kan använda förkonfigurerade rapporter och en instrument panel för att komma igång snabbt med aktivitet och händelse rapportering.
- **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

Det finns två gransknings metoder:

-   **BLOB-granskning**: Loggarna skrivs till Azure Blob Storage. Detta är en senare gransknings metod. Den ger högre prestanda, stöder granskning på objekt nivå med högre precision och är mer kostnads effektivt.
-   **Tabell granskning**: Loggarna skrivs till Azure Table Storage.

### <a name="threat-detection"></a>Identifiering av hot

[Avancerat skydd för Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) identifierar misstänkta aktiviteter som indikerar potentiella säkerhetshot. Du kan använda hot identifiering för att svara på misstänkta händelser i databasen, till exempel SQL-injektering, när de inträffar. Den innehåller aviseringar och gör det möjligt att använda Azure SQL Database granskning för att utforska misstänkta händelser.

![Hot identifiering för SQL Database och en webbapp, med en extern angripare och en skadlig Insider](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive data identifiering & klassificering, sårbarhets bedömning och hot identifiering. 

- [Klassificering av data identifierings &](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Sårbarhetsbedömning](/azure/sql-database/sql-vulnerability-assessment)  
- [Hotidentifiering](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL Avancerat skydd](/azure/postgresql/concepts-data-access-and-security-threat-protection) ger ett nytt säkerhets lager som gör att du kan identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter. Användare får en avisering om misstänkt databas aktiviteter och potentiella sårbarheter, samt mönster för avvikande databas åtkomst och frågor. Avancerat skydd för Azure Database for PostgreSQL integrerar aviseringar med Azure Security Center. Typ av aviseringar är:

- Åtkomst från ovanlig plats
- Åtkomst från ovanliga Azure-datacenter 
- Åtkomst från okända huvud konton 
- Åtkomst från ett potentiellt skadligt program 
- Bruten tvinga Azure Database för PostgreSQL-autentiseringsuppgifter 

[Azure Database for MySQL Avancerat](/azure/mysql/concepts-data-access-and-security-threat-protection) skydd ger skydd som liknar postgresql Avancerat skydd.  

## <a name="centralized-security-management"></a>Centraliserad säkerhets hantering

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Den ger integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer. Den hjälper till att upptäcka hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

[Security Center](../../security-center/security-center-sql-database.md) hjälper dig att skydda data i SQL Database genom att ge insyn i säkerheten för alla dina servrar och databaser. Med Security Center kan du:

- Definiera principer för SQL Database kryptering och granskning.
- Övervaka säkerheten för SQL Database resurser i alla dina prenumerationer.
- Identifiera och åtgärda säkerhets problem snabbt.
- Integrera aviseringar från [Azure SQL Database hot identifiering](/azure/sql-database/sql-database-threat-detection).

Security Center stöder rollbaserad åtkomst.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL-information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) identifierar och klassificerar automatiskt potentiellt känsliga data, ger en etikett funktion för beständig märkning av känsliga data med klassificerings attribut, och innehåller en detaljerad instrument panel som visar databasens klassificerings tillstånd.  

Dessutom beräknar den resultat uppsättnings känslighet för SQL-frågor, så att frågor som extraherar känsliga data kan granskas explicit och att data kan skyddas. Mer information om SQL Information Protection finns i Azure SQL Database identifiering och klassificering av data.

Du kan konfigurera [SQL information Protection-principer](/azure/security-center/security-center-info-protection-policy) i Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace är en onlinemarknadsplats för program och tjänster där nystartade företag och oberoende programvaruleverantörer (ISV:er) har möjlighet att erbjuda sina lösningar till Azure-kunder över hela världen.
Azure Marketplace kombinerar Microsoft Azure partner eko system till en enhetlig plattform för att bättre betjäna kunder och partner. Du kan [köra en sökning](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) för att visa databas säkerhets produkter som är tillgängliga på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

- [Skydda din Azure SQL-databas](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center-och Azure SQL Database-tjänsten](/azure/security-center/security-center-sql-database)
- [SQL Database hot identifiering](/azure/sql-database/sql-database-threat-detection)
- [Förbättra prestanda för SQL Database](/azure/sql-database/sql-database-performance-tutorial)
