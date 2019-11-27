---
title: Snabb start för Fivetran
description: Kom igång snabbt med Fivetran och Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 715f891484458f3bf3febc6807c3490b88062d50
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229096"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Kom igång snabbt med Fivetran och SQL Data Warehouse

I den här snabb starten beskrivs hur du konfigurerar en ny Fivetran-användare att arbeta med Azure SQL Data Warehouse. Artikeln förutsätter att du har en befintlig instans av SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Konfigurera en anslutning

1. Hitta det fullständigt kvalificerade Server namnet och databas namnet som du använder för att ansluta till SQL Data Warehouse.
    
    Om du behöver hjälp med att hitta den här informationen, se [Anslut till Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. I installations guiden väljer du om du vill ansluta databasen direkt eller med hjälp av en SSH-tunnel.

   Om du väljer att ansluta direkt till databasen måste du skapa en brand Väggs regel för att tillåta åtkomst. Den här metoden är den enklaste och säkraste metoden.

   Om du väljer att ansluta med hjälp av en SSH-tunnel ansluter Fivetran till en separat server i nätverket. Servern tillhandahåller en SSH-tunnel till din databas. Du måste använda den här metoden om databasen finns i ett oåtkomligt undernät i ett virtuellt nätverk.

3. Lägg till IP- **52.0.2.4** i brand väggen på server nivå för att tillåta inkommande anslutningar till din SQL Data Warehouse instans från Fivetran.

   Mer information finns i [Skapa en brandväggsregel på servernivå](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Konfigurera användarautentiseringsuppgifter

1. Anslut till din Azure SQL Data Warehouse genom att använda SQL Server Management Studio eller det verktyg som du föredrar. Logga in som en server administratörs användare. Kör sedan följande SQL-kommandon för att skapa en användare för Fivetran:
    - I huvud databasen: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - I SQL Data Warehouse databas:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Ge Fivetran-användaren följande behörigheter till ditt lager:

    ```
    GRANT CONTROL to fivetran;
    ```

    KONTROLL behörighet krävs för att skapa autentiseringsuppgifter för databasen som används när en användare läser in filer från Azure Blob Storage med PolyBase.

3. Lägg till en lämplig resurs klass till Fivetran-användaren. Vilken resurs klass du använder beror på vilket minne som krävs för att skapa ett columnstore-index. Till exempel är integreringar med produkter som Marketo och Salesforce kräver en högre resurs klass på grund av det stora antalet kolumner och den större mängden data som används av produkterna. En högre resurs klass kräver mer minne för att skapa columnstore-index.

    Vi rekommenderar att du använder statiska resurs klasser. Du kan börja med `staticrc20` resurs klass. `staticrc20` resurs klass allokerar 200 MB för varje användare, oavsett vilken prestanda nivå du använder. Om columnstore-indexeringen Miss lyckas på den ursprungliga resurs klass nivån ökar du resurs klassen.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Mer information finns i om [minnes-och samtidighets gränser](memory-concurrency-limits.md) och [resurs klasser](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Logga in på Fivetran

Logga in på Fivetran genom att ange de autentiseringsuppgifter som du använder för att få åtkomst till SQL Data Warehouse: 

* Värd (ditt Server namn).
* Lastning.
* Databas.
* Användare (användar namnet ska vara **fivetran\@_server_name_**  där *server_name* är en del av din Azure-värd-URI: ***server_name *. Database. Windows. net**).
* Ords.
