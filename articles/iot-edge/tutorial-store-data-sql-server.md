---
title: Självstudiekurs - Lagra data med SQL-modul med Azure IoT Edge
description: Den här självstudien visar hur du lagrar data lokalt på din IoT Edge-enhet med en SQL Server-modul
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d1b5ea9a9f78bc8a83159a34026d58d7a8cc89b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944278"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Självstudie: Lagra data på gränsen med SQL Server-databaser

Distribuera en SQL Server-modul för att lagra data på en Linux-enhet som kör Azure IoT Edge.

Använda Azure IoT Edge och SQL Server för att lagra och fråga efter data på gränsen. Azure IoT Edge har grundläggande lagringsfunktioner för att cachelagra meddelanden om en enhet tas offline och sedan vidarebefordra dem när anslutningen återupprättas. Du kanske behöver mer avancerade funktioner, som t.ex. att kunna fråga efter data lokalt. Dina IoT Edge-enheter kan använda lokala databaser för att utföra mer komplexa datorer utan att behöva upprätthålla en anslutning till IoT Hub.

Den här artikeln innehåller instruktioner för hur man distribuerar en SQL Server-databas till en IoT Edge-enhet. Azure Functions körs på IoT Edge-enheten och strukturerar inkommande data och skickar dem sedan till databasen. Stegen i den här artikeln kan också tillämpas på andra databaser som fungerar i containrar, t.ex. MySQL eller PostgreSQL.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio Code för att skapa en Azure-funktion
> * Distribuera en SQL-databas till din IoT Edge-enhet
> * Använd Visual Studio Code för att bygga moduler och distribuera dem till din IoT Edge-enhet
> * Visa genererade data

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att ställa in din utvecklingsmiljö för Linux-containerutveckling: [Utveckla IoT Edge-moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att fylla i den självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En AMD64 [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md).
  * ARM-enheter, som Raspberry Pis, kan inte köra SQL Server. Om du vill använda SQL på en ARM-enhet kan du registrera dig för att prova [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) i förhandsversion.
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerad för att köra Linux-behållare.

Den här självstudien använder en Azure Functions-modul för att skicka data till SQL Server. Om du vill utveckla en IoT Edge-modul med Azure Functions installerar du följande ytterligare förutsättningar på utvecklingsmaskinen:

* [C# för Visual Studio-kod (som drivs av OmniSharp) förlängning för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Om du vill skicka data till en databas behöver du en modul som kan strukturera data korrekt och lagra den i en tabell.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT-verktygen.

1. Öppna Visual Studio Code.

2. Öppna kommandopaletten VS-kod genom att välja **Visa** > **kommandopalett**.

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning:

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **SqlSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **sqlFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br>Den sista strängen ser ut som \<registernamnet\>.azurecr.io/sqlfunction. |

   VS Code läser in arbetsytan för IoT Edge-lösningen.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara filen.

### <a name="select-your-target-architecture"></a>Välj målarkitektur

För närvarande kan Visual Studio Code utveckla C-moduler för Linux AMD64 och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du riktar dig till med varje lösning, eftersom behållaren är byggd och körs på olika sätt för varje arkitekturtyp. Standard är Linux AMD64.

1. Öppna kommandopaletten och sök efter **Azure IoT Edge: Ange standardmålplattform för edge-lösning**eller välj genvägsikonen i sidofältet längst ned i fönstret.

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. För den här guiden använder vi en Ubuntu virtuell maskin som IoT Edge-enhet, så kommer att behålla standard **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna **moduler** > som**sqlFunction** > **sqlFunction.cs**i VS-kodutforskaren .

2. Ersätt hela innehållet i filen med följande kod:

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
                       using (var filteredMessage = new Message(messageBytes))
                       {
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

3. På linje 35 ersätter du ** \<strängens\> sql-anslutningssträng** med följande sträng. Egenskapen **Data Source** refererar till SQL Server-behållaren, som ännu inte finns. Du skapar den med namnet **SQL** i nästa avsnitt.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Spara filen **sqlFunction.cs**.

5. Öppna filen **sqlFunction.csproj**.

6. Leta reda på gruppen med paketreferenser och lägg till en ny för att inkludera SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Spara filen **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Lägga till SQL Server-behållaren

Ett [distributionsmanifest](module-composition.md) deklarerar vilka moduler IoT Edge-körningen kommer installera på din IoT Edge-enhet. Du angav koden för att skapa en anpassad funktionsmodul i föregående avsnitt, men SQL Server-modulen är redan byggd och tillgänglig på Azure Marketplace. Du behöver bara tala om för IoT Edge-körningen att inkludera den och sedan konfigurera den på din enhet.

1. Öppna kommandopaletten i Visual Studio-kod genom att välja **Visa** > **kommandopalett**.

2. Skriv och kör kommandot **Azure IoT Edge: Add IoT Edge-modulen**i kommandopaletten . Ange följande information i kommandopaletten:

   | Field | Värde |
   | ----- | ----- |
   | Välj distributionsmallfil | Kommandopaletten markerar filen deployment.template.json i den aktuella lösningsmappen. Markera den filen.  |
   | Välj modulmall | Välj **Modul från Azure Marketplace**. |

3. Sök efter och välj **SQL Server Module**på marknadsplatsen för Azure IoT Edge-modul .

4. Ändra modulnamnet till **sql**, alla gemener. Det här namnet matchar det behållarnamn som deklarerats i anslutningssträngen i sqlFunction.cs filen.

5. Välj **Importera** om du vill lägga till modulen i lösningen.

6. Öppna **filen deployment.template.json** i lösningsmappen.

7. Leta upp avsnittet **modules** (moduler). Du skulle se tre moduler. Modulen *SimulatedTemperatureSensor* ingår som standard i nya lösningar och tillhandahåller testdata som ska användas med dina andra moduler. Modulen *sqlFunction* är den modul som du först skapade och uppdaterade med ny kod. Slutligen importerades modulen *sql* från Azure Marketplace.

   >[!Tip]
   >SQL Server-modulen levereras med en standardlösenorduppsättning i miljövariablerna i distributionsmanifestet. Varje gång du skapar en SQL Server-container i en produktionsmiljö bör du [ändra standardlösenord för systemadministratören](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Stäng **filen deployment.template.json.**

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en lösning med en modul och lade sedan till en annan till distributionsmanifestet. SQL Server-modulen är värd för Microsoft, men du måste behålla koden i modulen Funktioner. I det här avsnittet skapar du lösningen, skapar behållaravbildningar för sqlFunction-modulen och skickar avbildningen till behållarregistret.

1. Öppna den integrerade terminalen i Visual Studio-kod genom att välja **Visa** > **terminal**.  

1. Logga in på ditt containerregister i Visual Studio Code så att du kan push-överföra avbildningarna till registret. Använd samma ACR-autentiseringsuppgifter (Azure Container Registry) som du har lagt till i .env-filen. Ange följande kommando i den integrerade terminalen:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Du kan se en säkerhetsvarning som rekommenderar användningen av parametern --password-stdin. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Mer information finns i [kommandotreferens för dockerinloggning.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. Högerklicka på filen **deployment.template.json** i VS-kodutforskaren och välj **Bygg och Push IoT Edge-lösning**.

När du talar om för Visual Studio Code att skapa din lösning, tar den först informationen i distributionsmallen och genererar en deployment.json-fil i en ny mapp med namnet **config**. Sedan körs två kommandon i den `docker build` integrerade `docker push`terminalen: och . Build-kommandot skapar din kod och containerizes modulen. Sedan skickar push-kommandot koden till behållarregistret som du angav när du initierade lösningen.

Du kan kontrollera att sqlFunction-modulen har skickats till behållarregistret. Navigera till behållarregistret i Azure-portalen. Välj **databaser** och sök efter **sqlFunction**. De andra två modulerna, SimulatedTemperatureSensor och sql, kommer inte att skickas till behållarregistret eftersom deras databaser redan finns i Microsoft-register.

## <a name="deploy-the-solution-to-a-device"></a>Distribuera lösningen till en enhet

Du kan ange moduler på en enhet via IoT Hub, men du kan också komma åt din IoT Hub och enheter via Visual Studio Code. I det här avsnittet kan du konfigurera åtkomst till din IoT Hub och sedan använda VS Code för att distribuera din lösning till IoT Edge-enheten.

1. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**.

2. Högerklicka på den enhet som du vill ha som mål för distributionen och välj **Skapa distribution för enskild enhet**.

3. I filutforskaren går du till **config**-mappen i din lösning och väljer **deployment.amd64**. Klicka på **Välj distributionsmanifest för Edge**.

   Använd inte filen deployment.template.json som ett distributionsmanifest.

Om distributionen lyckas skrivs ett bekräftelsemeddelande ut i utdata för VS Code.

Uppdatera statusen för din enhet i avsnittet Azure IoT Hub-enheter i VS Code. De nya modulerna visas och börjar rapportera som körande under de närmaste minuterna när containrarna installeras och startas. Du kan också kontrollera att alla moduler är igång på enheten. Kör följande kommando på IoT Edge-enheten för att se status för modulerna.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Skapa SQL-databasen

När du applicerar distributionsmanifestet på din enhet körs tre moduler. Modulen SimulatedTemperatureSensor genererar simulerade miljödata. Modulen sqlFunction hämtar data och formaterar dem för en databas. Det här avsnittet hjälper dig att konfigurera SQL-databasen för lagring av temperaturdata.

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

Om du vill prova en annan lagringsmetod på gränsen läser du om hur du använder Azure Blob Storage på IoT Edge.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med Azure Blob Storage på IoT Edge](how-to-store-data-blob.md)
