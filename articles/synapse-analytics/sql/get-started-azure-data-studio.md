---
title: 'Azure Data Studio (förhandsversion): Anslut och fråga Synapse SQL'
description: Använd Azure Data Studio (förhandsversion) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: edf5a6a6a0f17c21abb818a0d41d0d0b1c39949c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423805"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Ansluta till Synapse SQL med Azure Data Studio (förhandsversion)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Du kan använda [Azure Data Studio (förhandsversion)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics. 

## <a name="connect"></a>Anslut

Om du vill ansluta till Synapse SQL öppnar du Azure Data Studio och väljer **Ny anslutning**.

![Öppna Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Välj **Microsoft SQL Server** som **anslutningstyp**.

Anslutningen kräver följande parametrar:

* **Server:** Server i `<Azure Synapse workspace name>`formuläret -ondemand.sql.azuresynapse.net
* **Databas:** Databasnamn

> [!NOTE]
> Om du vill använda **SQL on-demand (preview)** ska webbadressen se ut så här:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Om du vill använda **SQL-pool** ska url:en se ut så här:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Välj **Windows-autentisering,** **Azure Active Directory**eller **SQL-inloggning** som **autentiseringstyp**.

Om du vill använda **SQL Login** som autentiseringstyp lägger du till parametrarna för användarnamn/lösenord:

* **Användare:** Serveranvändare i formuläret`<User>`
* **Lösenord:** Lösenord som är associerat med användaren

Om du vill använda Azure Active Directory måste du välja den autentiseringstyp som behövs.

![AAD-autentisering](./media/get-started-azure-data-studio/3-aad-auth.png)

Den här skärmbilden visar **anslutningsinformationen** för **Windows-autentisering:**

![Windows-autentisering](./media/get-started-azure-data-studio/3-windows-auth.png)

Den här **skärmbilden visar anslutningsinformationen** med **SQL Login:**

![SQL-inloggning](./media/get-started-azure-data-studio/2-database-details.png)

Efter lyckad inloggning bör du se ![en instrumentpanel som denna: Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Söka i data

När du är ansluten kan du fråga Synapse SQL med hjälp av [transact-SQL-satser (Transact-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) mot instansen. Välj **Ny fråga** i instrumentpanelsvyn för att komma igång.

![Ny fråga](./media/get-started-azure-data-studio/5-new-query.png)

Du kan till exempel använda följande Transact-SQL-uttryck för att [fråga Parquet-filer](query-parquet-files.md) med SQL på begäran:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Nästa steg 
Utforska andra sätt att ansluta till Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)
 
