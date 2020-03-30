---
title: Komma igång med temporala tabeller
description: Lär dig hur du kommer igång med att använda temporala tabeller i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 98fd2658f3fbcb0e7e29114d29f8dc6ed39eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820713"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Komma igång med temporala tabeller i Azure SQL Database

Temporaltabeller är en ny programmerbarhetsfunktion i Azure SQL Database som gör att du kan spåra och analysera den fullständiga historiken över ändringar i dina data, utan att behöva anpassad kodning. Temporaltabeller håller data nära relaterade till tidskontexten så att lagrade fakta endast kan tolkas som giltiga inom den specifika perioden. Den här egenskapen för temporala tabeller möjliggör effektiv tidsbaserad analys och få insikter från datautveckling.

## <a name="temporal-scenario"></a>Tidsmässiga scenario

Den här artikeln illustrerar stegen för att använda temporala tabeller i ett programscenario. Anta att du vill spåra användaraktivitet på en ny webbplats som utvecklas från grunden eller på en befintlig webbplats som du vill utöka med analys av användaraktivitet. I det här förenklade exemplet antar vi att antalet besökta webbsidor under en tidsperiod är en indikator som måste fångas in och övervakas i webbplatsdatabasen som finns i Azure SQL Database. Målet med den historiska analysen av användaraktivitet är att få indata för att omforma webbplatsen och ge bättre upplevelse för besökarna.

Databasmodellen för det här scenariot är mycket enkel - användaraktivitetsmått representeras med ett enda heltalsfält, **PageVisited,** och fångas in tillsammans med grundläggande information om användarprofilen. Dessutom, för tidsbaserad analys, skulle du behålla en serie rader för varje användare, där varje rad representerar antalet sidor en viss användare besökte inom en viss tidsperiod.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Lyckligtvis behöver du inte lägga någon ansträngning i din app för att upprätthålla denna aktivitetsinformation. Med temporala tabeller är denna process automatiserad - vilket ger dig full flexibilitet under webbdesign och mer tid att fokusera på själva dataanalysen. Det enda du behöver göra är att se till att **WebSiteInfo** tabellen är konfigurerad som [tidsmässiga systemversioner.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0) De exakta stegen för att använda temporala tabeller i det här scenariot beskrivs nedan.

## <a name="step-1-configure-tables-as-temporal"></a>Steg 1: Konfigurera tabeller som tidsmässiga
Beroende på om du startar ny utveckling eller uppgraderar befintliga program skapar du antingen tidstabeller eller ändrar befintliga genom att lägga till temporala attribut. I allmänhet kan ditt scenario vara en blandning av dessa två alternativ. Utför dessa åtgärder med hjälp av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) eller något annat transact-SQL-utvecklingsverktyg.

> [!IMPORTANT]
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Skapa ny tabell
Använd snabbmenyalternativet "Ny systemversionstabell" i SSMS Object Explorer för att öppna frågeredigeraren med ett temporaltabellmallsskript och använd sedan "Ange värden för mallparametrar" (Ctrl+Skift+M) för att fylla i mallen:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

I SSDT väljer du mallen "Temporal Table (System-Versioned)" när du lägger till nya objekt i databasprojektet. Det öppnar bordsdesignern och gör att du enkelt kan ange tabelllayouten:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Du kan också skapa temporaltabell genom att ange Transact-SQL-uttrycken direkt, som visas i exemplet nedan. Observera att de obligatoriska elementen i varje temporaltabell är PERIOD-definitionen och SYSTEM_VERSIONING-satsen med en referens till en annan användartabell som lagrar historiska radversioner:

```
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
```

