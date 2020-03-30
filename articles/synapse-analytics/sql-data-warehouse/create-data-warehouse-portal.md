---
title: Skapa och fråga en Synapse SQL-pool (Azure-portal)
description: Skapa och fråga en Synapse SQL-pool med Azure-portalen
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5d5b2509b212172758fa867d9f27b829f43aeeaa
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349101"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Snabbstart: Skapa och fråga en Synapse SQL-pool med Azure-portalen

Skapa och fråga snabbt en Synapse SQL-pool (datalager) i Azure Synapse Analytics (tidigare SQL DW) med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Krav

1. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

   > [!NOTE]
   > Att skapa en SQL-pool i Azure Synapse kan resultera i en ny fakturerbar tjänst. Mer information finns i [Azure Synapse Analytics-priser](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

Informationslager skapas med SQL-pool i Azure Synapse Analytics. En SQL-pool skapas med en definierad uppsättning [beräkningsresurser](memory-concurrency-limits.md). Databasen skapas inom en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) och i en [logisk Azure SQL-server](../../sql-database/sql-database-servers.md).

Följ dessa steg för att skapa en SQL-pool som innehåller exempeldata för **AdventureWorksDW.**

1. välj **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen.

   ![skapa en resurs i Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Välj **Databaser** på den **nya** sidan och välj **Azure Synapse Analytics (tidigare SQL DW)** i listan **Aktuell.**

   ![skapa ett tomt informationslager](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. I **Basics**anger du prenumeration, resursgrupp, SQL-poolnamn och servernamn:

   | Inställning | Föreslaget värde | Beskrivning |
   | :------ | :-------------- | :---------- |
   | **Prenumeration** | Din prenumeration | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
   | **Sql-poolnamn** | Alla globalt unika namn (Ett exempel är *mySampleDataWarehouse)* | Giltiga databasnamn finns i [Databasidentifierare](/sql/relational-databases/databases/database-identifiers). En SQL-pool är en typ av databas. |
   | **Server** | Valfritt globalt unikt namn | Välj befintlig server eller skapa ett nytt servernamn, välj **Skapa nytt**. Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |

   ![skapa grundläggande informationslagerinformation](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Under **Prestandanivå**väljer du **Välj prestandanivå** om du vill ändra konfigurationen med ett skjutreglage.

   ![ändra prestandanivå för informationslager](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Mer information om prestandanivåer finns [i Hantera beräkning i Azure SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md).

5. Nu när du har slutfört fliken Grunderna i Azure Synapse Analytics-formuläret väljer du **Granska + Skapa** och **skapar** sedan för att skapa SQL-poolen. Etableringen tar några minuter.

   ![välj Granska + Skapa](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![Välj Skapa](./media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Välj **Meddelanden för** att övervaka distributionsprocessen i verktygsfältet.

   ![avisering](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Azure Synapse-tjänsten skapar en brandvägg på servernivå. Den här brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern. Om du vill kan du lägga till brandväggsregler som tillåter anslutningar för specifika IP-adresser. Följ dessa steg för att skapa en [brandväggsregel på servernivå](../../sql-database/sql-database-firewall-configure.md) för klientens IP-adress.

> [!NOTE]
> Azure Synapse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433.

1. När distributionen är klar väljer du **Alla tjänster** på menyn till vänster. Välj **Databaser**, välj stjärnan bredvid **Azure Synapse Analytics** för att lägga till Azure Synapse Analytics i dina favoriter.

2. Välj **Azure Synapse Analytics** på menyn till vänster och välj sedan **mySampleDataWarehouse** på sidan **Azure Synapse Analytics.** Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade servernamnet (till exempel **sqlpoolservername.database.windows.net)** och innehåller alternativ för ytterligare konfiguration.

3. Kopiera det fullständiga servernamnet för att ansluta till servern och dess databaser i det här och andra snabbstarterna. Om du vill öppna serverinställningarna markerar du servernamnet.

   ![hitta servernamn](./media/create-data-warehouse-portal/find-server-name.png)

4. Välj **Visa brandväggsinställningar**.

   ![serverinställningar](./media/create-data-warehouse-portal/server-settings.png)

5. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

   ![brandväggsregler för server](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Om du vill lägga till din aktuella IP-adress i en ny brandväggsregel väljer du **Lägg till klient-IP** i verktygsfältet. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

7. välj **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

8. välj **OK** och stäng sedan sidan **Brandväggsinställningar.**

Du kan nu ansluta till SQL-servern och dess SQL-pooler med den här IP-adressen. Anslutningen fungerar från SQL Server Management Studio eller något annat verktyg du väljer. När du ansluter kan du använda ServerAdmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Välj **AV** på den här sidan och välj sedan **Spara** för att inaktivera brandväggen för alla Azure-tjänster.

## <a name="get-the-fully-qualified-server-name"></a>Hämta det fullständigt kvalificerade servernamnet

Hämta det fullständigt kvalificerade servernamnet för SQL-servern i Azure Portal. Du använder det fullständigt kvalificerade namnet senare när du ska ansluta till servern.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Azure Synapse Analytics** på menyn till vänster och välj din på sidan Azure **Synapse Analytics.**

3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**. I det här exemplet är det fullständigt kvalificerade namnet sqlpoolservername.database.windows.net.

    ![anslutningsinformation](./media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Ansluta till servern som serveradministratör

I det här avsnittet används [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att upprätta en anslutning till Azure SQL-servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning | Föreslaget värde | Beskrivning |
   | :------ | :-------------- | :---------- |
   | Servertyp | Databasmotor | Det här värdet är obligatoriskt |
   | servernamn | Fullständigt kvalificerat servernamn | Här är ett exempel: **sqlpoolservername.database.windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi konfigurerar i den här självstudiekursen. |
   | Inloggning | Serveradministratörskontot | Konto som du angav när du skapade servern. |
   | lösenord | Lösenordet för serveradministratörskontot | Lösenord som du angav när du skapade servern. |
   ||||

   ![Anslut till server](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. välj **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

4. Expandera **Databaser** i Object Explorer. Expandera **mySampleDatabase** så visas objekten i den nya databasen.

   ![databasobjekt](./media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>Köra några frågor

SQL Data Warehouse använder T-SQL som frågespråk. Använd följande steg om du vill öppna ett frågefönster och köra några T-SQL-frågor:

1. Högerklicka-välj **mySampleDataWarehouse** och välj **Ny fråga**. Ett nytt frågefönster öppnas.

2. Ange följande kommando i frågefönstret för att visa en lista över databaser.

    ```sql
    SELECT * FROM sys.databases
    ```

3. välj **Kör**. Resultatet av frågan visar två databaser: **master** och **mySampleDataWarehouse**.

   ![Skicka frågor mot databaser](./media/create-data-warehouse-portal/query-databases.png)

4. Vi kan titta på vissa data genom att använda följande kommando för att se hur många kunder med efternamnet Adams som har tre barn hemma. I resultatlistan finns sex kunder. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Skicka frågor till dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i SQL-poolen. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagring kan du pausa beräkningen när du inte använder SQL-poolen. Genom att pausa beräkningen debiteras du bara för datalagring. Du kan återuppta beräkningen när du är redo att arbeta med data.

- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen.

Följ dessa steg för att rensa resurser som du inte längre behöver.

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj din SQL-pool.

   ![Rensa resurser](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **Pausa.** När SQL-poolen är pausad visas knappen **Återuppta.** Om du vill återuppta beräkningen väljer du **Återuppta**.

3. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring väljer du **Ta bort**.

4. Om du vill ta bort den SQL-server som du skapade markerar du **sqlpoolservername.database.windows.net** i föregående bild och väljer sedan **Ta bort**. Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen markerar du **myResourceGroup**och väljer sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du läser in data i SQL-poolen fortsätter du till artikeln [Läs in data i SQL Pool.](load-data-from-azure-blob-storage-using-polybase.md) 
