---
title: 'Snabb start: Fivetran och informations lager'
description: Kom igång med Fivetran och ett informations lager för Azure Synapse Analytics.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b068b2436aaa1df22e3c83a54fb384f925149cc2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194622"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Snabb start: Fivetran med informations lager 

I den här snabb starten beskrivs hur du konfigurerar en ny Fivetran-användare så att den fungerar med ett Azure Synapse Analytics-informations lager som tillhandahålls med en SQL-pool. Artikeln förutsätter att du har ett befintligt informations lager.

## <a name="set-up-a-connection"></a>Konfigurera en anslutning

1. Hitta det fullständigt kvalificerade Server namnet och databas namnet som du använder för att ansluta till ditt data lager.
    
    Om du behöver hjälp med att hitta den här informationen kan du läsa [Anslut till ditt data lager](sql-data-warehouse-connect-overview.md).

2. I installations guiden väljer du om du vill ansluta databasen direkt eller med hjälp av en SSH-tunnel.

   Om du väljer att ansluta direkt till databasen måste du skapa en brand Väggs regel för att tillåta åtkomst. Den här metoden är den enklaste och säkraste metoden.

   Om du väljer att ansluta med hjälp av en SSH-tunnel ansluter Fivetran till en separat server i nätverket. Servern tillhandahåller en SSH-tunnel till din databas. Du måste använda den här metoden om databasen finns i ett oåtkomligt undernät i ett virtuellt nätverk.

3. Lägg till IP- **52.0.2.4** i brand väggen på server nivå för att tillåta inkommande anslutningar till data lager instansen från Fivetran.

   Mer information finns i [Skapa en brandväggsregel på servernivå](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Konfigurera användarautentiseringsuppgifter

1. Anslut till ditt informations lager genom att använda SQL Server Management Studio (SSMS) eller det verktyg som du föredrar. Logga in som en server administratörs användare. Kör sedan följande SQL-kommandon för att skapa en användare för Fivetran:

    - I huvud databasen: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - I informations lager databasen:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Ge Fivetran-användaren följande behörigheter till ditt data lager:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    KONTROLL behörighet krävs för att skapa autentiseringsuppgifter för databasen som används när en användare läser in filer från Azure Blob Storage med PolyBase.

3. Lägg till en lämplig resurs klass till Fivetran-användaren. Vilken resurs klass du använder beror på vilket minne som krävs för att skapa ett columnstore-index. Till exempel är integreringar med produkter som Marketo och Salesforce kräver en högre resurs klass på grund av det stora antalet kolumner och den större mängden data som används av produkterna. En högre resurs klass kräver mer minne för att skapa columnstore-index.

    Vi rekommenderar att du använder statiska resurs klasser. Du kan börja med `staticrc20` resurs klass. `staticrc20` resurs klass allokerar 200 MB för varje användare, oavsett vilken prestanda nivå du använder. Om columnstore-indexeringen Miss lyckas på den ursprungliga resurs klass nivån ökar du resurs klassen.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Mer information finns i om [minnes-och samtidighets gränser](memory-concurrency-limits.md) och [resurs klasser](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Logga in på Fivetran

Om du vill logga in på Fivetran anger du de autentiseringsuppgifter som du använder för att få åtkomst till ditt data lager: 

* Värd (ditt Server namn).
* Lastning.
* Databas.
* Användare (användar namnet ska vara **fivetran\@_server_name_**  där *server_name* är en del av din Azure-värd-URI:  **_Server\_Name_. Database.Windows.net**).
* Ords.
