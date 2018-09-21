---
title: Lagra data med Azure IoT Edge SQL-modulen | Microsoft Docs
description: Lär dig hur du lagrar data lokalt på din IoT Edge-enhet med en SQL Server-modul
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 19d2472b526c38880f9241ec448f8a9d4a327f2a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294194"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Självstudie: Lagra data på gränsen med SQL Server-databaser

Använda Azure IoT Edge och SQL Server för att lagra och fråga efter data på gränsen. Azure IoT Edge har inbyggda grundläggande lagringsfunktioner som cachelagrar meddelanden om en enhet tas offline och sedan vidarebefordrar dem när anslutningen återupprättas. Du kanske behöver mer avancerade funktioner, som t.ex. att kunna fråga efter data lokalt. IoT Edge-enheterna kan utföra mer komplexa beräkningar utan att behöva underhålla en anslutning till IoT Hub om en lokal databas läggs till. Exempelvis kan en fälttekniker visualisera sensordata från de senaste dagarna på en lokal dator, även om dessa data bara överförs till molnet en gång i månaden. Detta gör det lättare att förbättra maskininlärningsmodeller.

Den här artikeln innehåller instruktioner för hur man distribuerar en SQL Server-databas till en IoT Edge-enhet. Azure Functions körs på IoT Edge-enheten och strukturerar inkommande data och skickar dem sedan till databasen. Stegen i den här artikeln kan också tillämpas på andra databaser som fungerar i containrar, t.ex. MySQL eller PostgreSQL.

I den här guiden får du lära dig att: 

> [!div class="checklist"]
> * Använd Visual Studio Code för att skapa en Azure-funktion
> * Distribuera en SQL-databas till din IoT Edge-enhet
> * Använd Visual Studio Code för att bygga moduler och distribuera dem till din IoT Edge-enhet
> * Visa genererade data

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägget (tillhandahålls av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) för Visual Studio Code. 
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Skapa ett containerregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

    ![skapa containerregister](./media/tutorial-deploy-function/create-container-registry.png)

2. Namnge registret och välj en prenumeration.
3. För resursgruppen rekommenderar vi att du använder det resursgruppsnamn som innehåller din IoT Hub. Genom att lägga alla resurser i en grupp, kan du hantera dem tillsammans. Till exempel tas alla testresurser som ingår i gruppen bort om resursgruppen för testning tas bort. 
4. Sätt SKU:n till **Basic** och ändra **Administratörsanvändare** till **Aktivera**. 
5. Klicka på **Skapa**.
6. När du har skapat ditt containerregister, navigerar du till det och väljer **Åtkomstnycklar**. 
7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Om du vill skicka data till en databas behöver du en modul som kan strukturera data korrekt och lagra den i en tabell. 

Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT Edge-tillägget.

1. Öppna Visual Studio Code.
2. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**.
3. Öppna kommandopaletten i VS Code genom att välja **Visa** > **Kommandopalett**.
4. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 
   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **Azure Functions – C#** som mall för modulen. 
   4. Ge modulen namnet **sqlFunction**. 
   5. Ange det Azure Container Registry som du skapade i föregående avsnitt som avbildningslagringsplats för din första modul. Ersätt **localhost:5000** med serverinloggningsvärdet som du kopierade. Den slutliga strängen ser ut så här: **\<registernamn\>.azurecr.io/.azurecr.io/sqlFunction**.

4. VS Code läser in arbetsytan för IoT Edge-lösningen. Det finns en **modules**-mapp, en **.vscode**-mapp en mallfil för distributionsmanifestet. Öppna **moduler** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Ersätt innehållet i filen med följande kod:

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
           const string str = "<sql connection string>";
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

6. Ersätt strängen **\<sql-anslutningssträng\>** i rad 24 med följande sträng. Egenskapen för **datakälla** refererar till SQL Server-containernamnet **SQL** som du skapar i nästa avsnitt. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Spara filen **run.csx**. 

## <a name="add-a-sql-server-container"></a>Lägga till en SQL Server-container

Ett [distributionsmanifest](module-composition.md) deklarerar vilka moduler IoT Edge-körningen kommer installera på din IoT Edge-enhet. Du lade till koden för att skapa en anpassad Functions-modul i föregående avsnitt, men modulen SQL Server har redan skapats. Du behöver bara tala om för IoT Edge-körningen att inkludera den och sedan konfigurera den på din enhet. 

