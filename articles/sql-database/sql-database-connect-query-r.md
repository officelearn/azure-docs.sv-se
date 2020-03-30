---
title: Använda R med Machine Learning Services för att fråga en databas (förhandsgranskning)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Den här artikeln visar hur du använder ett R-skript med Azure SQL Database Machine Learning Services för att ansluta till en Azure SQL-databas och fråga den med Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768516"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Snabbstart: Använda R med Machine Learning Services för att fråga en Azure SQL-databas (förhandsversion)

I den här snabbstarten använder du R med Machine Learning Services för att ansluta till en Azure SQL-databas och använda T-SQL-uttryck för att fråga data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En [Azure SQL-databas](sql-database-single-database-get-started.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) med R aktiverat. [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skripten i den här artikeln är skrivna för att använda **Adventure Works-databasen.**

> [!NOTE]
> Under den offentliga förhandsversionen kommer Microsoft att gå ombord på dig och aktivera maskininlärning för din befintliga eller nya databas, men distributionsalternativet för hanterade instanser stöds för närvarande inte.

Machine Learning Services with R är en funktion i Azure SQL-databas som används för att köra R-skript i databasen. Mer information finns i [R-projektet](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

## <a name="create-code-to-query-your-sql-database"></a>Skapa kod för att fråga din SQL-databas

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med att ansluta läser du [Snabbstart: Använd SQL Server Management Studio för att ansluta och fråga en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skicka det fullständiga R-skriptet till [den sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

   Skriptet skickas genom `@script` argumentet. Allt i `@script` argumentet måste vara giltig R-kod.
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Om det uppstår några fel kan det bero på att den offentliga förhandsversionen av Machine Learning Services (med R) inte har aktiverats för din SQL-databas. Se [Förkunskaper](#prerequisites) ovan.

## <a name="run-the-code"></a>Kör koden

1. Kör [den sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

1. Kontrollera att de 20 översta raderna för kategori/produkt returneras i fönstret **Meddelanden.**

## <a name="next-steps"></a>Nästa steg

- [Designa din första Azure SQL-databas](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md)
- [Skapa och kör enkla R-skript i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-quickstart-r-create-script.md)
- [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
