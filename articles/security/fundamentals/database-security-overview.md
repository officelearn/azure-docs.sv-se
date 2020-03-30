---
title: Säkerhetsöversikt för Azure-databasen| Microsoft-dokument
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
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906049"
---
# <a name="azure-database-security-overview"></a>Säkerhetsöversikt för Azure-databasen

Säkerhet är ett stort problem för att hantera databaser och det har alltid varit en prioritet för Azure SQL Database. Azure SQL Database stöder anslutningssäkerhet med brandväggsregler och anslutningskryptering. Den stöder autentisering med användarnamn och lösenord och Azure Active Directory (Azure AD) autentisering, som använder identiteter som hanteras av Azure Active Directory. Auktorisering använder rollbaserad åtkomstkontroll.

Azure SQL Database stöder kryptering genom att utföra kryptering i realtid och dekryptering av databaser, associerade säkerhetskopior och transaktionsloggfiler i vila utan att kräva ändringar i programmet.

Microsoft tillhandahåller ytterligare sätt att kryptera företagsdata:

-   Kryptering på cellnivå är tillgänglig för att kryptera specifika kolumner eller till och med dataceller med olika krypteringsnycklar.
-   Om du behöver en maskinvarusäkerhetsmodul eller central hantering av krypteringsnyckelhierarkin kan du använda Azure Key Vault med SQL Server i en virtuell Virtuell Azure-dator (VM).
-   Alltid krypterad (för närvarande i förhandsversion) gör kryptering transparent för program. Det gör det också möjligt för klienter att kryptera känsliga data i klientprogram utan att dela krypteringsnycklarna med SQL Database.

Azure SQL Database Auditing gör det möjligt för företag att registrera händelser i en granskningslogg i Azure Storage. SQL Database Auditing är integrerat med Microsoft Power BI för att underlätta ingående rapporter och analyser.

Azure SQL-databaser kan vara tätt skyddade för att uppfylla de flesta lagstadgade krav eller säkerhetskrav, inklusive HIPAA, ISO 27001/27002 och PCI DSS-nivå 1. En aktuell lista över certifieringar för säkerhetsefterlevnad finns på [webbplatsen Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/services/).

Den här artikeln går igenom grunderna för att skydda Microsoft Azure SQL-databaser för strukturerade, tabellmässiga och relationsdata. Den här artikeln i synnerhet, hjälper dig att komma igång med resurser för att skydda data, kontrollera åtkomst och proaktiv övervakning.

## <a name="protection-of-data"></a>Skydd av uppgifter

SQL Database hjälper till att skydda dina data genom att tillhandahålla kryptering:

