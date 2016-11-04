---
title: Översikt över Stretch Database | Microsoft Docs
description: Läs om hur Stretch Database migrerar dina kalldata transparent och säkert till Microsoft Azure-molnet.
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: ''
editor: ''

ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: douglasl

---
# Översikt över Stretch Database
Stretch Database migrerar dina kalldata transparent och säkert till Microsoft Azure-molnet.

Om du vill komma igång med Stretch Database direkt, se [Kom igång genom att köra guiden aktivera databas för Stretch](sql-server-stretch-database-wizard.md).

## Vad är fördelarna med Stretch Database?
Stretch Database ger följande fördelar:

### Innehåller kostnadseffektiv tillgänglighet för kalldata\-
Sträck ut varma eller kalla transaktionsdata dynamiskt från SQL Server till Microsoft Azure med SQL Server Stretch Database. Till skillnad från vanlig lagring av kalldata, är dina data alltid online och tillgängliga att fråga. Du kan ange längre kvarhållning av data utan att gå över budget för stora tabeller som kundorderhistorik. Dra nytta av den låga kostnaden i Azure istället för att skala dyr \-lokal lagring. Du väljer prisnivå och konfigurerar inställningarna i Azure Portal för att behålla kontroll över kostnaderna. Skala upp eller ned efter behov. Besök sidan [Priser för SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) för information.

### Kräver inte ändringar i frågor eller program
Ha åtkomst till din SQL Serverdata sömlöst oavsett om den är lokal eller utsträckt till molnet.\-  Du anger den princip som bestämmer var data lagras och SQL Server hanterar dataflytten i bakgrunden. Hela tabellen finns alltid online och redo för frågor. Stretch Database kräver inga förändringar av befintliga frågor eller program. Dataplatsen är fullständigt transparent för programmet.

### Strömlinjeformar lokalt dataunderhåll\-
Minska lokalt underhåll och lagring för dina data.\- Säkerhetskopieringar för dina \-lokala data körs snabbare och slutförs inom underhållsfönstret. Säkerhetskopiering av molndelen av dina data körs automatiskt. Dina \-lokala lagringsbehov minskar drastiskt. Azure-lagring kan vara 80 % billigare än att lägga till \-lokala SSD-diskar.

### Skyddar dina data även under migrering
Få sinnesro genom att på ett säkert sträcka ut dina viktigaste program till molnet. SQL Servers Always Encrypted ger dig kryptering för dina data i rörelse. Säkerhet på radnivå (RLS) och andra avancerade SQL Server-säkerhetsfunktioner, fungerar också med Stretch Database för att skydda din data.

## Vad gör Stretch Database?
Efter att du aktiverat Stretch Database för en SQL Server-instans, en databas och minst en tabell, börjar Stretch Database att tyst migrera dina kalldata till Azure.

* Om du sparar kalldata i separata tabeller, kan du migrera hela tabellen.
* Om din tabell innehåller både varm- och kalldata, kan du ange en filterfunktion för att markera vilka rader som ska migreras.

**Du behöver inte ändra befintliga frågor eller klientprogram.** Du kan fortsätta att ha sömlös åtkomst till både lokal och fjärransluten data även under migreringen. Det kan finnas en viss svarsfördröjning för fjärrfrågor, men de uppstår bara när du frågar kalldata.

**Stretch Database ser till att ingen data går förlorad** om något fel uppstår vid migreringen. Det har även en logik för återförsök för att hantera anslutningsproblem som kan uppstå under migreringen. En dynamisk hanteringsvy ger status för migreringen.

**Du kan pausa datamigreringen** för att felsöka problem på den lokala servern eller maximera den tillgängliga nätverksbandbredden.

![Översikt över Stretch Database][StretchOverviewImage1]

## Är Stretch Database för dig?
Om följande stämmer på dig, kan Stretch Database hjälpa dig att möta dina krav och lösa dina problem.

| Om du är en beslutsfattare | Om du är en DBA |
| --- | --- |
| Jag behöver ha kvar transaktionsdata länge. |Mina tabeller håller på att växa sig alldeles för stora. |
| Jag behöver ibland fråga kalldata. |Mina användare säger att de vill ha åtkomst till kalldata, men de använder dem bara sällan. |
| Jag har appar, inklusive äldre appar, som jag inte vill uppdatera. |Jag måste hela tiden hålla på och köpa och lägga till mer lagringsutrymme. |
| Jag vill hitta ett sätt att spara pengar på lagringsutrymme. |Jag kan inte säkerhetskopiera eller återställa så stora tabeller inom SLA:n. |

## Vilka typer av databaser och tabeller är lämpliga kandidater för Stretch Database?
Stretch Database riktar sig mot transaktionella databaser med stora mängder kalldata, som vanligtvis lagrats i ett litet antal tabeller. De här tabellerna kan ha över en miljard rader.

Om du använder SQL Server 2016-funktionen med temporala tabeller, använd då Stretch Database för att migrera alla eller delar av de associerade historiska tabellerna till kostnads\-effektiv lagring i Azure. Mer information finns i [Hantera kvarhållning av historisk data i systemversionerade temporala tabeller](https://msdn.microsoft.com/library/mt637341.aspx).

Använd Stretch Database Advisor, en funktion i SQL Server 2016 Upgrade Advisor, för att identifiera databaser och tabeller för Stretch Database. Mer information finns i [Identifiera databaser och tabeller för Stretch Database](sql-server-stretch-database-identify-databases.md). Läs mer om potentiella blockerande problem i [Begränsningar för Stretch Database](sql-server-stretch-database-limitations.md).

## Testkör Stretch Database
**Testkör Stretch Database med exempeldatabasen AdventureWorks.** För att få exempeldatabasen AdventureWorks bör du minst hämta databasfilen och exempel- och skriptfilerna [härifrån](https://www.microsoft.com/download/details.aspx?id=49502). När du har återställt exempeldatabasen till en instans av SQL Server 2016, packar du upp exempelfilen och öppnar Stretch DB-exempelfilen från Stretch DB-mappen. Kör skripten i den här filen för att kontrollera hur mycket diskutrymme dina data tar upp före och efter att du aktiverar Stretch Database, för att följa förloppet för datamigreringen och för att bekräfta att du kan fortsätta att fråga befintlig data och infoga ny data både under och efter datamigrering.

## Nästa steg
**Identifiera databaser och tabeller som är lämpliga kandidater för Stretch Database.** Hämta SQL Server 2016 Upgrade Advisor och kör Stretch Database Advisor för att identifiera databaser och tabeller som lämpar sig för Stretch Database. Stretch Database Advisor identifierar också blockerande problem. Mer information finns i [Identifiera databaser och tabeller för Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Sep16_HO3-->


