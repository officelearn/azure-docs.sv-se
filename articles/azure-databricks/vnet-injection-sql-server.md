---
title: Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks-anteckningsbok
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk, även kallat VNet-inmatning.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288958"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Självstudier: Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks-anteckningsbok

Den här självstudien lär du dig hur du integrerar Azure Databricks med en SQL Server Linux Docker-behållare i ett virtuellt nätverk. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Distribuera en Azure Databricks-arbetsyta till ett virtuellt nätverk
> * Installera en Linux-dator i ett offentligt nätverk
> * Installera Docker
> * Installera Microsoft SQL Server på Linux docker-behållare
> * Fråga SQL Server med JDBC från en Databricks notebook

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa en [Databricks-arbetsyta i ett virtuellt nätverk](quickstart-create-databricks-workspace-vnet-injection.md).

* Installera [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Ladda ned [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

1. I Azure-portalen väljer du ikonen för **virtuella datorer**. Välj **+ Lägg till**.

    ![Lägg till nya Azure-dator](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. På den **grunderna** fliken väljer Ubuntu Server 16.04 LTS. Ändra VM-storleken till B1ms som har en virtuella processorer och 2 GB RAM-minne. Minimikravet för en Linux SQL Server Docker-behållare är 2 GB. Välj en administratör användarnamn och lösenord.

    ![Grunderna i fliken Konfiguration av ny virtuell dator](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navigera till den **nätverk** fliken. Välj det virtuella nätverket och den offentliga undernät som inkluderar Azure Databricks-kluster. Välj **granska + skapa**, sedan **skapa** att distribuera den virtuella datorn.

    ![Nätverksfliken med ny konfiguration av virtuell dator](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. När distributionen är färdig, gå till den virtuella datorn. Lägg märke till offentliga IP-adressen och virtuella nätverk/undernät i den **översikt**. Välj den **offentlig IP-adress**

    ![Översikt över virtuella datorer](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Ändra den **tilldelning** till **statiska** och ange en **DNS-Namnetiketten**. Välj **spara**, och starta om den virtuella datorn.

    ![Offentliga IP-adresskonfigurationen](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Välj den **nätverk** fliken **inställningar**. Observera att nätverkssäkerhetsgruppen som skapades under distributionen av Azure Databricks är associerad med den virtuella datorn. Välj **Lägg till regel för inkommande portar**.

7. Lägg till en regel för att öppna port 22 för SSH. Använd följande inställningar:
    
    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Källa|IP-adresser|IP-adresser anger den inkommande trafiken från en viss källa IP-adress ska tillåtas eller nekas av den här regeln.|
    |Käll-IP-adresser|< din offentliga IP-adress\>|Ange den offentliga IP-adressen. Du kan hitta din offentliga IP-adress genom att besöka [bing.com](https://www.bing.com/) och söka efter **”Min IP-adress”**.|
    |Källportintervall|*|Tillåta trafik från alla portar.|
    |Mål|IP-adresser|IP-adresser anger den utgående trafiken för en viss källa IP-adress ska tillåtas eller nekas av den här regeln.|
    |Mål-IP-adresser|< din vm offentlig IP-adress\>|Ange den virtuella datorns offentliga IP-adressen. Du hittar den på den **översikt** sidan för den virtuella datorn.|
    |Målportintervall|22|Öppna port 22 för SSH.|
    |Prioritet|290|Ge regeln ett prioritet.|
    |Namn|ssh-databricks-tutorial-vm|Ge regeln ett namn.|


    ![Lägg till ingående Säkerhetsregel för port 22](./media/vnet-injection-sql-server/open-port.png)

8. Lägg till en regel för att öppna port 1433 för SQL med följande inställningar:

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Källa|IP-adresser|IP-adresser anger den inkommande trafiken från en viss källa IP-adress ska tillåtas eller nekas av den här regeln.|
    |Käll-IP-adresser|10.179.0.0/16|Ange adressintervallet för det virtuella nätverket.|
    |Källportintervall|*|Tillåta trafik från alla portar.|
    |Mål|IP-adresser|IP-adresser anger den utgående trafiken för en viss källa IP-adress ska tillåtas eller nekas av den här regeln.|
    |Mål-IP-adresser|< din vm offentlig IP-adress\>|Ange den virtuella datorns offentliga IP-adressen. Du hittar den på den **översikt** sidan för den virtuella datorn.|
    |Målportintervall|1433|Öppna port 22 för SQL Server.|
    |Prioritet|300|Ge regeln ett prioritet.|
    |Namn|sql-databricks-tutorial-vm|Ge regeln ett namn.|

    ![Lägg till ingående Säkerhetsregel för port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Kör SQL Server i en Docker-behållare

1. Öppna [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), eller något annat verktyg som gör att du kan SSH till den virtuella datorn. Navigera till den virtuella datorn i Azure portal och väljer **Connect** att hämta SSH-kommandot måste du ansluta.

    ![Ansluta till den virtuella datorn](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ange kommandot i terminalen Ubuntu och ange administratörslösenordet som du skapade när du har konfigurerat den virtuella datorn.

    ![Ubuntu terminal SSH logga in](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Använd följande kommando för att installera Docker på den virtuella datorn.

    ```bash
    sudo apt-get install docker.io
    ```

    Kontrollera installationen av Docker med följande kommando:

    ```bash
    sudo docker --version
    ```

4. Installera avbildningen.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Kontrollera bilderna.

    ```bash
    sudo docker images
    ```

5. Kör behållaren från avbildningen.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Kontrollera att behållaren körs.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Skapa en SQL-databas

1. Öppna SQL Server Management Studio och Anslut till servern med servernamnet och SQL-autentisering. Logga in användarnamn är **SA** och lösenordet är det lösenord som anges i kommandot Docker. Lösenordet i detta kommando är `Password1234`.

    ![Ansluta till SQL Server med SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. När du har anslutit väljer **ny fråga** och ange följande kodavsnitt för att skapa en databas, en tabell och infoga vissa poster i tabellen.

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

    ![Fråga för att skapa en SQL Server-databas](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Fråga SQLServer från Azure Databricks

1. Gå till Azure Databricks-arbetsytan och kontrollera att du har skapat ett kluster som en del av förutsättningarna. Välj **skapa en anteckningsbok**. Ge anteckningsboken ett namn, Välj *Python* som språk och väljer det kluster som du skapade.

    ![Nya Databricks notebook-inställningarna](./media/vnet-injection-sql-server/create-notebook.png)

2. Använder du följande kommando för att pinga den interna IP-adressen för SQL Server-datorn. Den här pingen ska lyckas. Annars kan du kontrollera att behållaren körs och granska nätverkskonfigurationen security nätverkssäkerhetsgrupper (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Du kan också använda nslookup-kommando för att granska.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. När du har har en SQL Server, kan du fråga databasen och tabeller. Kör följande python-kod:

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

Ta bort resursgruppen, Azure Databricks-arbetsytan och alla relaterade resurser när de inte längre behövs. Tar bort jobbet undviker onödiga fakturering. Om du planerar att använda Azure Databricks-arbetsytan i framtiden kan du stoppa klustret och sedan starta det igen. Om du inte tänker fortsätta att använda den här Azure Databricks-arbetsytan, ta bort alla resurser som du skapade i den här självstudien med hjälp av följande steg:

1. I den vänstra menyn i Azure portal, klickar du på **resursgrupper** och klicka sedan på namnet på resursgruppen som du skapade.

2. På sidan med resursgrupper, väljer **ta bort**, skriver du namnet på resursen som ska tas bort i textrutan och välj sedan **ta bort** igen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om hur du extrahera, transformera och läsa in data med Azure Databricks.
> [!div class="nextstepaction"]
> [Självstudier: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
