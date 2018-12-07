---
title: 'Ansluta till Azure Databricks från Excel, Python eller R '
description: Lär dig hur du använder Simba driver för att ansluta Azure Databricks till Excel, Python eller R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: c57550a8b683ad8f184884374c4f09216417fc40
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995623"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ansluta till Azure Databricks från Excel, Python eller R

I den här artikeln får du lära dig hur du använder Databricks ODBC-drivrutinen för att ansluta Azure Databricks med Microsoft Excel, Python och R-språket. När du har skapat anslutningen kan du komma åt data i Azure Databricks från Excel, Python eller R-klienter. Du kan också använda klienterna för att ytterligare analysera data. 

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en arbetsyta för Azure Databricks en Spark-kluster och exempeldata som är associerade med klustret. Om du inte redan har dessa krav, Slutför Snabbstart på [kör ett Spark-jobb på Azure Databricks med Azure portal](quickstart-create-databricks-workspace-portal.md).

* Ladda ned Databricks ODBC-drivrutin från [hämtningssidan för Databricks-drivrutinen](https://databricks.com/spark/odbc-driver-download). Installera 32-bitars eller 64-bitars version beroende på program från där du vill ansluta till Azure Databricks. Till exempel för att ansluta från Excel, installera 32-bitars version av drivrutinen. Installera 64-bitars version av drivrutinen för att ansluta från R och Python.

* Konfigurera en personlig åtkomsttoken i Databricks. Anvisningar finns i [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Konfigurera en DNS

En datakälla (DSN) innehåller information om en specifik datakälla. En ODBC-drivrutin måste denna DSN att ansluta till en datakälla. I det här avsnittet ska ställa du in en DSN som kan användas med Databricks ODBC-drivrutin för att ansluta till Azure Databricks från klienter som Microsoft Excel, Python eller R.

1. Gå till Databricks-kluster i Azure Databricks-arbetsytan.

    ![Öppna Databricks-klustret](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "öppna Databricks-kluster")

2. Under den **Configuration** klickar du på den **JDBC/ODBC** fliken och kopiera värdena för **-serverns värdnamn** och **HTTP-sökväg**. Du behöver dessa värden för att slutföra stegen i den här artikeln.

    ![Hämta konfiguration för Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "hämta Databricks-konfiguration")

3. Starta på din dator **ODBC-datakällor** program (32-bitars eller 64-bitars) beroende på programmet. Använd 32-bitarsversionen för att ansluta från Excel. Använd 64-bitarsversionen för att ansluta från R och Python.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "starta ODBC-program")

4. Under den **användar-DSN** fliken **Lägg till**. I den **Skapa ny datakälla** dialogrutan den **Simba Spark ODBC Driver**, och klicka sedan på **Slutför**.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "starta ODBC-program")

5. I den **Simba Spark ODBC Driver** dialogrutan Ange följande värden:

    ![Konfigurera DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "konfigurera DSN")

    I följande tabell innehåller information om värdena du ska använda i dialogrutan.
    
    |Fält  | Värde  |
    |---------|---------|
    |**Namn på datakälla**     | Ange ett namn för datakällan.        |
    |**Värdar**     | Ange det värde som du kopierade från Databricks-arbetsyta för *-serverns värdnamn*.        |
    |**Port**     | Ange *443*.        |
    |**Autentisering** > **mekanism**     | Välj *användarnamn och lösenord*.        |
    |**Användarnamn**     | Ange *token*.        |
    |**Lösenord**     | Ange token värde som du kopierade från Databricks-arbetsyta. |
    
    Utför följande åtgärder i dialogrutan för DSN-installationsprogram.
    
    * Klicka på **HTTP alternativ**. I dialogrutan som öppnas, klistrar du in värdet för *HTTP-sökväg* som du kopierade från Databricks-arbetsyta. Klicka på **OK**.
    * Klicka på **SSL-alternativ**. I dialogrutan som öppnas väljer du den **aktivera SSL** markerar du kryssrutan. Klicka på **OK**.
    * Klicka på **testa** att testa anslutningen till Azure Databricks. Klicka på **OK** att spara konfigurationen.
    * I den **ODBC Data Source Administrator** dialogrutan klickar du på **OK**.

