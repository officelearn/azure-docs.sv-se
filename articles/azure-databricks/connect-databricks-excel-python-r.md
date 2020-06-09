---
title: 'Ansluta till Azure Databricks från Excel, python eller R '
description: Lär dig hur du använder Simba-drivrutinen för att ansluta Azure Databricks till Excel, python eller R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.custom: tracking-python
ms.openlocfilehash: 77b9bcf0dad8fa11fad4828649cd75d7df7806d5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561314"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ansluta till Azure Databricks från Excel, python eller R

I den här artikeln får du lära dig hur du använder Databricks ODBC-drivrutin för att ansluta Azure Databricks med Microsoft Excel, python eller R language. När du har upprättat anslutningen kan du komma åt data i Azure Databricks från Excel-, python-eller R-klienter. Du kan också använda klienterna för att analysera data ytterligare. 

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure Databricks arbets yta, ett Spark-kluster och exempel data som är kopplade till klustret. Om du inte redan har de här kraven måste du slutföra snabb starten vid [körning av ett Spark-jobb på Azure Databricks med hjälp av Azure Portal](quickstart-create-databricks-workspace-portal.md).

* Hämta Databricks ODBC-drivrutinen från [Databricks driv rutins nedladdnings sida](https://databricks.com/spark/odbc-driver-download). Installera 32-bitars-eller 64-bitars versionen beroende på vilket program du vill ansluta till Azure Databricks. Om du till exempel vill ansluta från Excel installerar du 32-bitars versionen av driv rutinen. Om du vill ansluta från R och python installerar du 64-bitars versionen av driv rutinen.

* Konfigurera en personlig åtkomsttoken i Databricks. Instruktioner finns i [token Management](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Konfigurera en DSN

Ett namn på data källan (DSN) innehåller information om en speciell data källa. En ODBC-drivrutin behöver denna DSN för att kunna ansluta till en data källa. I det här avsnittet skapar du en DSN som kan användas med Databricks ODBC-drivrutinen för att ansluta till Azure Databricks från klienter som Microsoft Excel, python eller R.

1. Gå till Databricks-klustret från arbets ytan Azure Databricks.

    ![Öppna Databricks-kluster](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Öppna Databricks-kluster")

2. Under fliken **konfiguration** klickar du på fliken **JDBC/ODBC** och kopierar värdena för **Server värdnamn** och **http-sökväg**. Du behöver dessa värden för att slutföra stegen i den här artikeln.

    ![Hämta Databricks-konfiguration](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Hämta Databricks-konfiguration")

3. Starta **ODBC data sources** -program (32-bitars eller 64-bitars) på datorn beroende på programmet. Om du vill ansluta från Excel använder du 32-bitars versionen. Om du vill ansluta från R och python använder du 64-bitars versionen.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Starta ODBC-app")

4. Under fliken **användar-DSN** klickar du på **Lägg till**. I dialog rutan **Skapa ny data källa** väljer du **Simba Spark ODBC-drivrutinen**och klickar sedan på **Slutför**.

    ![Starta ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Starta ODBC-app")

5. I dialog rutan **Simba Spark ODBC-drivrutin** anger du följande värden:

    ![Konfigurera DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurera DSN")

    Följande tabell innehåller information om de värden som ska finnas i dialog rutan.
    
    |Fält  | Värde  |
    |---------|---------|
    |**Namn på datakälla**     | Ange ett namn för data källan.        |
    |**Värd (er)**     | Ange det värde som du kopierade från Databricks-arbetsytan för *Server värdnamn*.        |
    |**Port**     | Ange *443*.        |
    |**Autentisering**  >  **Mekanism**     | Välj *användar namn och lösen ord*.        |
    |**Användarnamn**     | Ange *token*.        |
    |**Lösenord**     | Ange det token-värde som du kopierade från arbets ytan Databricks. |
    
    Utför följande ytterligare steg i dialog rutan DSN-konfiguration.
    
    * Klicka på **http-alternativ**. Klistra in värdet för *http-sökväg* som du kopierade från Databricks-arbetsytan i dialog rutan som öppnas. Klicka på **OK**.
    * Klicka på **SSL-alternativ**. Markera kryss rutan **Aktivera SSL** i dialog rutan som öppnas. Klicka på **OK**.
    * Klicka på **testa** för att testa anslutningen till Azure Databricks. Spara ändringarna genom att klicka på **OK**.
    * I dialog rutan **ODBC-administratör för data källa** klickar du på **OK**.

Du har nu konfigurerat din DSN. I nästa avsnitt använder du denna DSN för att ansluta till Azure Databricks från Excel, python eller R.

## <a name="connect-from-microsoft-excel"></a>Anslut från Microsoft Excel

I det här avsnittet hämtar du data från Azure Databricks till Microsoft Excel med hjälp av den DSN som du skapade tidigare. Kontrol lera att Microsoft Excel är installerat på datorn innan du börjar. Du kan använda en utvärderings version av Excel från [Microsoft Excel-utvärderings länken](https://products.office.com/excel).

1. Öppna en tom arbets bok i Microsoft Excel. I menyfliksområdet **data** klickar du på **Hämta data**. Klicka på **från andra källor** och sedan på **från ODBC**.

    ![Starta ODBC från Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Starta ODBC från Excel")

2. I dialog rutan **från ODBC** väljer du den DSN som du skapade tidigare och klickar sedan på **OK**.

    ![Välj DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Välj DSN")

3. Om du uppmanas att ange autentiseringsuppgifter för användar namn anger du **token**. För lösen ord anger du det token-värde som du hämtade från arbets ytan Databricks.

    ![Ange autentiseringsuppgifter för Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Välj DSN")

4. I fönstret Navigator väljer du den tabell i Databricks som du vill läsa in till Excel och klickar sedan på **Läs in**. 

    ![Läs in DTA i Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Läs in DTA i Excel")

När du har data i din Excel-arbetsbok kan du utföra analytiska åtgärder på den.

## <a name="connect-from-r"></a>Anslut från R

> [!NOTE]
> Det här avsnittet innehåller information om hur du integrerar en R Studio-klient som körs på Skriv bordet med Azure Databricks. Instruktioner för hur du använder R Studio i själva Azure Databricks-klustret finns i [R Studio på Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

I det här avsnittet använder du en R language IDE för att referera till data som är tillgängliga i Azure Databricks. Innan du börjar måste du ha följande installerat på datorn.

* Ett IDE för R-språk. Den här artikeln använder RStudio för Desktop. Du kan installera den från [R Studio Download](https://www.rstudio.com/products/rstudio/download/).
* Om du använder RStudio för Desktop som IDE, installerar du även Microsoft R client från [https://aka.ms/rclient/](https://aka.ms/rclient/) . 

Öppna RStudio och utför följande steg:

- Referera till `RODBC` paketet. På så sätt kan du ansluta till Azure Databricks med hjälp av den DSN som du skapade tidigare.
- Upprätta en anslutning med hjälp av DSN.
- Kör en SQL-fråga på data i Azure Databricks. I följande kodfragment är *radio_sample_data* en tabell som redan finns i Azure Databricks.
- Verifiera utdata genom att utföra några åtgärder i frågan. 

Följande kodfragment utför följande uppgifter:

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

## <a name="connect-from-python"></a>Anslut från python

I det här avsnittet använder du en python IDE (till exempel inaktivitet) för referens data som är tillgängliga i Azure Databricks. Innan du börjar måste du uppfylla följande krav:

* Installera python härifrån [.](https://www.python.org/downloads/) Om du installerar python från den här länken installeras även inaktiv.

* Installera paketet från en kommando tolk på datorn `pyodbc` . Kör följande kommando:

      pip install pyodbc

Öppna inaktiv och utför följande steg:

- Importera `pyodbc` paketet. På så sätt kan du ansluta till Azure Databricks med hjälp av den DSN som du skapade tidigare.
- Upprätta en anslutning med hjälp av den DSN som du skapade tidigare.
-  Kör en SQL-fråga med den anslutning som du skapade. I följande kodfragment är *radio_sample_data* en tabell som redan finns i Azure Databricks.
- Verifiera utdata genom att utföra åtgärder på frågan.

Följande kodfragment utför följande uppgifter:

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

* Information om källor där du kan importera data till Azure Databricks finns i [data källor för Azure Databricks](/azure/databricks/data/data-sources/index)


