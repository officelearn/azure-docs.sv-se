---
title: Ansluta till Power BI Professional
description: I den här självstudien går vi igenom steg hur du ansluter Power BI-skrivbordet till SQL on-demand (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422566"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Ansluta till Synapse SQL med Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

I den här självstudien går vi igenom steg hur du ansluter Power BI-skrivbordet till SQL on-demand (preview).

## <a name="prerequisites"></a>Krav

Verktyg för att utfärda frågor:

- VALFRI SQL-klient:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI-skrivbordet installerat

Parametrar:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slutpunktsadress för SQL on-demand-tjänst    | Kommer att användas som servernamn                                   |
| Slutpunktsregion för SQL-tjänst på begäran     | Kommer att användas för att avgöra vilken lagring vi kommer att använda i prover |
| Användarnamn och lösenord för åtkomst till slutpunkter | Kommer att användas för att komma åt slutpunkten                               |
| Databas som du ska använda för att skapa vyer     | Denna databas kommer att användas som utgångspunkt i       |

## <a name="first-time-setup"></a>Första gången setup

Det finns två steg innan du använder prover:

1. Skapa databas för dina vyer
2. Skapa autentiseringsuppgifter som ska användas av SQL på begäran för att komma åt filer i lagring

### <a name="create-database"></a>Skapa databas

Eftersom du kommer att använda demomiljö bör du skapa en egen databas för demoändamål. Databasen behövs för att skapa vyer i den. Du kommer att använda den här databasen i några exempelfrågor i den här dokumentationen.

> [!NOTE]
> Observera att databaser endast används för visningsmetadata, inte för faktiska data.
>
> Skriv ner databasnamn du använder, behöver du det senare.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Vi måste skapa autentiseringsuppgifter innan du kan köra frågor. Den här autentiseringsuppgifterna används av SQL on-demand-tjänsten för att komma åt filer i lagring.

> [!NOTE]
> Observera att du måste skapa autentiseringsuppgifter för åtkomst till lagringskontot. Även om SQL on-demand kan komma åt lagringar från olika regioner, kommer lagring och Azure Synapse-arbetsyta i samma region att ge bättre prestandaupplevelse.

**Kodavsnitt om hur du skapar autentiseringsuppgifter för folkräkningsdatabehållare**, kör:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Skapa Power BI-skrivbordsrapport

Öppna Power BI-skrivbordsprogrammet och välj alternativet "Hämta data".
![Öppna Power BI-skrivbordsprogrammet och välj hämta data.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Steg 1 - Välj datakälla

Välj "Azure" på menyn och sedan "Azure SQL Database".
![Välj datakälla.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Steg 2 - Välj databas

Skriv URL för databasen och namnet på databasen där vyn finns.
![Välj databas på slutpunkten.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till [frågelagringsfiler](get-started-azure-data-studio.md) för att lära dig hur du ansluter till SQL on-demand med Azure Data Studio.
 