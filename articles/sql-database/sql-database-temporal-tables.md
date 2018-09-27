---
title: Komma igång med Temporala tabeller i Azure SQL Database | Microsoft Docs
description: Lär dig hur du kommer igång med Temporala tabeller i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 03/21/2018
ms.openlocfilehash: d18630f9b4cea28bd19b2ac24e7b8c3d1822e17c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166426"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Komma igång med Temporala tabeller i Azure SQL Database
Temporala tabeller är en ny funktion för programmering i Azure SQL Database som hjälper dig att spåra och analysera den fullständiga historiken för ändringar i dina data, utan att behöva anpassad kodning. Temporala tabeller lagrar data som är nära förknippat med tiden kontext så att lagrade fakta kan tolkas som giltiga endast inom den specifika perioden. Den här egenskapen för Temporala tabeller möjliggör effektiv tidsbaserade analys och få information från de datautvecklingen.

## <a name="temporal-scenario"></a>Den temporala Scenario
Den här artikeln beskrivs stegen för att använda Temporala tabeller i ett scenario för programmet. Anta att du vill spåra användaraktivitet på en ny webbplats som utvecklas från grunden eller på en befintlig webbplats som du vill utöka med användaren aktivitet analytics. I det här förenklad exemplet förutsätter vi att antalet besökta webbsidor under en viss tidsperiod är en indikator som ska samlas in och övervakas i webbplats-databasen som finns på Azure SQL Database. Målet med historisk analys av användaraktivitet är att få indata för att göra om webbplats och ge bättre upplevelse för besökare.

Databasmodellen för det här scenariot är mycket enkelt – användaren aktivitet mått representeras med en enda heltalsfält **PageVisited**, och registreras tillsammans med grundläggande information om användarens profil. Dessutom för tidsbaserade analys du ser till att en serie med rader för varje användare, där varje rad motsvarar antalet sidor som en viss användare som har besökt under en viss tidsperiod.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Som tur är kan behöver du inte att placera alla arbete i din app för att underhålla den här informationen för aktiviteten. Med Temporala tabeller automatiskt den här processen – vilket ger dig fullständig flexibilitet under webbplatsdesign och mer tid att fokusera på dataanalysen själva. Det enda du behöver göra är att se till att **WebSiteInfo** tabellen har konfigurerats som [temporala systemversionstabeller](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Visa hur du använder Temporala tabeller i det här scenariot beskrivs nedan.

## <a name="step-1-configure-tables-as-temporal"></a>Steg 1: Konfigurera tabeller som temporala
Beroende på om du startar utvecklingen av nya eller uppgradera befintliga program, ska du skapa den temporala tabeller eller ändra befintliga genom att lägga till den temporala attribut. I allmänhet fallet ditt scenario kan vara en blandning av de här två alternativen. Utför dessa åtgärder med hjälp av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) eller några andra utvecklingsverktyg för Transact-SQL.

> [!IMPORTANT]
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Skapa ny tabell
Använda snabbmenyn för ”ny Systemversionstabellen” i SSMS Object Explorer för att öppna frågeredigeraren med ett skript för den temporala tabellen mallar och fylla mallen med hjälp av ”ange värden för mallparametrar” (Ctrl + Skift + M):

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

I SSDT, väljer du ”Temporal tabell (Systemversionstabeller)” mallen när du lägger till nya objekt i databasen-projektet. Som öppnar Tabelldesigner och gör att du kan enkelt ange layouten Tabell:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Du kan också skapa den temporala tabellen genom att ange Transact-SQL-uttryck direkt, som visas i exemplet nedan. Observera att de obligatoriska elementen för varje temporaltabellen är PERIOD-definitionen och SYSTEM_VERSIONING-satsen med en referens till en annan Användartabell som lagrar historisk radversioner:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

