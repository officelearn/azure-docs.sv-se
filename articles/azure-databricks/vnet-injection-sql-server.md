---
title: Fråga en SQL Server Linux Docker-behållare med Azure Databricks
description: I den här artikeln beskrivs hur du distribuerar Azure Databricks till ditt virtuella nätverk, även kallat VNet-injektion.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747659"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Självstudiekurs: Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks-anteckningsbok

Den här självstudien lär dig hur du integrerar Azure Databricks med en SQL Server Linux Docker-behållare i ett virtuellt nätverk. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Distribuera en Azure Databricks-arbetsyta till ett virtuellt nätverk
> * Installera en virtuell Linux-dator i ett offentligt nätverk
> * Installera Docker
> * Installera Microsoft SQL Server på Linux docker-behållare
> * Fråga SQL Server med JDBC från en Databricks-anteckningsbok

## <a name="prerequisites"></a>Krav

* Skapa en [Databricks-arbetsyta i ett virtuellt nätverk](quickstart-create-databricks-workspace-vnet-injection.md).

* Installera [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Ladda ned [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

1. I Azure-portalen väljer du ikonen för **virtuella datorer**. Välj sedan **+ Lägg till**.

    ![Lägg till ny virtuell Azure-dator](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Välj Ubuntu Server 18.04 LTS på fliken **Grunderna** och ändra storleken på den virtuella datorn till B2s. Välj ett användarnamn och lösenord för administratören.

    ![Fliken Grunderna i ny konfiguration av virtuella datorer](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navigera till fliken **Nätverk.** Välj det virtuella nätverket och det offentliga undernätet som innehåller ditt Azure Databricks-kluster. Välj **Granska + skapa**och sedan **Skapa** för att distribuera den virtuella datorn.

    ![Fliken Nätverk för ny konfiguration av virtuella datorer](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. När distributionen är klar navigerar du till den virtuella datorn. Lägg märke till den offentliga IP-adressen och det virtuella nätverket/undernätet i **översikten**. Välj den **offentliga IP-adressen**

    ![Översikt över virtuella datorer](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Ändra **tilldelningen** till **Statisk** och ange en **DNS-namnetikett**. Välj **Spara**och starta om den virtuella datorn.

    ![Konfiguration av offentlig IP-adress](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Välj fliken **Nätverk** under **Inställningar**. Observera att nätverkssäkerhetsgruppen som skapades under Azure Databricks-distributionen är associerad med den virtuella datorn. Välj **Lägg till inkommande portregel**.

7. Lägg till en regel för att öppna port 22 för SSH. Använd följande inställningar:
    
    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Källa|IP-adresser|IP-adresser anger att inkommande trafik från en viss källa IP-adress kommer att tillåtas eller nekas av den här regeln.|
    |Käll-IP-adresser|<din offentliga ip\>|Ange din offentliga IP-adress. Du kan hitta din offentliga IP-adress genom att besöka [bing.com](https://www.bing.com/) och söka efter **"min IP".**|
    |Källportintervall|*|Tillåt trafik från valfri port.|
    |Mål|IP-adresser|IP-adresser anger att utgående trafik för en viss källa IP-adress kommer att tillåtas eller nekas av den här regeln.|
    |Mål-IP-adressen|<din vm offentliga ip\>|Ange den virtuella datorns offentliga IP-adress. Du hittar detta på **översiktssidan för** din virtuella dator.|
    |Målportintervall|22|Öppen port 22 för SSH.|
    |Prioritet|290|Ge regeln en prioritet.|
    |Namn|ssh-databricks-tutorial-vm|Ge regeln ett namn.|


    ![Lägga till säkerhetsregel för inkommande trafik för port 22](./media/vnet-injection-sql-server/open-port.png)

8. Lägg till en regel för att öppna port 1433 för SQL med följande inställningar:

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Källa|Alla|Källa anger att inkommande trafik från en viss källa IP-adress kommer att tillåtas eller nekas av den här regeln.|
    |Källportintervall|*|Tillåt trafik från valfri port.|
    |Mål|IP-adresser|IP-adresser anger att utgående trafik för en viss källa IP-adress kommer att tillåtas eller nekas av den här regeln.|
    |Mål-IP-adressen|<din vm offentliga ip\>|Ange den virtuella datorns offentliga IP-adress. Du hittar detta på **översiktssidan för** din virtuella dator.|
    |Målportintervall|1433|Öppna port 22 för SQL Server.|
    |Prioritet|300|Ge regeln en prioritet.|
    |Namn|sql-databricks-tutorial-vm|Ge regeln ett namn.|

    ![Lägga till säkerhetsregel för inkommande trafik för port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Köra SQL Server i en Docker-behållare

1. Öppna [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), eller något annat verktyg som gör att du kan SSH i den virtuella datorn. Navigera till din virtuella dator i Azure-portalen och välj **Anslut** för att hämta det SSH-kommando som du behöver för att ansluta.

    ![Ansluta till den virtuella datorn](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ange kommandot i Ubuntu-terminalen och ange det administratörslösenord som du skapade när du konfigurerade den virtuella datorn.

    ![Ubuntu terminal SSH logga in](./media/vnet-injection-sql-server/vm-login-terminal.png)

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

1. Öppna SQL Server Management Studio och anslut till servern med servernamnet och SQL-autentiseringen. Inloggningsanvändarnamnet är **SA** och lösenordet är lösenordet som anges i Docker-kommandot. Lösenordet i exempelkommandot `Password1234`är .

    ![Ansluta till SQL Server med SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. När du har anslutit väljer du **Ny fråga** och anger följande kodavsnitt för att skapa en databas, en tabell och infoga några poster i tabellen.

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

    ![Fråga om du vill skapa en SQL Server-databas](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Fråga SQL Server från Azure Databricks

1. Navigera till din Azure Databricks-arbetsyta och kontrollera att du har skapat ett kluster som en del av förutsättningarna. Välj sedan **Skapa en anteckningsbok**. Ge anteckningsboken ett namn, välj *Python* som språk och välj det kluster du skapade.

    ![Nya inställningar för Databricks-datorer](./media/vnet-injection-sql-server/create-notebook.png)

2. Använd följande kommando för att pinga den interna IP-adressen för den virtuella SQL Server-datorn. Denna ping bör lyckas. Om inte, kontrollera att behållaren körs och granska konfigurationen för nätverkssäkerhetsgruppen (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Du kan också använda kommandot nslookup för att granska.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. När du har pingat SQL Server kan du fråga databasen och tabellerna. Kör följande python-kod:

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

När det inte längre behövs tar du bort resursgruppen, Azure Databricks-arbetsytan och alla relaterade resurser. Om du tar bort jobbet undviks onödig fakturering. Om du planerar att använda Azure Databricks-arbetsytan i framtiden kan du stoppa klustret och starta om det senare. Om du inte ska fortsätta att använda den här Azure Databricks-arbetsytan tar du bort alla resurser som du har skapat i den här självstudien med hjälp av följande steg:

1. Klicka på **Resursgrupper** på menyn till vänster i Azure-portalen och klicka sedan på namnet på resursgruppen som du skapade.

2. På resursgruppssidan väljer du **Ta bort,** skriver namnet på den resurs som ska tas bort i textrutan och väljer sedan **Ta bort** igen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du extraherar, transformerar och läser in data med Azure Databricks.
> [!div class="nextstepaction"]
> [Självstudiekurs: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