1. Öppna filen **deployment.template.json** i Visual Studio Code-utforskaren. 
2. Leta upp avsnittet **moduleContent.$edgeAgent.properties.desired.modules**. Två moduler bör listas: **tempSensor**, som genererar simulerade data, och din **sqlFunction**-modul.
3. Om du använder Windows-containrar ändrar du avsnittet **sqlFunction.settings.image**.
    ```json
    "image": "${MODULES.sqlFunction.windows-amd64}"
    ```
4. Lägg till följande kod för att deklarera en tredje modul. Lägg till ett kommatecken efter avsnittet sqlFunction och infoga:

   ```json
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
   ```

   Här är ett exempel ifall du är osäker på hur du lägger till ett JSON-element. ![Lägga till en SQL Server-container](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. Beroende på typen av Docker-containrar på din IoT Edge-enhet uppdaterar du **sql.settings**-parametrarna med följande kod:

   * Windows-containrar:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux-containrar:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Varje gång du skapar en SQL Server-container i en produktionsmiljö bör du [ändra standardlösenord för systemadministratören](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Spara filen **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en lösning med en modul och lade sedan till en annan till distributionsmanifestet. Nu måste du skapa lösningen, skapa containeravbildningar för modulerna och push-överföra avbildningarna till ditt containerregister. 

1. I .env-filen ger du IoT Edge-körningen autentiseringsuppgifterna för registret så att den kan komma åt modulavbildningarna. Leta upp avsnitten **CONTAINER_REGISTRY_USERNAME** och **CONTAINER_REGISTRY_PASSWORD** och infoga dina autentiseringsuppgifter efter lika med-symbolen: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourContainerReg=<username>
   CONTAINER_REGISTRY_PASSWORD_yourContainerReg=<password>
   ```

2. Spara .env-filen.
3. Logga in på ditt containerregister i Visual Studio Code så att du kan push-överföra avbildningarna till registret. Använd samma autentiseringsuppgifter som du lade till i .env-filen. Ange följande kommando i den integrerade terminalen:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Du kommer uppmanas att ange lösenordet. Klistra in lösenordet och tryck på **Retur**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

4. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning). 

## <a name="deploy-the-solution-to-a-device"></a>Distribuera lösningen till en enhet

Du kan ange moduler på en enhet via IoT Hub, men du kan också komma åt din IoT Hub och enheter via Visual Studio Code. I det här avsnittet kan du konfigurera åtkomst till din IoT Hub och sedan använda VS Code för att distribuera din lösning till IoT Edge-enheten. 

1. Välj **Azure IoT Hub: Välj IoT Hub** i kommandopaletten för VS Code.
2. Följ anvisningarna för att logga in på ditt Azure-konto. 
3. Välj din Azure-prenumeration och sedan din IoT Hub i kommandopaletten. 
4. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 
5. Högerklicka på den enhet som du vill ha som mål för distributionen och välj **Skapa distribution för enskild enhet**. 
6. I filutforskaren går du till **config**-mappen i din lösning och väljer **deployment.json**. Klicka på **Välj distributionsmanifest för Edge**. 

Om distributionen lyckas skrivs ett bekräftelsemeddelande ut i utdata för VS Code. Du kan också kontrollera att alla moduler är igång på enheten. 

Kör följande kommando på IoT Edge-enheten för att se status för modulerna. Det kan ta några minuter.

   ```PowerShell
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Skapa SQL-databasen

När du applicerar distributionsmanifestet på din enhet körs tre moduler. Modulen tempSensor genererar simulerade miljödata. Modulen sqlFunction hämtar data och formaterar dem för en databas. 

Det här avsnittet hjälper dig att konfigurera SQL-databasen för lagring av temperaturdata. 

1. Anslut till databasen med en kommandorad. 
   * Windows-container:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Linux-container: 

      ```bash
      docker exec -it sql bash
      ```

2. Öppna SQL-kommandoverktyget.
   * Windows-container:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Linux-container: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Skapa databasen: 

   * Windows-container
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Linux-container
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Definiera tabellen.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Du kan anpassa din SQL Server Docker-fil så att SQL Server automatiskt konfigureras för distribuering till flera IoT Edge-enheter. Mer information finns i [demoprojektet för Microsoft SQL Server-containern](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Visa lokala data

När din tabell har skapats börjar modulen sqlFunction lagra data i en lokal SQL Server 2017-databas på IoT Edge-enheten. 

Kör följande kommando från SQL-kommandoverktyget för att visa formaterade tabelldata: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Visa lokala data](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure Functions-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar Azure Functions med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-function.md). 

Fortsätt med någon av följande självstudier om du vill veta mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Hitta medelvärden med ett flytande fönster i Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
