---
title: 'Självstudiekurs: Anslut SQL on-demand (förhandsversion) till Power BI Desktop & skapa rapport'
description: I den här självstudien kan du lära dig hur du ansluter SQL on-demand (preview) i Azure Synapse Analytics till Power BI-skrivbordet och skapar en demorapport baserad på en vy.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0df8ac495b6aca81e46dffc248019483b1c82202
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422454"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Självstudiekurs: Anslut SQL on-demand (förhandsversion) till Power BI Desktop & skapa rapport

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa demodatabas
> - Skapa vy som används för rapport
> - Ansluta till Power BI-skrivbordet
> - Skapa rapport baserat på vy

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande programvara:

- Ett SQL-frågeverktyg, till exempel [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), eller SQL Server Management Studio [(SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power BI-skrivbordet](https://powerbi.microsoft.com/downloads/).

Värden för följande parametrar:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slutpunktsadress för SQL on-demand-tjänst    | Används som servernamn                                   |
| Slutpunktsregion för SQL-tjänst på begäran     | Används för att bestämma den lagring som används i proverna |
| Användarnamn och lösenord för åtkomst till slutpunkter | Används för att komma åt slutpunkt                               |
| Databas som du ska använda för att skapa vyer     | Databasen som används som utgångspunkt i exemplen       |

## <a name="1---create-database"></a>1 - Skapa databas

Skapa din egen demodatabas för demomiljön. Du använder den här databasen för att visa metadata, inte för att lagra faktiska data.

Skapa demodatabasen (och släpp en befintlig databas om det behövs) genom att köra följande Transact-SQL-skript (T-SQL):

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Skapa autentiseringsuppgifter

En autentiseringsfil är nödvändig för sql on-demand-tjänsten för att komma åt filer i lagring. Skapa autentiseringsuppgifterna för ett lagringskonto som finns i samma region som slutpunkten. Även om SQL on-demand kan komma åt lagringskonton från olika regioner, ger lagring och slutpunkt i samma region bättre prestanda.

Skapa autentiseringsuppgifterna genom att köra följande Transact-SQL-skript (T-SQL):

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - Förbered vy

Skapa vyn baserat på de externa demodata som Power BI kan använda genom att köra följande Transact-SQL-skript (T-SQL):

Skapa vyn `usPopulationView` i `Demo` databasen med följande fråga:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

Demodata innehåller följande datauppsättningar:

Usa befolkning efter kön och ras för varje amerikanskt län kommer från 2000 och 2010 Decennial Census i parkettformat.

| Mappsökväg                                                  | Beskrivning                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Överordnad mapp för data i demonstrationslagringskonto               |
| /release/us_population_county/                               | Amerikanska befolkningsdatafiler i parettformat, uppdelade efter år med hjälp av Hive/ Hadoop-partitioneringsschema. |

## <a name="4---create-power-bi-report"></a>4 - Skapa Power BI-rapport

Skapa rapporten för Power BI Desktop med hjälp av följande steg:

1. Öppna Power BI Desktop-programmet och välj **Hämta data**.

   ![Öppna Power BI-skrivbordsprogrammet och välj hämta data.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Välj **Azure** > **Azure SQL Database**. 

   ![Välj datakälla.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Skriv namnet på den server där databasen **Server** finns i fältet `Demo` Server och skriv sedan in databasnamnet. Välj alternativet **Importera** och välj sedan **OK**. 

   ![Välj databas på slutpunkten.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Välj önskad autentiseringsmetod:

    - Exempel för AAD 
  
    ![Klicka på Logga in.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Exempel för SQL Login - Skriv ditt användarnamn och lösenord.

    ![Använd SQL-inloggning.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Markera vyn `usPopulationView`och välj sedan **Läs in**. 

   ![Välj en vy på den valda databasen.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Vänta tills åtgärden är klar och sedan visas `There are pending changes in your queries that haven't been applied`ett popup-fönster som anger . Välj **Använd ändringar**. 

   ![Klicka på tillämpa ändringar.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Vänta tills dialogrutan **Använd frågeändringar** försvinner, vilket kan ta några minuter. 

   ![Vänta tills en fråga är klar.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. När inläsningen är klar väljer du följande kolumner i den här ordningen för att skapa rapporten:
   - countyName (länsnamn)
   - Befolkningen
   - stateName (stateName)

   ![Välj intressekolumner om du vill generera en kartrapport.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här rapporten tar du bort resurserna med följande steg:

1. Ta bort autentiseringsuppgifterna för lagringskontot

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Ta bort vyn

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Släpp databasen

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Nästa steg

Gå vidare till [frågelagringsfilerna](develop-storage-files-overview.md) för att lära dig hur du frågar lagringsfiler med Synapse SQL.
