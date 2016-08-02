<properties
    pageTitle="Översikt över Stretch Database | Microsoft Azure"
    description="Läs om hur Stretch Database migrerar din historiska data transparent och säkert till Microsoft Azure-molnet."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager=""
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Översikt över Stretch Database

Stretch Database migrerar din historiska data transparent och säkert till Microsoft Azure-molnet.

Om du vill komma igång med Stretch Database direkt, se [Kom igång genom att köra guiden aktivera databas för Stretch](sql-server-stretch-database-wizard.md).

## Vad är fördelarna med Stretch Database?
Stretch Database ger följande fördelar:

**Ger kostnads\-effektiv tillgänglighet för kalldata**. Sträck ut varm eller kall transaktionsdata dynamiskt från SQL Server till Microsoft Azure med SQL Server Stretch Database. Till skillnad från vanlig lagring av kalldata, är dina data alltid online och tillgängliga att fråga. Du kan ange längre kvarhållning av data utan att gå över budget för stora tabeller som kundorderhistorik. Dra nytta av den låga kostnaden i Azure istället för att skala dyr \-lokal lagring. Du väljer prisnivå och konfigurerar inställningarna i Azure Portal för att behålla kontroll över kostnaderna. Skala upp eller ned efter behov. Besök sidan [Priser för SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) för information.

**Kräver inga ändringar av frågor eller program** Ha åtkomst till din SQL Serverdata sömlöst oavsett om den är \-lokal eller utsträckt till molnet.  Du anger den princip som bestämmer var data lagras och SQL Server hanterar dataflytten i bakgrunden. Hela tabellen finns alltid online och redo för frågor. Stretch Database kräver inga förändringar av befintliga frågor eller program. Dataplatsen är fullständigt transparent för programmet.

**Effektiviserar \-lokalt dataunderhåll** Minska \-lokalt underhåll och lagring för din data. Säkerhetskopiering av molndelen av dina data körs automatiskt. Säkerhetskopieringar för dina \-lokala data körs snabbare och slutförs inom underhållsfönstret. Dina \-lokala lagringsbehov minskar drastiskt. Azure-lagring kan vara 80 % billigare än att lägga till \-lokala SSD-diskar.

**Skyddar din data, även under migrering** Ha sinnesro medan du säkert sträcker ut dina program till molnet. SQL Servers Always Encrypted ger dig kryptering för dina data i rörelse. Säkerhet på radnivå (RLS) och andra avancerade SQL Server-säkerhetsfunktioner, fungerar också med Stretch Database för att skydda din data.

## Vad gör Stretch Database?
Efter att du aktiverat Stretch Database för en SQL Serverinstans, börjar en databas och minst en tabell att tyst migrera din historiska data till Azure.

-   Om du sparar historisk data i separata tabeller, kan du migrera hela tabellen.

-   Om din tabell både innehåller historisk och aktuell data, kan du ange ett filterpredikat för att markera vilka rader som ska migreras.

Stretch Database ser till att ingen data går förlorad om något fel uppstår vid migreringen. Det har även en logik för återförsök för att hantera anslutningsproblem som kan uppstå under migreringen. En dynamisk hanteringsvy ger status för migreringen.

Du kan pausa datamigreringen för att felsöka problem på den lokala servern eller maximera den tillgängliga nätverksbandbredden.

Du behöver inte ändra befintliga frågor eller klientprogram. Du kan fortsätta att ha sömlös åtkomst till både lokal och fjärransluten data även under migreringen. Det kan finnas en viss svarsfördröjning för fjärrfrågor, men de uppstår bara när du frågar historisk data.

![Översikt över Stretch Database][StretchOverviewImage1]

## Är Stretch Database för dig?
Om följande stämmer på dig, kan Stretch Database hjälpa dig att möta dina krav och lösa dina problem.

|Om du är en beslutsfattare|Om du är en DBA|
|------------------------------|-------------------|
|Jag behöver ha kvar transaktionsdata länge.|Mina tabeller håller på att växa sig alldeles för stora.|
|Jag behöver ibland fråga historisk data.|Mina användare säger att de vill ha åtkomst till historisk data, men de använder den bara sällan.|
|Jag har appar, inklusive äldre appar, som jag inte vill uppdatera.|Jag måste hela tiden hålla på och köpa och lägga till mer lagringsutrymme.|
|Jag vill hitta ett sätt att spara pengar på lagringsutrymme.|Jag kan inte säkerhetskopiera eller återställa så stora tabeller inom SLA:n.|

## Vilka typer av databaser och tabeller är lämpliga kandidater för Stretch Database?
Stretch Database riktar sig mot transaktionella databaser med stora mängder historisk data, som vanligtvis lagrats i ett litet antal tabeller. De här tabellerna kan ha över en miljard rader.

