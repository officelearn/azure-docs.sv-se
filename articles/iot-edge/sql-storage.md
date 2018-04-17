---
title: Azure IoT kant SQL-modulen | Microsoft Docs
description: Lagra data på kanten med Microsoft SQL-moduler med Azure Functions för att formatera data.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d464bbfb9f38b184e47911a7224be8ec8679f0be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Lagra data på kanten med SQL Server-databaser

Använda Azure IoT-gränsenheterna för att lagra de data som genereras vid gränsen. Enheter med återkommande internet-anslutningar kan underhålla sina egna databaser och ändras tillbaka till molnet när ansluten. Enheter som har varit programmerad att skicka endast kritiska data till molnet kan spara resten av data för vanliga bulk filöverföringar. När i molnet, strukturerade data kan delas med andra Azure-tjänster, till exempel att skapa en maskininlärningsmodell. 

Den här artikeln innehåller instruktioner för att distribuera en SQL Server-databas till en IoT-enhet. Azure Functions, körs på IoT gränsenheten strukturer inkommande data och skickar dem sedan till databasen. Stegen i den här artikeln kan också tillämpas på andra databaser som fungerar i behållare som MySQL eller PostgreSQL. 

## <a name="prerequisites"></a>Förutsättningar 

Innan du börjar instruktionerna i den här artikeln bör du genomföra följande kurser:
* Distribuera Azure IoT kanten på en simulerad enhet i [Windows](tutorial-simulate-device-windows.md) eller [Linux](tutorial-simulate-device-linux.md)
* [Distribuera Azure-funktion som en IoT-Edge-modul](tutorial-deploy-function.md)

I följande artiklar krävs inte för att slutföra den här självstudiekursen, men kan ge bra kontext:
* [Kör SQL Server 2017 behållare avbildningen med Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Använda Visual Studio-koden för att utveckla och distribuera Azure Functions Azure IoT kant](how-to-vscode-develop-azure-function.md)

