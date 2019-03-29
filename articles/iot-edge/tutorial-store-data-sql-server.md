---
title: Självstudie om att lagra data med SQL-modulen – Azure IoT Edge | Microsoft Docs
description: Lär dig hur du lagrar data lokalt på din IoT Edge-enhet med en SQL Server-modul
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: a83b8a56a8108f86d868e3420d8368c74fba308a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578205"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Självstudie: Lagra data på gränsen med SQL Server-databaser

Använda Azure IoT Edge och SQL Server för att lagra och fråga efter data på gränsen. Azure IoT Edge har grundläggande lagringsfunktioner för att cachelagra meddelanden om en enhet tas offline och sedan vidarebefordra dem när anslutningen återupprättas. Du kanske behöver mer avancerade funktioner, som t.ex. att kunna fråga efter data lokalt. IoT Edge-enheter kan använda lokala databaser för att utföra mer komplexa databehandling utan att behöva underhålla en anslutning till IoT Hub. 

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

* Du kan använda en Azure virtuell dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux](quickstart-linux.md).
* SQL Server har endast stöd för Linux-containrar. Om du vill testa den här självstudien genom att använda en Windows-enhet som din IoT Edge-enhet måste du konfigurera den så att den använder Linux-behållare. Krav och installationssteg för konfiguration av IoT Edge-körningen för Linux-containrar i Windows finns i [Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Tillägget C# för Visual Studio Code (tillhandahålls av OmniSharp) för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 
  * Om du utvecklar på en Windows-dator, kontrollera Docker är [konfigurerad för att använda Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Azure IoT-verktyg för Visual Studio Code för att skapa en modul och en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för att lagra dina containeravbildningar. Två populära Docker-registertjänster är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

Om du inte redan har ett containerregister följer du dessa steg för att skapa ett nytt i Azure:

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

2. Skapa containerregistret genom att ange följande värden:

   | Fält | Värde | 
   | ----- | ----- |
   | Registernamn | Ange ett unikt namn. |
   | Prenumeration | Välj en prenumeration i listrutan. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. |
   | Administratörsanvändare | Ändra värdet till **Aktivera**. |
   | SKU | Välj **Grundläggande**. | 

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**. 

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare i självstudien för att ge åtkomst till containerregistret.  

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Om du vill skicka data till en databas behöver du en modul som kan strukturera data korrekt och lagra den i en tabell. 

Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT-verktygen.

1. Öppna Visual Studio Code.

2. Öppna kommandopaletten i VS Code genom att välja **Visa** > **Kommandopalett**.

3. I kommandopaletten skriver och kör du kommandot **Azure IoT Edge: Ny IoT Edge-lösning**. Ange följande information i kommandopaletten för att skapa din lösning: 

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **SqlSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **sqlFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br>Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/sqlFunction. |

   VS Code läser in arbetsytan för IoT Edge-lösningen. 
   
4. I IoT Edge-lösningen öppnar du \.env-filen. 

   När du skapar en ny IoT Edge-lösning uppmanas du av VS Code att ange dina registerautentiseringsuppgifter i \.env-filen. Den här filen är git-ignorerad, och IoT Edge-tillägget använder den senare för att tillhandahålla registeråtkomst till din IoT Edge-enhet. 

   Om du inte angav containerregistret i det föregående steget men accepterade standardmässiga localhost:5000 har du ingen \.env-fil.

5. I .env-filen ger du IoT Edge-körningen autentiseringsuppgifterna för registret så att den kan komma åt modulavbildningarna. Leta upp avsnitten **CONTAINER_REGISTRY_USERNAME** och **CONTAINER_REGISTRY_PASSWORD** och infoga dina autentiseringsuppgifter efter lika med-symbolen: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Spara .env-filen.

7. I VS Code-utforskaren öppnar du **moduler** > **sqlFunction** > **sqlFunction.cs**.

8. Ersätt hela innehållet i filen med följande kod:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
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
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

6. På rad 35 ersätter du strängen **\<sql connection string\>** med följande sträng. Den **datakälla** egenskapen refererar till den SQL Server-behållare som inte finns ännu, men du skapas med namnet **SQL** i nästa avsnitt. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Spara filen **sqlFunction.cs**. 

8. Öppna filen **sqlFunction.csproj**.

9. Hitta gruppen i paketet refererar till och lägga till en ny om du vill inkludera SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

10. Spara filen **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Lägg till SQL Server-behållare

Ett [distributionsmanifest](module-composition.md) deklarerar vilka moduler IoT Edge-körningen kommer installera på din IoT Edge-enhet. Du har angett koden för att göra en anpassad funktionsmodul i föregående avsnitt, men SQL Server-modulen är redan inbyggda och är tillgängliga på Azure Marketplace. Du behöver bara tala om för IoT Edge-körningen att inkludera den och sedan konfigurera den på din enhet. 

1. I Visual Studio Code, öppna kommandopaletten genom att välja **visa** > **kommandopaletten**.

2. I kommandopaletten skriver och kör du kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. Ange följande information för att lägga till en ny modul i kommandopaletten: 

   | Fält | Värde | 
   | ----- | ----- |
   | Välj distributionsmallfil | Kommandopaletten visar deployment.template.json-filen i din aktuella lösningsmappen. Välj filen.  |
   | Välj modulmall | Välj **modul från Azure Marketplace**. |

3. I Azure IoT Edge-modulen marketplace, Sök efter och välj **SQL Server-modul**. 

4. Ändra Modulnamn till **sql**, små bokstäver. Det här namnet matchar behållarnamn som deklarerats i anslutningssträngen i filen sqlFunction.cs. 

5. Välj **Import** att lägga till modulen i din lösning. 

6. I lösningsmappen, öppna den **deployment.template.json** fil. 

7. Leta upp avsnittet **modules** (moduler). Du bör se tre moduler. Modulen *tempSensor* ingår som standard i nya lösningar och ger testdata ska användas med dina andra moduler. Modulen *sqlFunction* är den modul som du ursprungligen skapade och uppdaterade med ny kod. Slutligen modulen *sql* har importerats från Azure Marketplace. 

   >[!Tip]
   >SQL Server-modulen som levereras med en standardlösenord som anges i miljövariablerna för distribution av manifestet. Varje gång du skapar en SQL Server-container i en produktionsmiljö bör du [ändra standardlösenord för systemadministratören](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Stäng den **deployment.template.json** fil.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en lösning med en modul och lade sedan till en annan till distributionsmanifestet. SQL Server-modulen är offentligt hos Microsoft, men du behöver behållaranpassa kod i Functions-modulen. I det här avsnittet, skapa lösningen, skapa behållaravbildningar för modulen sqlFunction och push-överför avbildningen till behållarregistret. 

1. I Visual Studio Code öppnar du den integrerade terminalen genom att välja **Visa** > **Terminal**.  

1. Logga in på ditt containerregister i Visual Studio Code så att du kan push-överföra avbildningarna till registret. Använd samma autentiseringsuppgifter för Azure Container Registry (ACR) som du lade till .env-fil. Ange följande kommando i den integrerade terminalen:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Du kanske ser en säkerhetsvarning som rekommenderar användning av parametern--lösenord stdin. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) kommandot referens. 

2. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning). 

När du instruerar Visual Studio Code att skapa din lösning hämtar den först information från distributionsmallen och genererar en .json-distributionsfil i en ny mapp med namnet **config**. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger modulen i container och push-överför sedan koden till det containerregister du angav när du initierade lösningen. 

Du kan kontrollera att modulen sqlFunction har överfört till ditt behållarregister. Gå till ditt behållarregister i Azure-portalen. Välj **databaser** och Sök efter **sqlFunction**. De andra två modulerna, tempSensor och sql, skickas inte till behållarregistret, eftersom du redan som pekar på deras databaser i Microsoft-register.

## <a name="deploy-the-solution-to-a-device"></a>Distribuera lösningen till en enhet

Du kan ange moduler på en enhet via IoT Hub, men du kan också komma åt din IoT Hub och enheter via Visual Studio Code. I det här avsnittet kan du konfigurera åtkomst till din IoT Hub och sedan använda VS Code för att distribuera din lösning till IoT Edge-enheten. 

1. I kommandopaletten för VS Code väljer du **Azure IoT Hub: Select IoT Hub** (Välj IoT-hubb).

2. Följ anvisningarna för att logga in på ditt Azure-konto. 

3. Välj din Azure-prenumeration och sedan din IoT Hub i kommandopaletten. 

4. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

5. Högerklicka på den enhet som du vill ha som mål för distributionen och välj **Skapa distribution för enskild enhet**. 

   ![Skapa distribution för en enskild enhet](./media/tutorial-store-data-sql-server/create-deployment.png)

6. I filutforskaren går du till **config**-mappen i din lösning och väljer **deployment.amd64**. Klicka på **Välj distributionsmanifest för Edge**. 

   Använd inte filen deployment.template.json som ett manifest för distribution.

Om distributionen lyckas skrivs ett bekräftelsemeddelande ut i utdata för VS Code. 

Uppdatera statusen för din enhet i avsnittet Azure IoT Hub-enheter i VS Code. De nya modulerna visas och börjar rapportera som körande under de närmaste minuterna när containrarna installeras och startas. Du kan också kontrollera att alla moduler är igång på enheten. Kör följande kommando på IoT Edge-enheten för att se status för modulerna. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Skapa SQL-databasen

När du applicerar distributionsmanifestet på din enhet körs tre moduler. Modulen tempSensor genererar simulerade miljödata. Modulen sqlFunction hämtar data och formaterar dem för en databas. Det här avsnittet hjälper dig att konfigurera SQL-databasen för lagring av temperaturdata. 

Kör följande kommandon på din IoT Edge-enhet. De kommandona ansluter till den **sql**-modul som körs på enheten och skapar en databas och en tabell som ska innehålla temperaturdata som skickas till den. 

1. I ett kommandoradsverktyg på IoT Edge-enheten ansluter du till din databas. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Öppna SQL-kommandoverktyget.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Skapa databasen: 
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

   ![Visa innehållet i en lokal databas](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure Functions-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar Azure Functions med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-function.md). 

Fortsätt med någon av följande självstudier om du vill veta mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av C#-kod](tutorial-csharp-module.md)