Nu har du din DSN ställa in. I nästa avsnitt, du kan använda den här DSN för att ansluta till Azure Databricks från Excel, Python eller R.

## <a name="connect-from-microsoft-excel"></a>Anslut från Microsoft Excel

I det här avsnittet ska hämta du data från Azure Databricks i Microsoft Excel med hjälp av DSN som du skapade tidigare. Innan du börjar måste du kontrollera att du har Microsoft Excel på datorn. Du kan använda en utvärderingsversion av Excel från [Microsoft Excel utvärderingsversion länk](https://products.office.com/excel).

1. Öppna en tom arbetsbok i Microsoft Excel. Från den **Data** menyfliksområdet, klickar du på **hämta Data**. Klicka på **från andra källor** och klicka sedan på **från ODBC**.

    ![Starta ODBC från Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "starta ODBC från Excel")

2. I den **från ODBC** dialogrutan, välj DSN som du skapade tidigare och klicka sedan på **OK**.

    ![Välj DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Välj DSN")

3. Om du tillfrågas om autentiseringsuppgifter för användarnamn anger **token**. För lösenord, anger du värdet för token som du hämtade från Databricks-arbetsyta.

    ![Ange autentiseringsuppgifter för Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Välj DSN")

4. I fönstret navigator markerar du tabellen i Databricks som du vill läsa in till Excel och klicka sedan på **läsa in**. 

    ![Läsa in DTA-anrop i Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "belastningen DTA-anrop till Excel")

När du har data i din Excel-arbetsbok, kan du utföra analytiska åtgärder på den.

## <a name="connect-from-r"></a>Ansluta från R

> [!NOTE]
> Det här avsnittet innehåller information om hur du integrerar en R Studio-klient som körs på skrivbordet med Azure Databricks. Anvisningar för hur du använder R Studio på själva Azure Databricks-klustret finns i [R Studio på Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

I det här avsnittet ska använda du R-språket IDE till referensdata som är tillgängliga i Azure Databricks. Innan du börjar måste du ha följande installerat på datorn.

* En IDE-miljö för R-språket. Den här artikeln använder RStudio för Desktop. Du kan installera det från [hämta R Studio](https://www.rstudio.com/products/rstudio/download/).
* Om du använder RStudio Desktop som din IDE kan även installera Microsoft R Client från [ https://aka.ms/rclient/ ](https://aka.ms/rclient/). 

Öppna RStudio och gör följande:

- Referens för den `RODBC` paketet. På så sätt kan du ansluta till Azure Databricks med DSN som du skapade tidigare.
- Upprätta en anslutning med hjälp av DSN.
- Kör en SQL-fråga på data i Azure Databricks. I följande kodavsnitt *radio_sample_data* är en tabell som redan finns i Azure Databricks.
- Utföra vissa åtgärder på frågan för att verifiera utdata. 

Följande kodavsnitt utför dessa uppgifter:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Ansluta från Python

I det här avsnittet använder du en Python IDE (till exempel IDLE) till referensdata som är tillgängliga i Azure Databricks. Innan du börjar måste du slutföra följande krav:

* Installera Python från [här](https://www.python.org/downloads/). Installera Python från den här länken installerar också INAKTIV.

* Från Kommandotolken på datorn, installera den `pyodbc` paketet. Kör följande kommando:

      pip install pyodbc

Öppna INAKTIV och utför följande steg:

- Importera den `pyodbc` paketet. På så sätt kan du ansluta till Azure Databricks med DSN som du skapade tidigare.
- Upprätta en anslutning med hjälp av DSN som du skapade tidigare.
-  Kör en SQL-fråga som använder anslutningen du skapade. I följande kodavsnitt *radio_sample_data* är en tabell som redan finns i Azure Databricks.
- Utföra åtgärder på frågan för att verifiera utdata.

Följande kodavsnitt utför dessa uppgifter:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Nästa steg

* Läs mer om datakällor där du kan importera data till Azure Databricks, i [datakällor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


