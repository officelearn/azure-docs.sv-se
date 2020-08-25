---
title: Distribuera Azure SQL Edge (för hands version) med hjälp av Azure Portal
description: Lär dig hur du distribuerar Azure SQL Edge (för hands version) med hjälp av Azure Portal
keywords: Distribuera SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816872"
---
# <a name="deploy-azure-sql-edge-preview"></a>Distribuera Azure SQL Edge (för hands version) 

Azure SQL Edge (för hands version) är en Relations databas motor som är optimerad för IoT och Azure IoT Edge distributioner. Det innehåller funktioner för att skapa ett högpresterande lagrings-och bearbetnings lager för IoT-program och-lösningar. Den här snabb starten visar hur du kommer igång med att skapa en Azure SQL Edge-modul via Azure IoT Edge med hjälp av Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Logga in på [Azure Portal](https://portal.azure.com/).
* Skapa ett [Azure-IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Registrera en [IoT Edge-enhet från Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Förbered IoT Edge-enheten för att [distribuera IoT Edge modul från Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Om du vill distribuera en virtuell Azure Linux-dator som en IoT Edge enhet, se den här [snabb starts guiden](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Distribuera SQL Edge-modulen från Azure Marketplace

Azure Marketplace är ett online-program och tjänster för tjänster där du kan bläddra igenom en rad olika företags program och-lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Edge kan distribueras till en Edge-enhet via Marketplace.

1. Hitta Azure SQL Edge-modulen på Azure Marketplace.<br><br>

   ![SQL Edge i MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Välj den program varu plan som bäst motsvarar dina krav och klicka på **skapa**. <br><br>

   ![Välj rätt program varu plan](media/deploy-portal/pick-correct-plan.png)

3. På sidan mål enheter för IoT Edge modul anger du följande information och klickar sedan på **skapa**

   |**Fält**  |**Beskrivning**  |
   |---------|---------|
   |Prenumeration  |  Azure-prenumerationen som IoT Hub skapades under |
   |IoT Hub   |  Namnet på IoT Hub där IoT Edges enheten är registrerad och välj sedan alternativet för att distribuera till en enhet|
   |IoT Edge enhets namn  |  Namnet på den IoT Edge enhet där SQL Edge skulle distribueras |

4. På sidan **Ange moduler** navigerar du till avsnittet om distributions moduler och klickar på **Konfigurera** mot SQL Edge-modulen. 

5. I fönstret **IoT Edge anpassade moduler** anger du önskade värden för miljövariablerna och/eller anpassar alternativen för att skapa och önskade egenskaper för modulen. En fullständig lista över miljövariabler som stöds refererar [SQL Server behållar miljö variabler](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parameter**  |**Beskrivning**|
   |---------|---------|
   | Name | Namn för modulen. |
   |SA_PASSWORD  | Ange ett starkt lösen ord för SQL Edge admin-kontot. |
   |MSSQL_LCID   | Anger språk-ID: t som ska användas för SQL Server. Till exempel är 1036 franska. |
   |MSSQL_COLLATION | Ställer in standard sortering för SQL Server. Den här inställningen åsidosätter standard mappningen för språk-ID (LCID) till sortering. |

   > [!NOTE]
   > Ändra inte eller uppdatera **avbildnings-URI: n** eller **ACCEPT_EULA** inställningarna i modulen.

6. I fönstret **IoT Edge anpassade moduler** uppdaterar du det önskade värdet för behållaren skapa alternativ för **värd porten**. Om du behöver distribuera mer än en SQL DB Edge-modul, se till att uppdatera monterings alternativet för att skapa ett nytt käll & mål par för den permanenta volymen. Mer information om monteringar och volymer finns i [använda volymer](https://docs.docker.com/storage/volumes/) i Docker-dokumentationen. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. I fönstret **IoT Edge anpassade moduler** uppdaterar du *önskade egenskaper för set-modulen* för att inkludera platsen för SQL-paketet och informationen om Stream Analytics-jobbet. Dessa två fält är valfria och ska användas om du vill distribuera SQL Edge-modulen med en databas och ett strömmande jobb.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. I fönstret **IoT Edge anpassade moduler** anger du *starta om principen* till alltid och *önskad status* för att köra.
9. I fönstret **IoT Edge anpassade moduler** klickar du på **Spara**.
10. Klicka på **Nästa**på sidan **Ange moduler** .
11. På sidan **Ange väg (valfritt)** på sidan **Ange moduler** anger du vägarna för modulen till modulen eller modulen för att IoT Edge Hub-kommunikation se [distribuera moduler och upprätta vägar i IoT Edge](../iot-edge/module-composition.md).
12. Klicka på **Nästa**.
13. Klicka på **Skicka**.

## <a name="connect-to-azure-sql-edge"></a>Ansluta till Azure SQL Edge

I följande steg används kommando rads verktyget för Azure SQL Edge, **SQLCMD**, inuti behållaren för att ansluta till Azure SQL Edge.

> [!NOTE]
> SQLCMD-verktyget är inte tillgängligt i ARM64-versionen av SQL Edge-behållare.

1. Använd `docker exec -it` kommandot för att starta ett interaktivt bash-gränssnitt i den behållare som körs. I följande exempel `azuresqledge` är namnet som anges av `Name` parametern för din IoT Edge-modul.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. När du är i behållaren ansluter du lokalt med SQLCMD. SQLCMD finns inte som standard i sökvägen, så du måste ange den fullständiga sökvägen.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Du kan utelämna lösen ordet på kommando raden för att uppmanas att ange det.

3. Om det lyckas, bör du komma till en **SQLCMD** kommando tolk: `1>` .

## <a name="create-and-query-data"></a>Skapa och fråga efter data

I följande avsnitt får du stegvisa anvisningar genom att använda **SQLCMD** och Transact-SQL för att skapa en ny databas, lägga till data och köra en enkel fråga.

### <a name="create-a-new-database"></a>Skapa en ny databas

Följande steg skapar en ny databas med namnet `TestDB` .

1. I kommando tolken **SQLCMD** klistrar du in följande Transact-SQL-kommando för att skapa en test databas:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. På nästa rad skriver du en fråga för att returnera namnet på alla databaser på servern:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Infoga data

Skapa sedan en ny tabell, `Inventory` och infoga två nya rader.

1. Växla kontext till den nya databasen från kommando tolken i **SQLCMD** `TestDB` :

   ```sql
   USE TestDB
   ```

2. Skapa en ny tabell med namnet `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Infoga data i den nya tabellen:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Skriv `GO` för att köra föregående kommandon:

   ```sql
   GO
   ```

### <a name="select-data"></a>Välj data

Kör nu en fråga för att returnera data från `Inventory` tabellen.

1. Från kommando tolken **SQLCMD** anger du en fråga som returnerar rader från `Inventory` tabellen där antalet är större än 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Kör kommandot:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Avsluta kommando tolken SQLCMD

1. För att avsluta **SQLCMD** -sessionen skriver du `QUIT` :

   ```sql
   QUIT
   ```

2. Om du vill avsluta den interaktiva kommando tolken i behållaren skriver du `exit` . Din behållare fortsätter att köras när du har avslutat det interaktiva bash-gränssnittet.

## <a name="connect-from-outside-the-container"></a>Anslut utanför behållaren

Du kan ansluta och köra SQL-frågor mot din Azure SQL Edge-instans från alla externa Linux-, Windows-eller macOS-verktyg som stöder SQL-anslutningar. Mer information om hur du ansluter till en SQL Edge-behållare från utsidan finns i [Anslut och fråga Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/connect).

I den här snabb starten har du distribuerat en SQL Edge-modul på en IoT Edge enhet. 

## <a name="next-steps"></a>Nästa steg

- [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md).
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge med hjälp av IoT Edge](tutorial-deploy-azure-resources.md).
- [Data strömning i Azure SQL Edge](stream-data.md)
