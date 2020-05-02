---
title: Anslut till Power BI Professional
description: I den här självstudien går vi igenom steg hur du ansluter Power BI Desktop till SQL på begäran (för hands version).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bedcc1f7375cb83131b00be93c785069a7d3e7d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692382"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Ansluta till Synapse SQL med Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [SQLCMD](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

I den här självstudien ska vi gå igenom stegen för att ansluta Power BI Desktop till SQL på begäran (för hands version).

## <a name="prerequisites"></a>Krav

Verktyg för att utfärda frågor:

- Valfri SQL-klient:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop installerat

Parametrar:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slut punkts adress för SQL-tjänst på begäran    | Kommer att användas som server namn                                   |
| Tjänstens slut punkts region för SQL på begäran     | Kommer att användas för att avgöra vilken lagrings enhet vi använder i exempel |
| Användar namn och lösen ord för slut punkts åtkomst | Kommer att användas för att komma åt slut punkten                               |
| Databas som du ska använda för att skapa vyer     | Den här databasen kommer att användas som start punkt i exempel       |

## <a name="first-time-setup"></a>Installation vid första tiden

Det finns två steg innan du använder exempel:

1. Skapa databas för dina vyer
2. Skapa autentiseringsuppgifter som ska användas av SQL på begäran för att komma åt filer i lagringen

### <a name="create-database"></a>Skapa databas

I den här artikeln för att komma igång bör du skapa en egen databas som du kan använda som en demonstration. En databas krävs för att skapa vyer. Du kommer att använda den här databasen i några av exempel frågorna i den här dokumentationen.

> [!NOTE]
> Databaser används bara för att visa metadata, inte för faktiska data.
>
> Skriv ned det databas namn du använder, du behöver det senare.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Vi måste skapa autentiseringsuppgifter innan du kan köra frågor. Den här autentiseringsuppgiften används av SQL-tjänsten på begäran för att komma åt filer i lagrings utrymmet.

> [!NOTE]
> Du måste skapa autentiseringsuppgifter för åtkomst till lagrings kontot. Även om SQL på begäran kan komma åt lagring från olika regioner, ger lagrings-och Azure Synapse-arbetsytan i samma region en bättre prestanda upplevelse.

Kodfragment för **att skapa autentiseringsuppgifter för inventerings data behållare**, kör:

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

## <a name="creating-power-bi-desktop-report"></a>Skapar Power BI Desktop-rapport

Öppna Power BI Desktop-programmet och välj alternativet **Hämta data** .

![Öppna Power BI Desktop-programmet och välj Hämta data.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Steg 1 – Välj data Källa

Välj **Azure** i menyn och **Azure SQL Database**.
![Välj data källa.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Steg 2 – Välj databas

Skriv webb adressen till databasen och namnet på databasen där vyn finns.
![Välj databas på slut punkten.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Nästa steg

Fortsätt att [fråga Storage-filer](get-started-azure-data-studio.md) och lär dig hur du ansluter till SQL på begäran med hjälp av Azure Data Studio.
 