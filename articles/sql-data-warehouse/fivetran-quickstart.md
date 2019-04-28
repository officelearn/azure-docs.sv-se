---
title: Fivetran Snabbstart för Azure SQL Data Warehouse | Microsoft Docs
description: Kom igång snabbt med Fivetran och Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 1d8b6d2df21fe0aabe85754e061db4deb129b32e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082519"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Kom igång snabbt med Fivetran och SQL Data Warehouse

Den här snabbstarten beskriver hur du ställer in en ny Fivetran användare att arbeta med Azure SQL Data Warehouse. Artikeln förutsätter att du har en befintlig instans av SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Skapa en anslutning

1. Hitta det fullständigt kvalificerade servernamnet och databasnamnet som används för att ansluta till SQL Data Warehouse.
    
    Om du behöver hjälp med att hitta den här informationen kan du läsa [Anslut till Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. I installationsguiden väljer du om du vill ansluta din databas direkt eller genom att använda en SSH-tunnel.

   Om du väljer att ansluta direkt till din databas, måste du skapa en brandväggsregel för att tillåta åtkomst. Det här är den enklaste och säkraste metoden.

   Om du vill ansluta med hjälp av en SSH-tunnel ansluter Fivetran till en separat server i nätverket. Servern tillhandahåller en SSH-tunnel till din databas. Om databasen är i ett otillgängligt undernät i ett virtuellt nätverk måste du använda den här metoden.

3. Lägg till IP-adressen **52.0.2.4** att brandväggen på servernivå för att tillåta inkommande anslutningar till din SQL Data Warehouse-instans från Fivetran.

   Mer information finns i [Skapa en brandväggsregel på servernivå](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Konfigurera användarautentiseringsuppgifter för

1. Anslut till Azure SQL Data Warehouse med hjälp av SQL Server Management Studio eller verktyg som du föredrar. Logga in som en administratörsanvändare för servern. Kör följande SQL-kommandon för att skapa en användare för Fivetran:
    - I master-databasen: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - I SQL Data Warehouse-databas:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Bevilja användaren Fivetran följande behörigheter till ditt informationslager:

    ```
    GRANT CONTROL to fivetran;
    ```

    Behörighet krävs för att skapa databasbegränsade autentiseringsuppgifter som används när en användare läser in filer från Azure Blob storage med hjälp av PolyBase.

3. Lägg till en lämplig resursklass som Fivetran-användare. Resursklass som du använder beror på det minne som krävs för att skapa ett columnstore-index. Till exempel integration med produkter som kräver en högre resursklass på grund av det stora antalet kolumner och större datavolymen Marketo eller Salesforce produkter använder. En högre resursklass kräver mer minne för att skapa columnstore-index.

    Vi rekommenderar att du använder statiska resursklasser. Du kan börja med den `staticrc20` resursklass. Den `staticrc20` resursklass allokerar 200 MB för varje användare, oavsett prestandanivån som du använder. Om det inte går att kolumnlagringsindexering på klassnivå första resursen, öka resursklassen.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Mer information finns i avsnittet om [minne och samtidighet gränser](memory-and-concurrency-limits.md) och [resursklasser](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Logga in på Fivetran

Ange de autentiseringsuppgifter som används för att få åtkomst till SQL Data Warehouse för att logga in till Fivetran: 

* Värd (namnet på servern).
* Port.
* Databas.
* Användare (användarnamnet ska vara **fivetran\@_server_name_**  där *server_name* är en del av din Azure-värd URI: ***server_name *. Database.Windows.NET**).
* Lösenordet.