När du skapar den temporala systemversionstabellen skapas automatiskt den medföljande historiktabellen med standardkonfigurationen. Standard historiktabellen innehåller ett grupperat index i B-trädet på periodkolumner (slut start) med sidan komprimering aktiverat. Den här konfigurationen är optimalt för flesta scenarier där temporala tabeller används, särskilt för [datagranskning](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

I det här fallet är vårt mål att utföra analyser av tidsbaserade trend över en längre historik och med större datamängder, så lagringsalternativ för historiktabellen är ett grupperat kolumnlagringsindex. Ett grupperat columnstore ger mycket bra komprimering och prestanda för analytiska frågor. Temporala tabeller ger dig möjlighet att konfigurera index på den aktuella och den temporala tabellen helt oberoende av varandra. 

> [!NOTE]
> Columnstore-index är tillgängliga i Premium-nivån och standardnivån, S3 och senare.
>

Följande skript visar hur Standardindex för historiktabellen kan ändras till grupperade:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Temporala tabeller representeras i Object Explorer med appens specifika ikon lättare kan identifiera, även om dess historiktabellen visas som en underordnad nod.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Ändra befintlig tabellen till temporala
Vi täcker alternativt scenario där WebsiteUserInfo tabellen redan finns, men har inte utformats för att behålla historiken för ändringar. I det här fallet kan du helt enkelt utöka den befintliga tabellen ska bli temporala, som visas i följande exempel:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

## <a name="step-2-run-your-workload-regularly"></a>Steg 2: Kör din arbetsbelastning regelbundet
Den största fördelen med Temporala tabeller är att du inte behöver ändra eller anpassa din webbplats på något sätt att utföra ändringsspårning. När du skapat bevara Temporala tabeller transparent tidigare radversioner varje gång du utföra ändringar på dina data. 

För att kunna utnyttja automatisk ändringsspårning för det här scenariot ska vi bara uppdatera kolumnen **PagesVisited** varje gång när användaren avslutar göra åtaganden sessionen på webbplats:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Det är viktigt att Observera att uppdateringsfrågan inte behöver veta exakt tid när faktiska åtgärden utfördes inte heller hur historiska data bevaras för framtida analys. Båda aspekter hanteras automatiskt av Azure SQL-databasen. Följande diagram illustrerar hur historikdata genereras på varje uppdatering.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Steg 3: Utför analys av historiska data
När den temporala systemversionshanteringen är aktiverad, nu är bara en fråga från dig analys av historiska data. I den här artikeln kommer vi att ge några exempel som hanterar vanliga scenarier för analys - vill veta mer information om alla, utforska olika alternativ som introduceras med den [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) satsen.

Om du vill se de 10 viktigaste användarna ordnat efter antalet besökta webbsidor från och med en timme sedan, kör du den här frågan:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Du kan enkelt ändra den här frågan för att analysera besök från och med en dag sedan, sedan månaden eller vid någon tidpunkt tidigare du vill.

Använd följande exempel för att utföra grundläggande statistisk analys för föregående dag:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Om du vill söka efter aktiviteter för en specifik användare inom en viss tidsperiod, Använd instruktionen INNEHÖLL:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Grafisk visualisering är särskilt användbart för den temporala frågor som du kan visa trender och användningsmönster i ett intuitivt sätt enkelt:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Utvecklas tabellschema
Normalt behöver ändra schemat för den temporala tabellen medan du arbetar med utveckling av appar. För att kommer bara köra regelbundna ALTER TABLE-instruktioner och Azure SQL Database på lämpligt sätt vidarebefordrar ändringar till historiktabellen. Följande skript visar hur du kan lägga till ytterligare attribut för spårning:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Du kan ändra kolumndefinitionen när din arbetsbelastning är aktiv:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Slutligen kan du ta bort en kolumn som du inte behöver längre.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

Du kan också använda senaste [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) att ändra schemat för den temporala tabellen medan du är ansluten till databasen (onlineläge) eller som en del av databasprojektet (offlineläge).

## <a name="controlling-retention-of-historical-data"></a>Kontrollera kvarhållningen av historiska data
Med systemversionerade temporala tabeller öka historiktabellen databasstorleken mer än vanliga tabeller. En stor och ständigt växande historiktabellen kan bli ett problem som både på grund av ren lagringskostnader samt aktiviteternas en prestanda skatt på den temporala frågor. Därför kan är utveckla en databevarandeprincip för att hantera data i historiktabellen en viktig aspekt av planering och hantering av livscykeln för varje temporal tabell. Med Azure SQL Database kan ha du följande metoder för att hantera historisk data i den temporala tabellen:

* [Tabellpartitionering](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Anpassade skriptet för rensning](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Nästa steg
Kolla in detaljerad information om Temporala tabeller [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn935015.aspx).
Gå till Channel 9 till hör en [kundens verkliga temporala implemenation framgångshistoria](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [live temporala demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

