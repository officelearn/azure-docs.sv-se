---
title: Komma igång med Temporala tabeller i Azure SQL Database | Microsoft Docs
description: Lär dig hur du kommer igång med Temporala tabeller i Azure SQL Database.
services: sql-database
author: bonova
ms.date: 03/21/2018
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.author: bonova
ms.openlocfilehash: 140d2c9f6c334cec7d2761d05d7b20eb7106b9fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649048"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Komma igång med Temporala tabeller i Azure SQL-databas
Temporala tabeller är en ny funktion för programmering i Azure SQL Database som hjälper dig att spåra och analysera fullständig historiken för ändringar i dina data utan att behöva anpassad kodning. Temporala tabeller Behåll data som är nära relaterade till tid kontext så att lagrade fakta kan tolkas som giltiga endast inom en viss period. Den här egenskapen för Temporala tabeller möjliggör effektiv tidsbaserade analys och hämtning insikter från utvecklingen av data.

## <a name="temporal-scenario"></a>Tidsbestämd Scenario
Den här artikeln beskrivs stegen för att använda Temporala tabeller i ett scenario för programmet. Anta att du vill spåra användaraktivitet på en ny webbplats som utvecklas från grunden eller på en befintlig webbplats som du vill utöka med aktiviteten analyser. I detta exempel antar vi att antalet besökta sidor under en tidsperiod är en symbol som ska samlas in och övervakas i databasen webbplats som finns på Azure SQL Database. Målet med historisk analys av användaraktivitet är att hämta indata till omdesign webbplats och ge bättre upplevelse för besökare.

Databasmodellen för det här scenariot är väldigt enkelt - användaren aktivitet mått representeras med ett enda heltalsfält **PageVisited**, och avbildas tillsammans med grundläggande information om användarens profil. Dessutom för klockslag analys skulle du ha en serie med rader för varje användare, där varje rad motsvarar antalet sidor som en viss användare besöker inom en viss tidsperiod.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Lyckligtvis behöver du inte gör några i din app att underhålla informationen aktivitet. Med Temporala tabeller är den här processen automatiskt - så att du fullständig flexibilitet vid webbdesign och mer tid för att fokusera på Dataanalys sig själv. Det enda du behöver göra är att säkerställa att **WebSiteInfo** tabellen har konfigurerats som [temporala systemversionstabeller](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Stegen finns i använda Temporala tabeller i det här scenariot beskrivs nedan.

## <a name="step-1-configure-tables-as-temporal"></a>Steg 1: Konfigurera tabeller som temporala
Beroende på om du startar nyutveckling eller uppgradera befintliga program, ska du skapa temporala tabeller eller ändra befintliga genom att lägga till temporala attribut. I allmänhet ärende, ditt scenario kan vara en blandning av de här två alternativen. Utför dessa åtgärder med hjälp av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS) [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) eller andra Transact-SQL-utvecklingsverktyg.

> [!IMPORTANT]
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Skapa ny tabell
Använd kontexten menyalternativet ”nya Systemversionstabellen” i Object Explorer i SSMS att öppna frågeredigeraren med ett skript för temporala tabellen mallar och sedan använda ”Ange värden för mallparametrar” (Ctrl + Skift + M) för att fylla i mallen:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

I SSDT, väljer du ”Temporala tabellen (Systemversionstabeller)” mallen när du lägger till nya objekt databasprojektet. Som öppnar tabelldesignern och gör att du kan enkelt ange table-layout:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Du kan också en temporal tabell skapa genom att ange Transact-SQL-uttryck direkt, som visas i exemplet nedan. Observera att de obligatoriska elementen i varje temporal tabell är perioddefinitionen och SYSTEM_VERSIONING-satsen med en referens till en annan Användartabell som lagrar historisk raden versioner:

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