Om du använder SQL Server 2016-funktionen med temporala tabeller, använd då Stretch Database för att migrera alla eller delar av de associerade historiska tabellerna till kostnads\-effektiv lagring i Azure. Mer information finns i [Hantera kvarhållning av historisk data i systemversionerade temporala tabeller](https://msdn.microsoft.com/library/mt637341.aspx).

Använd Stretch Database Advisor, en funktion i SQL Server 2016 Upgrade Advisor, för att identifiera databaser och tabeller för Stretch Database. Mer information finns i [Identifiera databaser och tabeller för Stretch Database](sql-server-stretch-database-identify-databases.md). Läs mer om potentiella blockerande problem i [Begränsningar i ytområde och blockerande problem för Stretch Database](sql-server-stretch-database-limitations.md).

## <a name="FAQ"></a>Vanliga frågor om Stretch Database
**Fungerar Stretch Database med &lt;SQL Server-funktionsnamn&gt;?**
-   För en lista över SQL Server-funktioner som gör en tabell oläsbar för Stretch, se [Begränsningar i ytområde och blockerande problem för Stretch Database](sql-server-stretch-database-limitations.md).

-   Alternativt kan du hämta SQL Server 2016 Upgrade Advisor och köra Stretch Database Advisor för att identifiera databaser och tabeller som lämpar sig för Stretch Database. Mer information finns i [Identifiera databaser och tabeller för Stretch Database](sql-server-stretch-database-identify-databases.md).

**Kan jag använda en annan lokal SQL Server-instans för Stretch Database?**
Nej. Stretch Database stöder inte en annan lokal SQL Server-instans som fjärrslutpunkt.

**Kan jag inaktivera Stretch och flytta tillbaka migrerad data till den lokala tabellen?**
Ja. Mer information finns i [Inaktivera Stretch Database och hämta tillbaka fjärrdata](sql-server-stretch-database-disable.md).

## Villkor
**Lokal databas**. Den \-lokala SQL Server-databasen.

**Fjärrslutpunkt**. Platsen i Microsoft Azure som innehåller databasens fjärrdata.

**Lokal data**. Data i en databas där Stretch Database är aktiverat som inte kommer att flyttas till Azure baserat på Stretch Database-konfigurationen för tabellerna i databasen.

**Lämplig data**. Data i en databas där Stretch Database är aktiverat som inte än har flyttats, men som kommer att flyttas till Azure baserat på Stretch Database-konfigurationen för tabellerna i databasen.

**Fjärrdata**. Data i en databas med Stretch Database aktiverat som redan har flyttats till Azure.

## Arkitektur
Stretch Database utnyttjar resurserna i Microsoft Azure för att lasta av arkivdatalagring och frågebearbetning.

När du aktiverar Stretch Database på en databas, skapar det en säker länkad serverdefinition i den \-lokala SQL Servern. Den här länkade serverdefinitionen har fjärrslutpunkten som mål. När du aktiverar Stretch Database på en tabell i databasen, etablerar det fjärresurser och påbörjar migrering av lämplig data, om migrering har aktiverats.

Frågor mot tabeller med Stretch Database aktiverat, körs automatiskt både mot den lokala databasen och fjärrslutpunkten. Stretch Database utnyttjar bearbetningskraften i Azure för att köra frågor mot fjärrdata genom att skriva om frågan. Du kan se den här omskrivningen som en ”fjärrfråge”-operatör i den nya frågeplanen.

![Stretch Database-arkitektur][StretchOverviewImage2]

## Säkerhet och behörigheter

### Aktivera och inaktivera Stretch Database för en SQL Server-instans
För att börja konfigurera databaser för Stretch Database, behöver du först ändra konfigurationsalternativet ”fjärrdata-arkiv” på instans\-nivå med sp\_configure. Den här åtgärden kräver sysadmin- eller serveradmin-behörighet. Med det här alternativet aktiverat, kan du konfigurera databaser för Stretch Database, migrera data och fråga data på fjärrslutpunkten.

### Aktivera och inaktivera en Stretch Database för en databas eller tabell
För att konfigurera en databas för Stretch Database, behöver du ha CONTROL DATABASE-behörighet. Dessutom behöver du ange autentiseringsuppgifter för en administratör för fjärrslutpunkten.

För att konfigurera en tabell för Stretch Database, behöver du har ALTER-behörighet på tabellen och databasen behöver vara konfigurerad för Stretch Database.

### Dataåtkomst
Det är bara systemprocesser som har åtkomst till serverdefinitionen bakom Stretch Database. Användarinloggningar kan inte utfärda frågor genom den länkade serverdefinitionen till fjärrslutpunkten.

Stretch Database ändrar inte behörighetsmodellen för en befintlig databas. Användarinloggningar kan fråga data i en tabell med Stretch Database aktiverat genom den lokala databasen. Den lokala databasen kontrollerar behörighet för alla åtgärder som initieras av användaren på samma sätt som för andra objekt. Om du har åtkomstbehörighet för tabellen med Stretch Database aktiverat, har du åtkomst till allt innehåll som du är auktoriserad för, oavsett var datan är fysiskt belägen.

![Dataåtkomstmodell för Stretch Database][StretchOverviewImage3]

## Testkör Stretch Database
**Testkör Stretch Database med exempeldatabasen AdventureWorks.** För att få exempeldatabasen AdventureWorks bör du minst hämta databasfilen och exempel- och skriptfilerna [härifrån](https://www.microsoft.com/download/details.aspx?id=49502). När du har återställt exempeldatabasen till en instans av SQL Server 2016, packar du upp exempelfilen och öppnar Stretch DB-exempelfilen från Stretch DB-mappen. Kör skripten i den här filen för att kontrollera hur mycket diskutrymme dina data tar upp före och efter att du aktiverar Stretch Database, för att följa förloppet för datamigreringen och för att bekräfta att du kan fortsätta att fråga befintlig data och infoga ny data både under och efter datamigrering.

## Nästa steg
**Identifiera databaser och tabeller som är lämpliga kandidater för Stretch Database.** Hämta SQL Server 2016 Upgrade Advisor och kör Stretch Database Advisor för att identifiera databaser och tabeller som lämpar sig för Stretch Database. Stretch Database Advisor identifierar också blockerande problem. Mer information finns i [Identifiera databaser och tabeller för Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Jun16_HO2-->


