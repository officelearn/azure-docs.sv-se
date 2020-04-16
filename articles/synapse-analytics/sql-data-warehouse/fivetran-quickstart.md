---
title: 'Snabbstart: Fivetran och datalager'
description: Kom igång med Fivetran och ett Azure Synapse Analytics-informationslager.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8f164232a3b1782511758f93a9e9b8d17d3714d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414275"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Snabbstart: Fivetran med informationslager 

Den här snabbstarten beskriver hur du konfigurerar en ny Fivetran-användare för att arbeta med ett Azure Synapse Analytics-informationslager som etablerats med en SQL Pool. Artikeln förutsätter att du har ett befintligt informationslager.

## <a name="set-up-a-connection"></a>Konfigurera en anslutning

1. Leta reda på det fullständiga servernamn och det databasnamn som du använder för att ansluta till ditt informationslager.
    
    Om du behöver hjälp med att hitta den här informationen läser du [Anslut till ditt informationslager](../sql/connect-overview.md).

2. I installationsguiden väljer du om du vill ansluta databasen direkt eller med hjälp av en SSH-tunnel.

   Om du väljer att ansluta direkt till databasen måste du skapa en brandväggsregel för att tillåta åtkomst. Denna metod är den enklaste och säkraste metoden.

   Om du väljer att ansluta med en SSH-tunnel ansluter Fivetran till en separat server i nätverket. Servern tillhandahåller en SSH-tunnel till databasen. Du måste använda den här metoden om databasen finns i ett otillgängligt undernät i ett virtuellt nätverk.

3. Lägg till IP-adressen **52.0.2.4** i brandväggen på servernivå för att tillåta inkommande anslutningar till datalagerinstansen från Fivetran.

   Mer information finns i [Skapa en brandväggsregel på servernivå](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Ställ in användaruppgifter

1. Anslut till ditt informationslager med hjälp av SQL Server Management Studio (SSMS) eller det verktyg som du föredrar. Logga in som serveradministratör. Kör sedan följande SQL-kommandon för att skapa en användare för Fivetran:

    - I huvuddatabasen: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - I databasen för informationslager:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Ge Fivetran-användaren följande behörigheter till ditt informationslager:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    BEHÖRIGHET krävs för att skapa autentiseringsuppgifter för databasomfattning som används när en användare läser in filer från Azure Blob-lagring med hjälp av PolyBase.

3. Lägg till en lämplig resursklass till Fivetran-användaren. Vilken resursklass du använder beror på vilket minne som krävs för att skapa ett columnstore-index. Integrationer med produkter som Marketo och Salesforce kräver till exempel en högre resursklass på grund av det stora antalet kolumner och den större mängden data som produkterna använder. En högre resursklass kräver mer minne för att skapa columnstore-index.

    Vi rekommenderar att du använder statiska resursklasser. Du kan börja `staticrc20` med resursklassen. Resursklassen `staticrc20` allokerar 200 MB för varje användare, oavsett vilken prestandanivå du använder. Om columnstore-indexeringen misslyckas på den ursprungliga resursklassnivån ökar du resursklassen.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Mer information finns i om [minnes- och samtidighetsgränser](memory-concurrency-limits.md) och [resursklasser](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Anslut från Fivetran

Om du vill ansluta till ditt informationslager från ditt Fivetran-konto anger du de autentiseringsuppgifter som du använder för att komma åt ditt informationslager: 

* Värd (ditt servernamn).
* Port.
* Databas.
* Användare (användarnamnet ska vara **fivetran\@server_name** där *server_name* är en del av din Azure-värd URI: ** _servernamn\__.database.windows.net**).
* Lösenord.
