---
title: Ansluta till Azure Databricks från Excel, Python eller R | Microsoft Docs
description: Lär dig hur du använder Simba driver för att ansluta Azure Databricks till Excel, Python eller R.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bbf75a03fb771aa415a26e151614cecfaa14c485
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598886"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ansluta till Azure Databricks från Excel, Python eller R

Lär dig hur du använder Databricks ODBC-drivrutinen för att ansluta Azure Databricks med Microsoft Excel, Python eller R språk i den här artikeln. När du har skapat anslutningen kan du komma åt data i Azure Databricks från Excel, Python eller R-klienter. Du kan också använda klienterna för att ytterligare analysera informationen. 

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure Databricks arbetsyta, Spark-kluster och exempeldata som är associerade med klustret. Om du inte redan har dessa krav slutföra quickstart på [köra ett jobb med Spark på Azure Databricks med Azure-portalen](quickstart-create-databricks-workspace-portal.md).

* Hämta Databricks ODBC-drivrutinen från [Databricks drivrutinen hämtningssidan](https://databricks.com/spark/odbc-driver-download). Installera 32-bitars eller 64-bitars version beroende på programmet som du vill ansluta till Azure Databricks från. Till exempel för att ansluta från Excel, installera 32-bitars version av drivrutinen. Installera 64-bitars version av drivrutinen för att ansluta från R och Python.

* Ställ in en personlig åtkomsttoken i Databricks. Instruktioner finns i [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Konfigurera en DSN

En datakälla (DSN) innehåller information om en specifik datakälla. En ODBC-drivrutinen måste denna DSN att ansluta till en datakälla. I det här avsnittet skapar du en DSN som kan användas med Databricks ODBC-drivrutinen för att ansluta till Azure Databricks från klienter som Microsoft Excel, Python eller R.

1. Gå till klustret Databricks från arbetsytan Azure Databricks.

    ![Öppna Databricks klustret](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "öppna Databricks kluster")

2. Under den **Configuration** klickar du på den **JDBC/ODBC-** fliken och kopiera värdena för **-serverns värdnamn** och **HTTP-sökväg**. Du måste dessa värden för att slutföra stegen i den här artikeln.

    ![Hämta Databricks konfiguration](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "hämta Databricks konfiguration")

3. På datorn, startar **ODBC-datakällor** programmet (32-bitars eller 64-bitars) beroende på programmet. Använd 32-bitarsversionen för att ansluta från Excel. Använd 64-bitarsversionen för att ansluta från R och Python.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "starta ODBC-program")

4. Under den **användar-DSN** klickar du på **Lägg till**. I den **Skapa ny datakälla** dialogrutan markerar du den **Simba Spark ODBC Driver**, och klicka sedan på **Slutför**.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "starta ODBC-program")

5. I den **Simba Spark ODBC Driver** dialogrutan Ange följande värden:

    ![Konfigurera DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "konfigurera DSN")

    Följande tabell innehåller information om värdena som ska ange i dialogrutan.
    
    |Fält  | Värde  |
    |---------|---------|
    |**Namn på datakälla**     | Ange ett namn för datakällan.        |
    |**Värdar**     | Ange ett värde som du kopierade från arbetsytan Databricks för *-serverns värdnamn*.        |
    |**Port**     | Ange *443*.        |
    |**Autentisering** > **mekanism**     | Välj *användarnamn och lösenord*.        |
    |**Användarnamn**     | Ange *token*.        |
    |**Lösenord**     | Ange token värde som du kopierade från arbetsytan Databricks. |
    
    Utför följande i dialogrutan DSN installation.
    
    * Klicka på **alternativ för HTTP**. I dialogrutan som öppnas, klistrar du in värdet för *HTTP-sökväg* som du kopierade från Databricks arbetsyta. Klicka på **OK**.
    * Klicka på **SSL alternativ**. I dialogrutan som öppnas väljer du den **aktivera SSL** kryssrutan. Klicka på **OK**.
    * Klicka på **testa** att testa anslutningen till Azure Databricks. Klicka på **OK** att spara konfigurationen.
    * I den **ODBC Data Source Administrator** dialogrutan klickar du på **OK**.

Nu har du din DSN ställa in. I nästa avsnitt, du kan använda den här DSN för att ansluta till Azure Databricks från Excel, Python eller R.

## <a name="connect-from-microsoft-excel"></a>Ansluta från Microsoft Excel

I det här avsnittet hämtar du data från Azure Databricks i Microsoft Excel med hjälp av det DSN-namn som du skapade tidigare. Innan du börjar bör du kontrollera att du har installerat på datorn i Microsoft Excel. Du kan använda en utvärderingsversion av Microsoft Excel från [Microsoft Excel utvärderingsversion länk](https://products.office.com/excel).

1. Öppna en tom arbetsbok i Microsoft Excel. Från den **Data** band klickar du på **hämta Data**. Klicka på **från andra källor** och klicka sedan på **från ODBC**.

    ![Starta ODBC från Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "starta ODBC från Excel")

2. I den **från ODBC** dialogrutan, Välj det DSN-namn som du skapade tidigare och klicka sedan på **OK**.

    ![Välj DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Välj DSN")

3. Om du tillfrågas om autentiseringsuppgifter för användarnamn anger **token**. Ange token värde som du hämtade från arbetsytan Databricks för lösenord.

    ![Ange autentiseringsuppgifter för Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Välj DSN")

4. Välj tabellen från fönstret navigator i Databricks som du vill läsa in till Excel och klickar sedan på **läsa in**. 

    ![Läsa in dta i Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "belastningen dta till Excel")

När du har data i Excel-arbetsboken kan du utföra analytiska åtgärder på den.

## <a name="connect-from-r"></a>Ansluta från R

I det här avsnittet använder du ett R-språk IDE kan referera till data i Azure Databricks. Innan du börjar måste du ha följande installerat på datorn.

* IDE-miljö för R språk. Den här artikeln använder RStudio för skrivbordet. Du kan installera den från [hämta R Studio](https://www.rstudio.com/products/rstudio/download/).
* Om du använder RStudio för skrivbord som din IDE också installera Microsoft R-klienten från [ http://aka.ms/rclient/ ](http://aka.ms/rclient/). 

Öppna RStudio och gör följande:

- Referens för den `RODBC` paketet. Detta gör att du kan ansluta till Azure Databricks med DSN som du skapade tidigare.
- Upprätta en anslutning med DSN.
- Kör en SQL-fråga på data i Azure Databricks. I följande fragment *radio_sample_data* är en tabell som redan finns i Azure Databricks.
- Utföra vissa åtgärder på frågan för att kontrollera utdata. 

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

I det här avsnittet använder du en Python IDE (t.ex INAKTIV) kan referera till data i Azure Databricks. Innan du börjar måste uppfylla följande krav:

* Installera Python från [här](https://www.python.org/downloads/). INAKTIVT installeras automatiskt när du installerar Python från den här länken.

* Från en kommandotolk på datorn, installera den `pyodbc` paketet. Kör följande kommando:

      pip install pyodbc

Öppna INAKTIVT och gör följande:

- Importera den `pyodbc` paketet. Detta gör att du kan ansluta till Azure Databricks med DSN som du skapade tidigare.
- Upprätta en anslutning med DSN som du skapade tidigare.
-  Kör en SQL-fråga som använder anslutningen du skapat. I följande fragment *radio_sample_data* är en tabell som redan finns i Azure Databricks.
- Utför åtgärder på frågan för att kontrollera utdata.

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

* Läs om datakällor från där du kan importera data till Azure Databricks i [datakällor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


