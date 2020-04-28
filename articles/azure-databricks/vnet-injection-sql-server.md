---
title: Fråga en SQL Server Linux Docker-behållare med Azure Databricks
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk, även kallat VNet-injektering.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73747659"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Självstudie: fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks Notebook

I den här självstudien lär du dig att integrera Azure Databricks med en SQL Server Linux Docker-behållare i ett virtuellt nätverk. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Distribuera en Azure Databricks-arbetsyta till ett virtuellt nätverk
> * Installera en virtuell Linux-dator i ett offentligt nätverk
> * Installera Docker
> * Installera Microsoft SQL Server på Linux Docker-behållaren
> * Fråga SQL Server med JDBC från en Databricks-anteckningsbok

## <a name="prerequisites"></a>Krav

* Skapa en [Databricks-arbetsyta i ett virtuellt nätverk](quickstart-create-databricks-workspace-vnet-injection.md).

* Installera [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Ladda ned [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

1. I Azure Portal väljer du ikonen för **Virtual Machines**. Välj sedan **+ Lägg till**.

    ![Lägg till ny virtuell Azure-dator](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. På fliken **grundläggande** väljer du Ubuntu Server 18,04 LTS och ändrar storleken på den virtuella datorn till B2S. Välj ett administratörs användar namn och lösen ord.

    ![Fliken grunder i den nya konfigurationen för virtuell dator](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Gå till fliken **nätverk** . Välj det virtuella nätverk och det offentliga undernät som innehåller ditt Azure Databricks-kluster. Välj **Granska + skapa**och sedan **skapa** för att distribuera den virtuella datorn.

    ![Fliken nätverk i den nya konfigurationen för virtuell dator](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. När distributionen är klar navigerar du till den virtuella datorn. Lägg märke till den offentliga IP-adressen och det virtuella nätverket/under nätet i **översikten**. Välj den **offentliga IP-adressen**

    ![Översikt över virtuell dator](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Ändra **tilldelningen** till **statisk** och ange en **DNS-** benämning. Välj **Spara**och starta om den virtuella datorn.

    ![Konfiguration av offentlig IP-adress](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Välj fliken **nätverk** under **Inställningar**. Observera att den nätverks säkerhets grupp som skapades under Azure Databricks distributionen är kopplad till den virtuella datorn. Välj **Lägg till regel för inkommande port**.

7. Lägg till en regel för att öppna port 22 för SSH. Använd följande inställningar:
    
    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Källa|IP-adresser|IP-adresser anger att inkommande trafik från en specifik käll-IP-adress ska tillåtas eller nekas av den här regeln.|
    |Käll-IP-adresser|<din offentliga IP-adress\>|Ange din offentliga IP-adress. Du kan hitta din offentliga IP-adress genom att besöka [Bing.com](https://www.bing.com/) och söka efter **"min IP"**.|
    |Källportintervall|*|Tillåt trafik från vilken port som helst.|
    |Mål|IP-adresser|IP-adresser anger att utgående trafik för en speciell käll-IP-adress ska tillåtas eller nekas av den här regeln.|
    |Mål-IP-adressen|<din offentliga IP-adress för virtuell dator\>|Ange den virtuella datorns offentliga IP-adress. Du hittar detta på sidan **Översikt** på den virtuella datorn.|
    |Målportintervall|22|Öppna port 22 för SSH.|
    |Prioritet|290|Ge regeln en prioritet.|
    |Name|SSH-databricks – självstudie – VM|Ge regeln ett namn.|


    ![Lägg till inkommande säkerhets regel för port 22](./media/vnet-injection-sql-server/open-port.png)

8. Lägg till en regel för att öppna port 1433 för SQL med följande inställningar:

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Källa|Alla|Källa anger att inkommande trafik från en specifik käll-IP-adress ska tillåtas eller nekas av den här regeln.|
    |Källportintervall|*|Tillåt trafik från vilken port som helst.|
    |Mål|IP-adresser|IP-adresser anger att utgående trafik för en speciell käll-IP-adress ska tillåtas eller nekas av den här regeln.|
    |Mål-IP-adressen|<din offentliga IP-adress för virtuell dator\>|Ange den virtuella datorns offentliga IP-adress. Du hittar detta på sidan **Översikt** på den virtuella datorn.|
    |Målportintervall|1433|Öppna port 22 för SQL Server.|
    |Prioritet|300|Ge regeln en prioritet.|
    |Name|SQL-databricks – självstudie – VM|Ge regeln ett namn.|

    ![Lägg till inkommande säkerhets regel för port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Kör SQL Server i en Docker-behållare

1. Öppna [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)eller andra verktyg som gör det möjligt att använda SSH i den virtuella datorn. Navigera till den virtuella datorn i Azure Portal och välj **Anslut** för att hämta det SSH-kommando som du behöver ansluta.

    ![Ansluta till den virtuella datorn](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ange kommandot i Ubuntu-terminalen och ange det administratörs lösen ord som du skapade när du konfigurerade den virtuella datorn.

    ![Ubuntu Terminal SSH-inloggning](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Använd följande kommando för att installera Docker på den virtuella datorn.

    ```bash
    sudo apt-get install docker.io
    ```

    Verifiera installationen av Docker med följande kommando:

    ```bash
    sudo docker --version
    ```

4. Installera avbildningen.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Kontrol lera avbildningarna.

    ```bash
    sudo docker images
    ```

5. Kör behållaren från avbildningen.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Kontrol lera att behållaren körs.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Skapa en SQL-databas

1. Öppna SQL Server Management Studio och Anslut till servern med Server namnet och SQL-autentisering. Inloggnings-username är **sa** och lösen ordet är lösen ordet som anges i Docker-kommandot. Lösen ordet i exempel kommandot är `Password1234`.

    ![Anslut till SQL Server med SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. När du har anslutit väljer du **ny fråga** och anger följande kodfragment för att skapa en databas, en tabell och infoga några poster i tabellen.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Fråga för att skapa en SQL Server databas](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Fråga SQL Server från Azure Databricks

1. Gå till arbets ytan Azure Databricks och kontrol lera att du har skapat ett kluster som en del av kraven. Välj sedan **skapa en antecknings bok**. Ge antecknings boken ett namn, Välj *python* som språk och välj det kluster som du har skapat.

    ![Nya inställningar för Databricks Notebook](./media/vnet-injection-sql-server/create-notebook.png)

2. Använd följande kommando för att pinga den interna IP-adressen för den SQL Server virtuella datorn. Pingen ska lyckas. Om inte, verifierar du att behållaren körs och kontrollerar konfigurationen för nätverks säkerhets gruppen (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Du kan också använda nslookup-kommandot för att granska.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. När du har skickat SQL Server kan du fråga databasen och tabellerna. Kör följande python-kod:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, Azure Databricks arbets ytan och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviks onödig fakturering. Om du planerar att använda arbets ytan Azure Databricks i framtiden kan du stoppa klustret och starta om det senare. Om du inte kommer att fortsätta att använda den här Azure Databricks arbets ytan tar du bort alla resurser som du skapade i den här självstudien med hjälp av följande steg:

1. Klicka på **resurs grupper** på den vänstra menyn i Azure Portal och klicka sedan på namnet på den resurs grupp som du skapade.

2. På sidan resurs grupp väljer du **ta bort**, skriver in namnet på resursen som ska tas bort i text rutan och väljer sedan **ta bort** igen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig att extrahera, transformera och läsa in data med hjälp av Azure Databricks.
> [!div class="nextstepaction"]
> [Självstudie: extrahera, transformera och läsa in data med hjälp av Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
