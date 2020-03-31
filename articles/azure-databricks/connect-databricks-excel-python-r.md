---
title: 'Ansluta till Azure Databricks från Excel, Python eller R '
description: Lär dig hur du använder Simba-drivrutinen för att ansluta Azure Databricks till Excel, Python eller R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73601952"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ansluta till Azure Databricks från Excel, Python eller R

I den här artikeln får du lära dig hur du använder Databricks ODBC-drivrutinen för att ansluta Azure Databricks med Microsoft Excel-, Python- eller R-språk. När du har upprättat anslutningen kan du komma åt data i Azure Databricks från Excel-, Python- eller R-klienterna. Du kan också använda klienterna för att ytterligare analysera data. 

## <a name="prerequisites"></a>Krav

* Du måste ha en Azure Databricks-arbetsyta, ett Spark-kluster och exempeldata som är associerade med klustret. Om du inte redan har dessa förutsättningar slutför du snabbstarten på [Kör ett Spark-jobb på Azure Databricks med Azure-portalen](quickstart-create-databricks-workspace-portal.md).

* Ladda ner Databricks ODBC drivrutin från [Databricks drivrutin nedladdningssida](https://databricks.com/spark/odbc-driver-download). Installera 32- eller 64-bitarsversionen beroende på vilket program du vill ansluta till Azure Databricks. Om du till exempel vill ansluta från Excel installerar du 32-bitarsversionen av drivrutinen. Om du vill ansluta från R och Python installerar du 64-bitarsversionen av drivrutinen.

* Skapa en personlig åtkomsttoken i Databricks. Instruktioner finns i [Tokenhantering](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Konfigurera ett DSN

Ett datakällnamn (DSN) innehåller information om en viss datakälla. En ODBC-drivrutin behöver det här DSN för att ansluta till en datakälla. I det här avsnittet konfigurerar du ett DSN som kan användas med Databricks ODBC-drivrutinen för att ansluta till Azure Databricks från klienter som Microsoft Excel, Python eller R.

1. Från Arbetsytan Azure Databricks navigerar du till Databricks-klustret.

    ![Öppna Databricks-kluster](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Öppna Databricks-kluster")

2. Klicka på fliken **JDBC/ODBC** under fliken **Konfiguration** och kopiera värdena för **Servervärdnamn** och **HTTP-sökväg**. Du behöver dessa värden för att slutföra stegen i den här artikeln.

    ![Hämta konfigurationen för Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Hämta konfigurationen för Databricks")

3. Starta **ODBC-datakällor** (32- eller 64-bitars) beroende på programmet på datorn. Om du vill ansluta från Excel använder du 32-bitarsversionen. Om du vill ansluta från R och Python använder du 64-bitarsversionen.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Starta ODBC-appen")

4. Klicka på **Lägg till**under fliken **Användar-DSN.** I dialogrutan **Skapa ny datakälla** markerar du **Simba Spark ODBC-drivrutinen**och klickar sedan på **Slutför**.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Starta ODBC-appen")

5. Ange följande värden i dialogrutan **Simba Spark ODBC Driver:**

    ![Konfigurera DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurera DSN")

    I följande tabell finns information om de värden som ska tillhandahållas i dialogrutan.
    
    |Field  | Värde  |
    |---------|---------|
    |**Namn på datakälla**     | Ange ett namn för datakällan.        |
    |**Värd/program (er)**     | Ange det värde som du kopierade från Databricks-arbetsytan för *Server-värdnamn*.        |
    |**Port**     | Skriv in *443*.        |
    |**Autentiseringsmekanism** > **Mechanism**     | Välj *Användarnamn och lösenord*.        |
    |**Användarnamn**     | Ange *token*.        |
    |**Lösenord**     | Ange tokenvärdet som du kopierade från Databricks-arbetsytan. |
    
    Utför följande ytterligare steg i dialogrutan Konfigurera DSN.
    
    * Klicka på **HTTP-alternativ**. I dialogrutan som öppnas klistrar du in värdet för *HTTP-sökvägen* som du kopierade från Databricks arbetsyta. Klicka på **OK**.
    * Klicka på **SSL-alternativ**. Markera kryssrutan **Aktivera SSL** i dialogrutan som öppnas. Klicka på **OK**.
    * Klicka på **Testa** om du vill testa anslutningen till Azure Databricks. Spara ändringarna genom att klicka på **OK**.
    * Klicka på **OK**i dialogrutan **ODBC-datakällaadministratör.**

Du har nu din DSN inrättas. I nästa avsnitt använder du det här DSN för att ansluta till Azure Databricks från Excel, Python eller R.

## <a name="connect-from-microsoft-excel"></a>Ansluta från Microsoft Excel

I det här avsnittet hämtar du data från Azure Databricks till Microsoft Excel med hjälp av det DSN som du skapade tidigare. Innan du börjar kontrollerar du att microsoft Excel är installerat på datorn. Du kan använda en utvärderingsversion av Excel från [Testlänken](https://products.office.com/excel)i Microsoft Excel .

1. Öppna en tom arbetsbok i Microsoft Excel. Klicka på Hämta **data**i menyfliksområdet **Data** . Klicka på **Från andra källor** och klicka sedan på Från **ODBC**.

    ![Starta ODBC från Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Starta ODBC från Excel")

2. Markera det DSN som du skapade tidigare i dialogrutan **Från ODBC** och klicka sedan på **OK**.

    ![Välj DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Välj DSN")

3. Om du uppmanas att ange autentiseringsuppgifter anger **du token**för användarnamn . För lösenord anger du tokenvärdet som du hämtade från Databricks-arbetsytan.

    ![Ange autentiseringsuppgifter för Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Välj DSN")

4. Välj tabellen i Databricks som du vill läsa in i Excel i navigeringsfönstret och klicka sedan på **Läs in**. 

    ![Ladda dta i Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Ladda dta i Excel")

När du har data i Excel-arbetsboken kan du utföra analytiska åtgärder på den.

## <a name="connect-from-r"></a>Anslut från R

> [!NOTE]
> Det här avsnittet innehåller information om hur du integrerar en R Studio-klient som körs på skrivbordet med Azure Databricks. Instruktioner om hur du använder R Studio i Azure Databricks-klustret finns i [R Studio på Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

I det här avsnittet använder du ett R-språk IDE för att referera till data som är tillgängliga i Azure Databricks. Innan du börjar måste du ha följande installerat på datorn.

* Ett IDE för R-språk. I den här artikeln används RStudio för skrivbord. Du kan installera den från [R Studio nedladdning](https://www.rstudio.com/products/rstudio/download/).
* Om du använder RStudio för skrivbordet som IDE [https://aka.ms/rclient/](https://aka.ms/rclient/)installerar du även Microsoft R-klienten från . 

Öppna RStudio och gör följande:

- Referera `RODBC` till paketet. På så sätt kan du ansluta till Azure Databricks med hjälp av DSN som du skapade tidigare.
- Upprätta en anslutning med DSN.
- Kör en SQL-fråga på data i Azure Databricks. I följande kodavsnitt är *radio_sample_data* en tabell som redan finns i Azure Databricks.
- Utför vissa åtgärder på frågan för att verifiera utdata. 

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

I det här avsnittet använder du en Python IDE (till exempel IDLE) för att referera till data som är tillgängliga i Azure Databricks. Innan du börjar fyller du i följande förutsättningar:

* Installera Python [härifrån](https://www.python.org/downloads/). Installera Python från den här länken installerar också IDLE.

* Installera paketet från `pyodbc` en kommandotolk på datorn. Kör följande kommando:

      pip install pyodbc

Öppna IDLE och gör följande:

- Importera `pyodbc` paketet. På så sätt kan du ansluta till Azure Databricks med hjälp av DSN som du skapade tidigare.
- Upprätta en anslutning med hjälp av det DSN som du skapade tidigare.
-  Kör en SQL-fråga med den anslutning som du skapade. I följande kodavsnitt är *radio_sample_data* en tabell som redan finns i Azure Databricks.
- Utför åtgärder på frågan för att verifiera utdata.

Följande kodavsnitt utför dessa uppgifter:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Nästa steg

* Mer information om källor där du kan importera data till Azure Databricks finns i [Datakällor för Azure Databricks](/azure/databricks/data/data-sources/index)