När du skapar tidstabellen för systemversioner skapas tabellen medföljande historik med standardkonfigurationen automatiskt. Standardhistoriktabellen innehåller ett grupperat B-trädindex i periodkolumnerna (slut, start) med sidkomprimering aktiverad. Den här konfigurationen är optimal för de flesta scenarier där tidstabeller används, särskilt för [datagranskning](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

I det här fallet strävar vi efter att utföra tidsbaserad trendanalys över en längre datahistorik och med större datauppsättningar, så lagringsvalet för historiktabellen är ett grupperat columnstore-index. En klustrad columnstore ger mycket bra komprimering och prestanda för analytiska frågor. Temporaltabeller ger dig flexibiliteten att konfigurera index på aktuella och tidsmässiga tabeller helt oberoende av dem. 

> [!NOTE]
> Columnstore-index är tillgängliga på Premium-nivån och på standardnivån, S3 och högre.
>

Följande skript visar hur standardindex i historiktabellen kan ändras till den klustrade columnstore:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Temporaltabeller representeras i Objektutforskaren med den specifika ikonen för enklare identifiering, medan dess historiktabell visas som en underordnad nod.

![AlterTable (Ändra Tabell)](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Ändra befintlig tabell till temporal
Låt oss täcka det alternativa scenariot där tabellen WebbplatsAnvändareInfo redan finns, men som inte har utformats för att hålla en historik över ändringar. I det här fallet kan du helt enkelt utöka den befintliga tabellen till att bli tidsmässig, vilket visas i följande exempel:

```
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
```

## <a name="step-2-run-your-workload-regularly"></a>Steg 2: Kör din arbetsbelastning regelbundet
Den största fördelen med temporala tabeller är att du inte behöver ändra eller justera din webbplats på något sätt för att utföra ändringsspårning. När temporaltabellerna har skapats finns det i tidigare radversioner på ett transparent sätt varje gång du utför ändringar på dina data. 

För att utnyttja automatisk ändringsspårning för det här scenariot ska vi bara uppdatera **kolumnsidorVisiterade** varje gång en användare avslutar sin session på webbplatsen:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Det är viktigt att notera att uppdateringsfrågan inte behöver veta den exakta tiden när den faktiska åtgärden inträffade eller hur historiska data kommer att bevaras för framtida analys. Båda aspekterna hanteras automatiskt av Azure SQL Database. Följande diagram visar hur historikdata genereras på varje uppdatering.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Steg 3: Utföra historisk dataanalys
Nu när tidsmässiga systemversioner är aktiverat är historisk dataanalys bara en fråga bort från dig. I den här artikeln kommer vi att ge några exempel som behandlar vanliga analysscenarier - för att lära dig alla detaljer, utforska olika alternativ som införts med FOR SYSTEM_TIME-klausulen. [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)

Om du vill visa de 10 användare som beställts efter antalet besökta webbsidor för en timme sedan kör du den här frågan:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Du kan enkelt ändra den här frågan för att analysera webbplatsbesöken från och med en dag sedan, för en månad sedan eller när som helst tidigare du vill.

Om du vill utföra grundläggande statistisk analys för föregående dag använder du följande exempel:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Om du vill söka efter aktiviteter för en viss användare använder du in-satsen INNE I inom en viss tidsperiod:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafisk visualisering är särskilt bekvämt för tidsmässiga frågor som du kan visa trender och användningsmönster på ett intuitivt sätt mycket enkelt:

![TemporalGraph (TemporalGraph)](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Tabellschema utvecklas
Vanligtvis måste du ändra det tidsmässiga tabellschemat medan du gör apputveckling. För det, helt enkelt köra vanliga ALTER TABLE-satser och Azure SQL Database kommer att sprida ändringar i historiktabellen på lämpligt sätt. Följande skript visar hur du kan lägga till ytterligare attribut för spårning:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

På samma sätt kan du ändra kolumndefinitionen medan arbetsbelastningen är aktiv:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Slutligen kan du ta bort en kolumn som du inte behöver längre.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Du kan också använda senaste [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) för att ändra tidstabellschemat medan du är ansluten till databasen (onlineläge) eller som en del av databasprojektet (offlineläge).

## <a name="controlling-retention-of-historical-data"></a>Kontrollera lagring av historiska data
Med tidstabeller med systemversioner kan historiktabellen öka databasstorleken mer än vanliga tabeller. En stor och ständigt växande historia tabell kan bli ett problem både på grund av ren lagringskostnader samt införa en prestandaskatt på tidsmässiga frågor. Att utveckla en datalagringsprincip för hantering av data i historiktabellen är därför en viktig aspekt av planering och hantering av livscykeln för varje temporal tabell. Med Azure SQL Database har du följande metoder för att hantera historiska data i den tidsmässiga tabellen:

* [Tabellpartitionering](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Anpassat rensningsskript](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Nästa steg

- Mer information om temporala tabeller finns i incheckning [av temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).
- Besök Kanal 9 för att höra en [riktig kund tidsmässig implementering framgångssaga](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [levande tidsmässig demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