- För data i rörelse via [TLS (Transport Layer Security).](https://support.microsoft.com/kb/3135244)
- För data i vila genom [transparent datakryptering](https://go.microsoft.com/fwlink/?LinkId=526242).
- För data som används via [Alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx).

För andra sätt att kryptera dina data, kan du överväga:

-   [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   [Azure Key Vault med SQL Server i en Virtuell Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), om du behöver en maskinvarusäkerhetsmodul eller central hantering av krypteringsnyckelhierarkin.

### <a name="encryption-in-motion"></a>Kryptering i rörelse

Ett vanligt problem för alla klient-serverprogram är behovet av sekretess när data flyttas över offentliga och privata nätverk. Om data som flyttar över ett nätverk inte är krypterade finns det en risk att den kan fångas och stjälas av obehöriga användare. När du arbetar med databastjänster kontrollerar du att data krypteras mellan databasklienten och servern. Kontrollera också att data krypteras mellan databasservrar som kommunicerar med varandra och med mellannivåprogram.

Ett problem när du administrerar ett nätverk är att skydda data som skickas mellan program i ett nätverk som inte är betrodda. Du kan använda [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna.

I autentiseringsprocessen skickar en TLS/SSL-klient ett meddelande till en TLS/SSL-server. Servern svarar med den information som servern behöver för att autentisera sig själv. Klienten och servern utför ytterligare ett utbyte av sessionsnycklar och autentiseringsdialogen avslutas. När autentiseringen är klar kan SSL-säker kommunikation påbörjas mellan servern och klienten via de symmetriska krypteringsnycklar som upprättas under autentiseringsprocessen.

Alla anslutningar till Azure SQL Database kräver kryptering (TLS/SSL) hela tiden medan data är "under överföring" till och från databasen. SQL Database använder TLS/SSL för att autentisera servrar och klienter och sedan använda den för att kryptera meddelanden mellan de autentiserade parterna. 

I programmets anslutningssträng måste du ange parametrar för att kryptera anslutningen och inte lita på servercertifikatet. (Detta görs för dig om du kopierar anslutningssträngen från Azure-portalen.) Annars kommer anslutningen inte att verifiera serverns identitet och kommer att vara mottagliga för "man-in-the-middle"-attacker. För den ADO.NET drivrutinen, till exempel, `Encrypt=True` dessa `TrustServerCertificate=False`anslutningssträngparametrar är och .

### <a name="encryption-at-rest"></a>Vilande kryptering

Du kan vidta flera försiktighetsåtgärder för att skydda databasen. Designa till exempel ett säkert system, kryptera konfidentiella resurser och skapa en brandvägg runt databasservrarna. Men i ett scenario där det fysiska mediet (till exempel enheter eller säkerhetskopieringsband) stjäls kan en skadlig part bara återställa eller bifoga databasen och bläddra bland data.

En lösning är att kryptera känsliga data i databasen och skydda nycklarna som används för att kryptera data med ett certifikat. Den här lösningen förhindrar att alla som inte har nycklarna använder data, men den här typen av skydd måste planeras.

För att lösa detta problem stöder SQL Server och SQL Database [transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparent datakryptering krypterar SQL Server- och SQL Database-datafiler, så kallade krypteringsdata i vila.

Transparent datakryptering hjälper till att skydda mot hotet om skadlig aktivitet. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet.  

Transparent datakryptering krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databaskrypteringsnyckeln. I SQL Database skyddas databaskrypteringsnyckeln av ett inbyggt servercertifikat. Det inbyggda servercertifikatet är unikt för varje SQL Database-server.

Om en databas är i en Geo-DR-relation skyddas den av en annan nyckel på varje server. Om två databaser är anslutna till samma server delar de samma inbyggda certifikat. Microsoft roterar automatiskt dessa certifikat minst var 90:e dag. 

Mer information finns i [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Kryptering som används (klient)

De flesta dataintrång innebär stöld av kritiska data, till exempel kreditkortsnummer eller personligt identifierbar information. Databaser kan vara guldgruvor av känslig information. De kan innehålla kundernas personuppgifter (som nationella identifikationsnummer), konfidentiell konkurrensinformation och immateriella rättigheter. Förlorade eller stulna data, särskilt kunddata, kan resultera i varumärkesskador, konkurrensnackdel och allvarliga böter – även stämningar.

![Funktionen Alltid krypterad illustrerad som ett lås och en nyckel](./media/database-security-overview/azure-database-fig1.png)

[Alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx) är en funktion som är utformad för att skydda känsliga data som lagras i Azure SQL Database- eller SQL Server-databaser. Alltid krypterad gör det möjligt för klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server).

Alltid krypterad ger en separation mellan personer som äger data (och kan visa den) och personer som hanterar data (men bör inte ha någon tillgång). Det hjälper till att säkerställa att lokala databasadministratörer, molndatabasoperatörer eller andra högprivilegierade men obehöriga användare inte kan komma åt krypterade data.

Dessutom gör Alltid krypterad kryptering transparent för program. En alltid krypterad drivrutin installeras på klientdatorn så att den automatiskt kan kryptera och dekryptera känsliga data i klientprogrammet. Drivrutinen krypterar data i känsliga kolumner innan data vidarebefordras till databasmotorn. Drivrutinen skriver automatiskt om frågor så att semantiken till programmet bevaras. På samma sätt dekrypterar drivrutinen transparent data, lagrade i krypterade databaskolumner, som finns i frågeresultat.

## <a name="access-control"></a>Åtkomstkontroll

Om du vill tillhandahålla säkerhet styr SQL Database åtkomsten med hjälp av:

- Brandväggsregler som begränsar anslutningen via IP-adress.
- Autentiseringsmekanismer som kräver att användare bevisar sin identitet.
- Auktoriseringsmekanismer som begränsar användare till specifika åtgärder och data.

### <a name="database-access"></a>Åtkomst till databasen

Dataskyddet börjar med att kontrollera åtkomsten till dina data. Det datacenter som är värd för dina data hanterar fysisk åtkomst. Du kan konfigurera en brandvägg för att hantera säkerheten i nätverkslagret. Du styr också åtkomsten genom att konfigurera inloggningar för autentisering och definiera behörigheter för server- och databasroller.

#### <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tillhandahåller en relationsdatabastjänst för Azure och andra internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](/azure/sql-database/sql-database-firewall-configure).

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. Om du vill komma åt en SQL-databas från datorn kontrollerar du att klientdatorbrandväggen tillåter utgående TCP-kommunikation på TCP-port 1433. Om inkommande anslutningar inte behövs för andra program blockerar du dem på TCP-port 1433.

#### <a name="authentication"></a>Autentisering

Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:

-   **SQL Server-autentisering:** Ett enda inloggningskonto skapas när en logisk SQL-instans skapas, så kallad SQL Database Subscriber Account. Det här kontot ansluter med HJÄLP AV [SQL Server-autentisering](/azure/sql-database/sql-database-security-overview) (användarnamn och lösenord). Kontot är administratör på den logiska server-instansen och på alla användardatabaser kopplade till denna instans. Behörigheterna för prenumerantkontot kan inte begränsas. Endast ett av dessa konton kan finnas.
-   **Azure Active Directory-autentisering:** [Azure AD-autentisering](/azure/sql-database/sql-database-aad-authentication) är en mekanism för anslutning till Azure SQL Database och Azure SQL Data Warehouse med hjälp av identiteter i Azure AD. Du kan använda den för att centralt hantera identiteter för databasanvändare.

![Azure AD-autentisering med SQL-databas](./media/database-security-overview/azure-database-fig2.png)

 Fördelarna med Azure AD-autentisering är:
  - Det ger ett alternativ till SQL Server-autentisering.
  - Det hjälper till att stoppa spridningen av användaridentiteter över databasservrar och tillåter lösenordsrotation på ett enda ställe.
  - Du kan hantera databasbehörigheter med hjälp av externa (Azure AD) grupper.
  - Det kan eliminera lagring av lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som Azure AD stöder.

#### <a name="authorization"></a>Auktorisering

[Auktorisering](/azure/sql-database/sql-database-manage-logins) refererar till vad en användare kan göra i en Azure SQL-databas. Den styrs av ditt användarkontos [databasrollmedlemskap](https://msdn.microsoft.com/library/ms189121) och [behörigheter på objektnivå](https://msdn.microsoft.com/library/ms191291.aspx). Auktorisering är processen att avgöra vilka värdepapperiseringsbara resurser som ett huvudnamn kan komma åt och vilka åtgärder som tillåts för dessa resurser.

### <a name="application-access"></a>Programåtkomst

#### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

En servicerepresentant på ett callcenter kan identifiera de som ringer med flera siffror i personnumret eller kreditkortsnumret. Men dessa dataposter bör inte vara helt exponerade för servicerepresentanten.

Du kan definiera en maskeringsregel som maskerar alla utom de fyra sista siffrorna i ett personnummer eller kreditkortsnummer i resultatuppsättningen för en fråga.

![Maskering på ett nummer som skickas mellan en SQL-databas och företagsappar](./media/database-security-overview/azure-database-fig3.png)

Som ett annat exempel kan en lämplig datamask definieras för att skydda personligt identifierbar information. En utvecklare kan sedan fråga produktionsmiljöer för felsökning utan att bryta mot efterlevnadsreglerna.

[Dynamisk datamaskering i SQL Database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig dataexponering genom att maskera den för användare som inte är privilegierade. Dynamisk datamaskering stöds för V12-versionen av Azure SQL Database.

[Dynamisk datamaskering](/sql/relational-databases/security/dynamic-data-masking) hjälper till att förhindra obehörig åtkomst till känsliga data genom att du kan ange hur mycket av känsliga data som ska visas med minimal påverkan på programlagret. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

> [!Note]
> Dynamisk datamaskering kan konfigureras av azure-databasadministratören, serveradministratören eller säkerhetsansvariga.

#### <a name="row-level-security"></a>Säkerhet på radnivå

Ett annat vanligt säkerhetskrav för databaser med flera innehavare är [säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131.aspx). Du kan använda den här funktionen för att styra åtkomsten till rader i en databastabell baserat på egenskaperna hos den användare som kör en fråga. (Exempelkännetecken är gruppmedlemskap och utförandekontext.)

![Säkerhet på radnivå som gör det möjligt för en användare att komma åt rader i en tabell via en klientapp](./media/database-security-overview/azure-database-fig4.png)

Logiken för åtkomstbegränsning finns på databasnivån i stället för bort från data på en annan programnivå. Databassystemet tillämpar åtkomstbegränsningarna varje gång dataåtkomsten görs från valfri nivå. Detta gör ditt säkerhetssystem mer tillförlitligt och robust genom att minska ytan på ditt säkerhetssystem.

Säkerhet på radnivå introducerar predikatbaserad åtkomstkontroll. Den har en flexibel, centraliserad utvärdering som kan ta hänsyn till metadata eller andra kriterier som administratören bestämmer på lämpligt sätt. Predikatet används som ett kriterium för att avgöra om användaren har rätt åtkomst till data baserat på användarattribut. Du kan implementera etikettbaserad åtkomstkontroll med hjälp av predikatbaserad åtkomstkontroll.

## <a name="proactive-monitoring"></a>Proaktiv övervakning

SQL Database hjälper till att skydda dina data genom att tillhandahålla *gransknings-* och *hotidentifieringsfunktioner.*

### <a name="auditing"></a>Granskning

[Granskning av Azure SQL Database](/azure/sql-database/sql-database-auditing-get-started) ökar din förmåga att få insikt i händelser och ändringar som sker i databasen. Exempel är uppdateringar och frågor mot data.

SQL Database-granskning spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktivitet och få insikt i avvikelser och avvikelser som kan tyda på affärsproblem eller misstänkta säkerhetsöverträdelser. Granskning möjliggör och underlättar efterlevnad av efterlevnadsstandarder men garanterar inte efterlevnad.

Du kan använda SQL Database-granskning för att:

- **Behåll** en granskningshistorik för valda händelser. Du kan definiera kategorier med databasåtgärder som ska granskas.
- **Rapport om** databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att snabbt komma igång med aktivitets- och händelserapportering.
- **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

Det finns två granskningsmetoder:

-   **Blob-granskning**: Loggar skrivs till Azure Blob-lagring. Det här är en nyare granskningsmetod. Det ger högre prestanda, stöder högre granularitet objektnivå granskning, och är mer kostnadseffektivt.
-   **Tabellgranskning**: Loggar skrivs till Azure Table storage.

### <a name="threat-detection"></a>Hotidentifiering

[Avancerat skydd mot hot för Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) identifierar misstänkta aktiviteter som indikerar potentiella säkerhetshot. Du kan använda hotidentifiering för att svara på misstänkta händelser i databasen, till exempel SQL-injektioner, när de inträffar. Den ger aviseringar och tillåter användning av Azure SQL Database-granskning för att utforska misstänkta händelser.

![Hotidentifiering för SQL Database och en webbapp, med en extern angripare och en skadlig insider](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive dataidentifiering & klassificering, sårbarhetsbedömning och hotidentifiering. 

- [Klassificering & & dataidentifiering](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Bedömning av sårbarhet](/azure/sql-database/sql-vulnerability-assessment)  
- [Identifiering av hot](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL Advanced Threat Protection](/azure/postgresql/concepts-data-access-and-security-threat-protection) ger ett nytt säkerhetslager som gör att du kan identifiera och svara på potentiella hot när de uppstår genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare får en avisering om misstänkta databasaktiviteter och potentiella sårbarheter, samt avvikande databasåtkomst och frågorsmönster. Avancerat skydd mot azure-databas för PostgreSQL integrerar aviseringar med Azure Security Center. Typ av aviseringar är:

- Åtkomst från ovanlig plats
- Åtkomst från ovanliga Azure-datacenter 
- Åtkomst från okänt huvudnamn 
- Åtkomst från ett potentiellt skadligt program 
- Brute force Azure-databas för PostgreSQL-autentiseringsuppgifter 

[Azure Database for MySQL Advanced Threat Protection](/azure/mysql/concepts-data-access-and-security-threat-protection) ger skydd som liknar PostgreSQL Advanced Protection.  

## <a name="centralized-security-management"></a>Centraliserad säkerhetshantering

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Det ger integrerad säkerhetsövervakning och principhantering över dina Azure-prenumerationer. Det hjälper till att upptäcka hot som annars skulle gå obemärkt förbi, och det fungerar med ett brett ekosystem av säkerhetslösningar.

[Security Center](../../security-center/security-center-alerts-data-services.md) hjälper dig att skydda data i SQL Database genom att ge insyn i säkerheten för alla dina servrar och databaser. Med Security Center kan du:

- Definiera principer för SQL Database-kryptering och granskning.
- Övervaka säkerheten för SQL Database-resurser för alla dina prenumerationer.
- Identifiera och åtgärda snabbt säkerhetsproblem.
- Integrera aviseringar från [azure SQL Database hotidentifiering](/azure/sql-database/sql-database-threat-detection).

Security Center stöder rollbaserad åtkomst.

## <a name="sql-information-protection"></a>SQL-informationsskydd

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) identifierar och klassificerar automatiskt potentiellt känsliga data, tillhandahåller en märkningsmekanism för att ständigt tagga känsliga data med klassificeringsattribut och tillhandahåller en detaljerad instrumentpanel som visar databasens klassificeringstillstånd.  

Dessutom beräknas resultatuppsättningens känslighet för SQL-frågor, så att frågor som extraherar känsliga data uttryckligen kan granskas och data kan skyddas. Mer information om SQL-informationsskydd finns i Azure SQL Database Data Discovery and Classification.

Du kan konfigurera [SQL Information Protection-principer](/azure/security-center/security-center-info-protection-policy) i Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace är en marknadsplats för onlineprogram och tjänster som gör det möjligt för nystartade företag och oberoende programvaruleverantörer (ISV: er) att erbjuda sina lösningar till Azure-kunder runt om i världen.
Azure Marketplace kombinerar Microsoft Azure-partnerekosystem till en enhetlig plattform för att bättre betjäna kunder och partner. Du kan [köra en sökning](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) för att visa databassäkerhetsprodukter som är tillgängliga på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

- [Skydda din Azure SQL-databas](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center och Azure SQL Database-tjänst](/azure/security-center/security-center-sql-database)
- [Identifiering av HOT I SQL Database](/azure/sql-database/sql-database-threat-detection)
- [Förbättra prestanda för SQL-databasen](/azure/sql-database/sql-database-performance-tutorial)
