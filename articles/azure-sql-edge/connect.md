---
title: Anslut och fråga Azure SQL Edge (för hands version)
description: Lär dig mer om att ansluta och skicka frågor till Azure SQL Edge (för hands version)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e7034c3f664eeba802341510b109ba9cc57845a8
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235138"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Anslut och fråga Azure SQL Edge (för hands version)

När du har distribuerat Azure SQL Edge-behållaren kan du ansluta till SQL Database-motorn från någon av följande platser.

- Inuti behållaren
- Från en annan Docker-behållare som körs på samma värd.
- Från värddatorn
- Från andra klient datorer i nätverket.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Verktyg för att ansluta till Azure SQL Edge

Anslutningar till en Azure SQL Edge-instans kan göras från något av de vanliga verktygen som anges nedan.

* [SQLCMD](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) -SQLCMD-klient verktyg ingår redan i Azure SQL Edge-behållar avbildningen. Om du ansluter till en behållare som körs med ett interaktivt bash-gränssnitt kan du köra verktygen lokalt.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio-koden](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Om du vill ansluta till en Azure SQL Edge-databasmotor från en nätverks dator behöver du följande

- *IP-adress eller nätverks namn för värddatorn* – det här är värd datorn där Azure SQL Edge-behållaren körs.
- *Port mappning för Azure SQL Edge-behållare* – det här är port mappningen för Docker-containerns port till en port på värden. Inom containerns SQL Edge mappas alltid till port 1433. Detta kan ändras som en del av Azure SQL Edge-distributionen. Om du vill ändra port numret uppdaterar du "behållar skapande alternativ" för SQL Edge-modulen i Azure IoT Edge. I exemplet nedan mappas port 1433 i behållaren till port 1600 på värden.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *Sa-lösenord för SQL Edge-instansen* – detta är det värde som har angetts för **SA_PASSWORD** -miljövariabeln under SQL Edge-distribution.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Ansluta till databas motorn inifrån behållaren

[Kommando rads verktygen för SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) ingår i Azure SQL Edge-behållar avbildningen. Om du ansluter till behållaren med en interaktiv kommando tolk kan du köra verktygen lokalt.

1. Använd `docker exec -it` kommandot för att starta ett interaktivt bash-gränssnitt i den behållare som körs. I följande exempel `e69e056c702d` är container-ID: t.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Du behöver inte alltid ange hela container-ID: t. Du behöver bara ange tillräckligt många tecken för att unikt identifiera det. I det här exemplet kan det vara tillräckligt för att använda `e6` eller i `e69` stället för det fullständiga ID: t.

2. När du är i behållaren ansluter du lokalt med SQLCMD. SQLCMD finns inte som standard i sökvägen, så du måste ange den fullständiga sökvägen.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. När du är färdig med SQLCMD skriver du `exit` .

4. När du är färdig med det interaktiva kommando tolken skriver du `exit` . Din behållare fortsätter att köras när du har avslutat det interaktiva bash-gränssnittet.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Anslut till SQL Edge från en annan behållare på samma värd

Eftersom två behållare som körs på samma värd finns i samma Docker-nätverk, kan de enkelt nås med hjälp av behållarens namn och port adressen för tjänsten. Om du till exempel ansluter till SQL Edge-instansen från en annan python-modul (container) på samma värd kan du använda en anslutnings sträng som liknar följande. Exemplet nedan förutsätter att SQL Edge är konfigurerat för att lyssna på standard porten.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Ansluta till SQL Edge från en annan nätverks dator

Om du vill ansluta till SQL Edge-instansen från en annan dator i nätverket måste du använda IP-adressen för Docker-värden och den värd port som SQL Edge-behållaren är mappad till. Om t. ex. IP-adressen för Docker-värden är * xxx.xxx.xxx.xxx "och SQL Edge-container mappas till värd port *1600*, skulle Server adressen för SQL Edge-instans vara **xxx. xxx. xxx. xxx, 1600**. Det uppdaterade python-skriptet skulle vara

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Om du vill ansluta till en SQL Edge-instans med SQL Server Management Studio som körs på en Windows-dator, se [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Om du vill ansluta till en SQL Edge-instans med Visual Studio Code på en Windows-, Mac-eller Linux-dator refererar du till [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

För att ansluta till en SQL Edge-instans med Azure Data Studio på Windows, Mac-eller Linux-dator refererar [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Se även

[Anslut och fråga](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Installera SQL Server verktyg på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