Medföljande historiktabellen med standardkonfigurationen skapas automatiskt när du skapar temporal systemversionstabell. Standard-historiktabellen innehåller ett grupperat index B-trädet på periodkolumner (end start) med sidan komprimering aktiverad. Den här konfigurationen är optimala för flesta scenarier där temporala tabeller används, särskilt för [data granskning](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

I det här fallet mål att utföra tidsbaserade trendanalys över en längre data historik och större datauppsättningar så att lagring valet för historiktabellen är ett grupperat columnstore-index. Ett grupperat columnstore ger mycket bra komprimering och prestanda för analytiska frågor. Temporala tabeller ger dig möjlighet att konfigurera index för de aktuella och temporala tabellerna helt oberoende av varandra. 

> [!NOTE]
> Columnstore-index är tillgängliga i Premium-nivån och på standardnivån S3 och senare.
>

Följande skript visar hur Standardindex för historiktabellen kan ändras till grupperade columnstore:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Temporala tabeller representeras i Object Explorer med specifika ikonen för enklare identifiering när dess historiktabellen visas som en underordnad nod.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Ändra befintlig tabell till temporala
Vi ska omfatta alternativa scenario där WebsiteUserInfo tabellen redan finns, men har inte utformats för att hålla en historik över ändringarna. I det här fallet kan du helt enkelt utöka den befintliga tabellen ska bli temporala, som visas i följande exempel:

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
Den största fördelen med Temporala tabeller är att du inte behöver ändra eller anpassa din webbplats på något sätt att utföra ändringsspårning. När du skapat kvarstår Temporala tabeller transparent tidigare versioner av raden varje gång du genomför ändringar på dina data. 

För att utnyttja automatiska ändringsspårning för det här scenariot vi bara uppdatera kolumnen **PagesVisited** varje gång när användaren slutar göra åtaganden session på webbplatsen:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Det är viktigt att Observera att uppdateringsfrågan inte behöver veta exakt tid när den faktiska åtgärden uppstod eller hur historiska data sparas för framtida analys. Båda aspekter hanteras automatiskt av Azure SQL-databasen. Följande diagram illustrerar hur historikdata genereras på varje uppdatering.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Steg 3: Analysera historiska data
Nu när temporala systemversionshanteringen är aktiverat, historiska dataanalys är bara en fråga från dig. I den här artikeln ska vi tillhandahåller några exempel som löser vanliga scenarier för analys - utforska olika alternativ som introducerades i om du vill veta alla uppgifter i [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) satsen.

Om du vill se de översta 10 användare ordnat efter antalet besökta webbsidor från och med en timme sedan köra den här frågan:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Du kan enkelt ändra den här frågan för att analysera besök från och med en dag sedan, en månad sedan eller när som helst i förflutna du vill.

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

Om du vill söka efter aktiviteter för en specifik användare i en viss tidsperiod, Använd instruktionen som ingår:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Grafisk visualiseringen är särskilt användbart för temporala frågor som du kan visa trender och användningsmönster i ett intuitivt sätt enkelt:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Utvecklingen av tabellens schema
Vanligtvis behöver du ändra schemat för temporala tabeller när du gör app-utveckling. För att kör bara reguljära ALTER TABLE-satser och Azure SQL Database kommer på lämpligt sätt att sprida ändringar till historiktabellen. Följande skript visar hur du kan lägga till ytterligare attribut för spårning:

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

Du kan också använda senaste [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) ändra temporal tabellschemat medan du är ansluten till databasen (onlineläge) eller som en del av databasprojektet (offlineläge).

## <a name="controlling-retention-of-historical-data"></a>Kontrollera kvarhållning av historisk data
Med temporala systemversionstabeller, kan historiktabellen öka databasens storlek mer än vanliga tabeller. En stor och ständigt växande historiktabellen kan bli ett problem både på grund av ren lagringskostnader samt införande av en prestanda skatt på temporala frågor. Därför kan är utveckla en databevarandeprincip för att hantera data i historiktabellen en viktig del i att planera och hantera livscykeln för varje temporal tabell. Du har följande metoder för att hantera historisk data i den temporala tabellen med Azure SQL Database:

* [Tabellpartitionering](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Rensning av anpassade skript](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Nästa steg
Mer detaljerad information om Temporala tabeller kolla [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn935015.aspx).
Besök Channel 9 hör en [verkliga kunden temporal implemenation fungerat](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [live temporal demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

