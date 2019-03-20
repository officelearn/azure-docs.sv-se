---
title: Azure SQL Database viktig information | Microsoft Docs
description: Läs mer om nya funktioner och förbättringar i Azure SQL Database-tjänsten och i dokumentationen för Azure SQL Database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861305"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL-databas

Den här artikeln innehåller nya funktioner och förbättringar i tjänsten SQL Database och SQL Database-dokumentationen. Förbättringar för SQL Database-tjänsten, finns även [uppdateringar av tjänsten SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| Kommer snart ||
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Har lagts till loggen gränser för enskilda databaser|Mer information finns i [enkel database vCore-resursgränser](sql-database-vcore-resource-limits-single-databases.md).|
| Har lagts till loggen gränser för elastiska pooler och databaser i en pool|Mer information finns i [vCore resource begränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).|
| Har lagts till Transaction log rate styrning| Lagt till nytt innehåll för [Transaction log rate styrning](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Uppdaterade PowerShell-exempel för enskilda databaser och elastiska pooler använda az.sql modul | Mer information finns i [PowerShell-exempel för enskilda databaser och elastiska pooler](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Februari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
|Skapa en återställningsbart online-index är nu allmänt tillgänglig| Mer information finns i [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Hanterad instans-stöd för routningstabeller förbättrad| Mer information finns i [krav på](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Databasen namnbyte stöds i hanterade instansen | Mer information finns i den [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) och [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) syntax.|
|SQL-databas som en källa för referensdata för Stream Analytics. | Mer information finns i [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).|
|Data Migration Assistant lägger till stöd för den hanterade instansen. |Mer information finns i [vad är nytt i DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Migration Assistant lägger till stöd för target diagnostisk utvärdering av för den hanterade instansen. | Mer information finns i [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Datamigreringstjänst stöder migrering från Amazon RDS till managed instance | Mer information finns i [Självstudie: Migrera Fjärrskrivbordstjänster SQL Server till Azure SQL Database eller en Azure SQL Database hanterad instans online med DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|Lägger till den hanterade instansen distribution alternativet förtydliganden|Uppdatera många artiklar för att förtydliga se om det gäller enkel databas och elastisk pool distributionsalternativ för hanterad instans. |
|Uppdaterade tempdb-storlekar för DTU-baserade inköpsmodellen | Mer information finns i [Tempdb-databasen i SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Uppdaterade import och export med bacpac-fil för hanterad instans-support| Mer information finns i [Import från BACPAC](sql-database-import.md) och [exportera till BACPAC](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>Januari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| Ytterligare kornighet alternativ för att beräkna resurser | Generell användning och affärskritisk tjänstnivåer för [enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) har nu mer detaljerade beräkningsalternativ.|
| Visning av granskningsposter för den hanterade instansen i Azure portal | Visa [granskningsposter för hanterade instanser](sql-database-managed-instance-auditing.md) i Azure portal stöds nu.|
| ADVANCE threat funktion för dubblettidentifiering bytt namn till avancerad säkerhet för Data | Funktionen för identifiering av avancerade hot som bytt namn till [avancerade datasäkerhet](sql-advanced-threat-protection.md) för enskilda databaser, elastiska pooler och hanterade instanser. |
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Hanterade instanser och Transaktionsreplikering | Har lagts till artikeln om hur du använder [Transaktionsreplikering med hanterade instanser](replication-with-sql-database-managed-instance.md) |
| Har lagts till Azure AD med självstudiekursen för hanterad instans | Detta [Azure AD med hanterad instans](sql-database-managed-instance-aad-security-tutorial.md) självstudiekurs visar du behöva konfigurera och testa hanterade instans security med Azure AD-inloggningar. |
| Uppdaterat innehåll för jobbet automation med hjälp av Transact-SQL-skript | Uppdaterade och tydliggjorde innehåll för att använda [jobbet automation med hjälp av Transact-SQL-skript](sql-database-job-automation-overview.md) för enskilda databaser, elastiska pooler och hanterade instanser |
| Säkerhetsinnehåll för hanterade instanser uppdateras | Uppdaterade och klargör innehållet för den [säkerhetsmodell för hanterade instanser](sql-database-security-overview.md), och skillnader i med säkerhetsmodell för enskilda databaser och elastiska pooler |
| Uppdatera alla snabbstarter och självstudier | Alla snabbstarter och självstudier i den [dokumentation](https://docs.microsoft.com/azure/sql-database) har uppdaterats och så att den matchar ändringar i Azure portal |
| Översikt för har lagts till snabbstartguider | Lagt till en översikt över Snabbstartsguide till [enkla databaser](sql-database-quickstart-guide.md) och för [hanterade instanser](sql-database-managed-instance-quickstart-guide.md) |
| Har lagts till SQL-databas i ordlista | Detta [villkoren ordlista](sql-database-glossary-terms.md) artikeln innehåller en fullständig förteckning över SQL Database-termer och länkar till den primära konceptuella sidan som förklarar termen i kontexten. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Bidra till innehållet förbättring

Azure SQL-dokumentationsuppsättningen är öppen källkod. Att arbeta offentligt ger flera fördelar:

- Öppen källkod lagringsplatser med att få feedback på vilka dokument som behövs mest.
- Granskning av lagringsplatser med öppen källkod med att publicera det mest hjälpfulla innehållet på din första utgåva.
- Uppdatering av lagringsplatser med öppen källkod med att göra det enklare att kontinuerligt förbättra innehållet.

Om du vill bidra till Azure SQL Database-dokumentationsinnehåll, se den [Microsoft Docs översikt över deltagarguiden](https://docs.microsoft.com/contribute/). Användarupplevelsen på [docs.microsoft.com](https://docs.microsoft.com/) integrerar [GitHub](https://github.com/) arbetsflödet direkt för att göra det ännu enklare. Börja med att [redigera det dokument du visar](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Hjälp till genom [granska nya ämnen](https://docs.microsoft.com/contribute/#review-open-prs), eller [skapa kvalitetsärenden](https://docs.microsoft.com/contribute/#create-quality-issues).