När du har slutfört nödvändiga självstudier ska du ha alla nödvändiga komponenter som är klar på datorn: 
* En aktiv Azure IoT-hubb.
* En IoT-Edge-enhet med minst 2 GB RAM-minne och en enhet på 2 GB.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [IoT-Edge kontrollen skript](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* AzureIoTEdgeFunction mall (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* En aktiv IoT-hubb med minst en IoT-enhet.

Windows- och Linux-behållare på x64 processorarkitekturer fungerar för den här kursen. SQL Server stöder inte ARM-processorer.

## <a name="deploy-a-sql-server-container"></a>Distribuera en SQL Server-behållare

I det här avsnittet lägger du till en MS SQL-databas den simulerade enheten IoT kant. Använda SQL Server 2017 docker behållaren image, tillgänglig som en [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) behållare och som en [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) behållare. 

### <a name="deploy-sql-server-2017"></a>Distribuera SQLServer 2017

Koden i det här avsnittet skapar som standard en behållare med ledigt Utvecklarversionen av SQL Server 2017. Om du vill köra produktion versionerna i stället Se [kör produktion behållaren bilder](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) detaljerad information. 

I steg 3 måste du lägga till skapa alternativ till SQL Server-behållare som är viktiga för att fastställa miljövariabler och persistant lagring. Den konfigurerade [miljövariabler](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) acceptera licensavtalet och ange ett lösenord. Den [persistant lagring](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) konfigureras med hjälp av [monterar](https://docs.docker.com/storage/volumes/). Monteringar skapa behållaren 2017 för SQL Server med en *sqlvolume* volymbehållare kopplade så att dina data kvarstår även om behållaren har tagits bort. 

1. Öppna den `deployment.json` filen i Visual Studio-koden. 
2. Ersätt den **moduler** i filen med följande kod: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. Ersätt den `<docker registry address>` med adressen fylls i slutförda kursen [distribuera Azure-funktion som en gräns för IoT-modul - förhandsgranskning](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function)

   >[!NOTE]
   >Behållarregistrets adressen är samma som den inloggningsserver som du kopierade från ditt register. Det bör vara i form av `<your container registry name>.azurecr.io`

4. Beroende på operativsystem som du kör uppdatera inställningarna för SQL-modulen med följande kod: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. Spara filen. 
6. Markera i paletten VS kod kommandot **kant: skapa distribution för gränsenheten**. 
7. Välj din IoT-Edge-enhets-ID.
8. Välj den `deployment.json` filen som du har uppdaterat. I utdatafönstret visas motsvarande utdata för din distribution. 
9. Om du vill starta Edge-runtime, Välj **kant: starta Edge** i paletten kommando.

>[!TIP]
>Varje gång som du skapar en SQL Server-behållare i en produktionsmiljö bör du [ändra standardlösenord](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Skapa SQL-databas

Det här avsnittet hjälper dig att ställa in SQL-databasen för att lagra data temperatur från sensorer som är anslutna till IoT gränsenheten. Om du använder en simulerad enhet, kommer dessa data från den *tempSensor* behållare. 

I ett kommandoradsverktyg för att ansluta till databasen: 

* Windows-behållaren
   ```cmd
   docker exec -it sql cmd
   ```

* Linux-behållare
   ```bash
   docker exec -it sql bash
   ```

Öppna verktyget för SQL-kommando: 

* Windows-behållaren
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux-behållare
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Skapa databasen: 

* Windows-behållaren
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux-behållare
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Definiera tabellen: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Du kan anpassa din SQL Server docker-fil att automatiskt konfigurera SQL Server ska distribueras till flera kant för IoT-enheter. Mer information finns i [Microsoft SQL Server-behållaren demo projekt](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Förstå SQL-anslutning

Vi använder vägar för att tillåta behållare kommunicera när återstående isolerade från varandra i andra självstudier. När du arbetar med en SQL Server-databas, men krävs en närmare relation. 

IoT-Edge skapar automatiskt en brygga (Linux) eller ett nätverk för NAT (Windows) när den startar. Nätverket kallas **azure-iot-kant**. Om du behöver felsöka den här anslutningen, kan du leta upp dess egenskaper på kommandoraden:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT kant kan också lösa DNS i ett behållarnamn via docker, så du inte behöver referera till SQL Server-databasen genom att dess IP-adress. 

T.ex är här den anslutningssträng som vi använder i nästa avsnitt: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Du kan se att anslutningssträngen refererar behållaren med namnet **sql**. Om du har ändrat Modulnamn ska vara något annat att uppdatera den här anslutningssträngen. Annars fortsätter du med nästa avsnitt. 

## <a name="update-your-azure-function"></a>Uppdatera din Azure-funktion
Uppdatera FilterFunction Azure-funktion som du gjort i föregående självstudiekursen för att skicka data till databasen. Ändra den här filen så att data som tagits emot av din sensorer strukturer därefter lagras i en SQLtabell. 

1. Öppna mappen FilterFunction i Visual Studio-koden. 
2. Ersätt filen run.csx med följande kod som innehåller SQL-anslutningssträng från föregående avsnitt: 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>Uppdatera behållaren avbildningen

Om du vill tillämpa de ändringar som du har gjort Uppdatera behållaren avbildningen, publicera den och starta om IoT kant.

1. I Visual Studio Code expanderar den **Docker** mapp. 
2. Beroende på vilken plattform som du använder, expandera antingen den **windows nano** eller **linux x64** mapp. 
3. Högerklicka på den **Dockerfile** fil och markera **skapa IoT kant modulen Docker bild**.
4. Navigera till den **FilterFunction** projektmappen, och klicka på **Välj mapp som EXE_DIR**.
5. I popup-textrutan överst på VS Code-fönstret anger du avbildningens namn. Till exempel `<your container registry address>/filterfunction:latest`. Om du distribuerar till lokala registret namnet ska vara `<localhost:5000/filterfunction:latest>`.
6. I paletten VS kod kommandot väljer **kant: Push-gräns för IoT-modulen Docker bild**. 
7. Ange samma avbildningens namn i textrutan popup. 
8. Markera i paletten VS kod kommandot **kant: starta om Edge**.

## <a name="view-the-local-data"></a>Visa lokala data

När din behållare starta lagras data från temperatursensorer i en lokal databas för SQL Server 2017 på enheten IoT kant. 

I ett kommandoradsverktyg för att ansluta till databasen: 

* Windows-behållaren
   ```cmd
   docker exec -it sql cmd
   ```

* Linux-behållare
   ```bash
   docker exec -it sql bash
   ```

Öppna verktyget för SQL-kommando: 

* Windows-behållaren
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux-behållare
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Visa dina data: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [Konfigurera SQL Server 2017 behållare bilder på Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Besök den [mssql docker GitHub-lagringsplatsen](https://github.com/Microsoft/mssql-docker) för resurser, feedback och kända problem.
