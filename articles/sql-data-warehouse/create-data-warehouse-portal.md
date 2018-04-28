---
title: 'Snabbstart: Skapa och fråga Azure SQL Data Warehouse - Azure-portalen | Microsoft Docs'
description: Skapa och läsa ett data warehouse med Azure SQL Data Warehouse i Azure-portalen.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e9501d023ad1e0bfdf692f31ec61ae59959c0f23
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Snabbstart:Skapa och skicka frågor till ett Azure SQL informationslager i Azure-portalen

Skapa snabbt och skicka frågor till ett Azure SQL Data Warehouse med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="before-you-begin"></a>Innan du börjar

Ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Skapa ett datalager

Ett Azure SQL Data Warehouse skapas med en definierad uppsättning [beräkningsresurser](memory-and-concurrency-limits.md). Databasen skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL-server](../sql-database/sql-database-servers-databases.md#what-is-an-azure-sql-logical-server). 

Följ de här stegen för att skapa ett SQL Data Warehouse som innehåller exempeldatabasen AdventureWorksDW. 

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Välj **Databases** (Databaser) på sidan **New** (nytt) och välj **SQL Data Warehouse** under **Featured** (aktuella) på sidan **New** (nytt).

    ![skapa ett tomt informationslager](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Fyll i formuläret SQL Data Warehouse med följande information:   

    | Inställning | Föreslaget värde | Beskrivning | 
    | ------- | --------------- | ----------- | 
    | **Databasnamn** | mySampleDataWarehouse | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). Observera att ett informationslager är en typ av databas.| 
    | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
    | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
    | **Välj källa** | Exempel | Anger att exempeldatabas ska läsas in. Observera att ett informationslager är en typ av databas. |
    | **Välj exempel** | AdventureWorksDW | Anger att exempeldatabasen AdventureWorksDW ska läsas in.  |

    ![skapa ett informationslager](media/create-data-warehouse-portal/select-sample.png)

4. Klicka på **Server** för att skapa och konfigurera en ny server för den nya databasen. Fyll i formuläret **Ny server** med följande information: 

    | Inställning | Föreslaget värde | Beskrivning | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
    | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
    | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
    | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

    ![skapa databasserver](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Klicka på **Välj**.

6. Klicka på **prestandanivå** ska kunna ange konfigurationen för prestanda för datalagret.

7. Den här kursen väljer **Gen2**. Skjutreglaget, som standard är inställt på **DW1000c**.  Prova att flytta det uppåt och nedåt för att se hur det fungerar. 

    ![konfigurera prestanda](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Klicka på **Använd**.

9. Nu när du har slutfört formuläret SQL Data Warehouse, klicka på **skapa** att tillhandahålla databasen. Etableringen tar några minuter. 

    ![klicka på skapa](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.
    
     ![avisering](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Tjänsten SQL Database Warehouse skapar en brandvägg på servernivå som hindrar externa program och verktyg från att ansluta till servern eller databaser på servern. Om du vill kan du lägga till brandväggsregler som tillåter anslutningar för specifika IP-adresser.  Följ dessa steg för att skapa en [brandväggsregel på servernivå](../sql-database/sql-database-firewall-configure.md) för klientens IP-adress. 

> [!NOTE]
> SQL Database Warehouse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433.
>

1. När distributionen är klar klickar du på **SQL-informationslager** från den vänstra menyn och klicka sedan på **mySampleDatabase** på den **SQL-informationslager** sidan. På översiktssidan för din databas öppnas och visar fullständigt kvalificerade servernamnet (exempelvis **mynewserver 20180430.database.windows.net**) och innehåller alternativ för ytterligare konfiguration. 

2. Kopiera det här fullständiga servernamnet för anslutning till servern och databaserna i efterföljande snabbstarter. Öppna serverinställningarna genom att klicka på servernamnet.

   ![hitta servernamn](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Öppna serverinställningarna 
4. genom att klicka på servernamnet.

   ![serverinställningar](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Klicka på **Visa brandväggsinställningar**. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

   ![brandväggsregler för server](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Klicka på **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

6. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Nu kan du ansluta till SQL-servern och dess informationslager med den här IP-adress. Anslutningen fungerar från SQL Server Management Studio eller något annat verktyg du väljer. När du ansluter kan du använda ServerAdmin-kontot som du skapade tidigare.  

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Klicka på **AV** på den här sidan och klicka sedan på **Spara** för att inaktivera brandväggen för alla Azure-tjänster.

## <a name="get-the-fully-qualified-server-name"></a>Hämta det fullständigt kvalificerade servernamnet

Hämta det fullständigt kvalificerade servernamnet för SQL-servern i Azure Portal. Du använder det fullständigt kvalificerade namnet senare när du ska ansluta till servern.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-informationslager** från den vänstra menyn och klicka på datalager på den **SQL datawarehouses** sidan. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**. I det här exemplet är det fullständigt kvalificerade namnet mynewserver 20180430.database.windows.net. 

    ![anslutningsinformation](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Ansluta till servern som serveradministratör

I det här avsnittet används [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) för att upprätta en anslutning till Azure SQL-servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Servertyp | Databasmotor | Det här värdet är obligatoriskt |
   | servernamn | Fullständigt kvalificerat servernamn | Här är ett exempel: **mynewserver 20180430.database.windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi konfigurerar i den här självstudiekursen. |
   | Inloggning | Serveradministratörskontot | Detta är det konto som du angav när du skapade servern. |
   | Lösenord | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

    ![Anslut till server](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

5. Expandera **Databaser** i Object Explorer. Expandera **mySampleDatabase** så visas objekten i den nya databasen.

    ![databasobjekt](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Köra några frågor

SQL Data Warehouse använder T-SQL som frågespråk. Använd följande steg om du vill öppna ett frågefönster och köra några T-SQL-frågor:

1. Högerklicka på **mySampleDataWarehouse** och välj **Ny fråga**.  Ett nytt frågefönster öppnas.
2. Ange följande kommando i frågefönstret för att visa en lista över databaser.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Klicka på **Kör**.  Resultatet av frågan visar två databaser: **master** och **mySampleDataWarehouse**.

    ![Skicka frågor mot databaser](media/create-data-warehouse-portal/query-databases.png)

4. Vi kan titta på vissa data genom att använda följande kommando för att se hur många kunder med efternamnet Adams som har tre barn hemma. I resultatlistan finns sex kunder. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![Skicka frågor till dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i informationslagret. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkningarna debiteras du endast för lagringen av data. Du kan återuppta beräkningarna när du är redo att arbeta med data.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på ditt informationslager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När informationslagret har pausats visas knappen **Starta**.  Klicka på **Starta** om du vill återuppta beräkningarna.

2. Om du vill ta bort informationslagret så att du varken debiteras för beräkning eller lagring klickar du på **Ta bort**.

3. Ta bort SQL-server som du har skapat, klicka på **mynewserver 20180430.database.windows.net** i föregående bild och klicka sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

4. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg
Du har nu skapat ett informationslager och en brandväggsregel, anslutit till ditt informationslager och kört några frågor. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.
> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
