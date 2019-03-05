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
ms.date: 03/04/2019
ms.author: carlrab
ms.openlocfilehash: af0fd591393f9f187a75f79fea980d41085df5cb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342453"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL-databas

Den här artikeln innehåller nya funktioner och förbättringar i tjänsten SQL Database och SQL Database-dokumentationen. Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Har lagts till loggen gränser för enskilda databaser|Mer information finns i [enkel database vCore resursbegränsningar](sql-database-vcore-resource-limits-single-databases.md)|
| Har lagts till loggen gränser för elastiska pooler och databaser i en pool|Mer information finns i [vCore resource begränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)|
| Har lagts till Transaction log rate styrning| Lagt till nytt innehåll för [Transaction log rate styrning](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)|
| Uppdaterade PowerShell-exempel för enskilda databaser och elastiska pooler använda az.sql modul | Mer information finns i [PowerShell-exempel för enskilda databaser och elastiska pooler](sql-database-powershell-samples.md#single-database-and-elastic-pools).

## <a name="february-2019"></a>Februari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
|Skapa en återställningsbart online-index är nu allmänt tillgänglig| Mer information finns i [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)|
|Hanterad instans-stöd för routningstabeller förbättrad| Mer information finns i [krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements)|

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|Lägger till den hanterade instansen distribution alternativet förtydliganden|Uppdatera många artiklar för att förtydliga se om det gäller enkel databas och elastisk pool distributionsalternativ för hanterad instans |
|Uppdaterade tempdb-storlekar för DTU-baserade inköpsmodellen | Mer information finns i [Tempdb-databasen i SQL-databas](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)|
|Uppdaterade import och export med bacpac-fil för hanterad instans-support| Mer information finns i [Import från BACPAC](sql-database-import.md) och [exportera till BACPAC](sql-database-export.md) |

## <a name="january-2019"></a>Januari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| Ytterligare kornighet alternativ för att beräkna resurser | Generell användning och affärskritisk tjänstnivåer för [enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) har nu mer detaljerade beräkningsalternativ.|
| Visning av granskningsposter för den hanterade instansen i Azure portal | Visa [granskningsposter för hanterade instanser](sql-database-managed-instance-auditing.md) i Azure portal stöds nu.|
| ADVANCE threat funktion för dubblettidentifiering bytt namn till avancerad säkerhet för Data | Funktionen för identifiering av avancerade hot som bytt namn till [avancerade datasäkerhet](sql-advanced-threat-protection.md) för enskilda databaser, elastiska pooler och hanterade instanser |
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

Om du vill bidra till Azure SQL Database-dokumentationsinnehåll, se den [Microsoft Docs översikt över deltagarguiden](https://docs.microsoft.com/en-us/contribute/). Användarupplevelsen på [docs.microsoft.com](https://docs.microsoft.com/) integrerar [GitHub](https://github.com/) arbetsflödet direkt för att göra det ännu enklare. Börja med att [redigera det dokument du visar](https://docs.microsoft.com/en-us/contribute/#quick-edits-to-existing-documents). Hjälp till genom [granska nya ämnen](https://docs.microsoft.com/en-us/contribute/#review-open-prs), eller [skapa kvalitetsärenden](https://docs.microsoft.com/en-us/contribute/#create-quality-issues).
