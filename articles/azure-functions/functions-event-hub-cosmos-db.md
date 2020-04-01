---
title: 'Självstudiekurs: Använda Java-funktioner med Azure Cosmos DB och Event Hubs'
description: Den här självstudien visar hur du använder händelser från eventhubbar för att göra uppdateringar i Azure Cosmos DB med hjälp av en funktion som är skriven i Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: b6d7b2c60e777266b1cab578b8970c1fa1c6bc50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77425331"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Självstudiekurs: Skapa en funktion i Java med en Event Hub-utlösare och en Azure Cosmos DB-utdatabindning

Den här självstudien visar hur du använder Azure Functions för att skapa en Java-funktion som analyserar en kontinuerlig ström av temperatur- och tryckdata. Händelserbubbhändelser som representerar sensoravläsningar utlöser funktionen. Funktionen bearbetar händelsedata och lägger sedan till statusposter i en Azure Cosmos DB.

I den här självstudien ska du:

> [!div class="checklist"]
> * Skapa och konfigurera Azure-resurser med Hjälp av Azure CLI.
> * Skapa och testa Java-funktioner som interagerar med dessa resurser.
> * Distribuera dina funktioner till Azure och övervaka dem med Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudien måste du ha följande installerat:

* [Java Developer Kit](https://aka.ms/azure-jdks), version 8
* [Apache Maven](https://maven.apache.org), version 3.0 eller senare
* [Azure CLI](/cli/azure/install-azure-cli) om du föredrar att inte använda Cloud Shell
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) version 2.6.666 eller senare

> [!IMPORTANT]
> Miljövariabeln `JAVA_HOME` måste ställas in på installationsplatsen för JDK för att slutföra den här självstudien.

Om du föredrar att använda koden för den här självstudien direkt, se [java-funktioner-eventhub-cosmosdb-exemplet](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) repo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Skapa Azure-resurser

I den här självstudien behöver du följande resurser:

* En resursgrupp som innehåller andra resurser
* Namnområde, händelsenav och auktoriseringsregel för händelsehubbar
* Ett Cosmos DB-konto, en databas och en samling
* En funktionsapp och ett lagringskonto som ska vara värd för den

Följande avsnitt visar hur du skapar dessa resurser med hjälp av Azure CLI.

### <a name="log-in-to-azure"></a>Logga in på Azure

Om du inte använder Cloud Shell måste du använda Azure CLI lokalt för att komma åt ditt konto. Använd `az login` kommandot från Bash-prompten för att starta den webbläsarbaserade inloggningsupplevelsen. Om du har åtkomst till mer än en `az account set --subscription` Azure-prenumeration anger du standard med följt av prenumerations-ID.

### <a name="set-environment-variables"></a>Ange miljövariabler

Skapa sedan några miljövariabler för namn och plats för de resurser som du ska skapa. Använd följande kommandon och `<value>` ersätt platshållarna med de värden du väljer. Värden bör överensstämma med [namngivningsregler och begränsningar för Azure-resurser](/azure/architecture/best-practices/resource-naming). Använd `LOCATION` ett av de värden som produceras `az functionapp list-consumption-locations` av kommandot för variabeln.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

Resten av den här självstudien använder dessa variabler. Tänk på att dessa variabler endast kvarstår under den aktuella Azure CLI- eller Cloud Shell-sessionen. Du måste köra dessa kommandon igen om du använder ett annat lokalt terminalfönster eller om cloud shell-sessionen tar time out.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure använder resursgrupper för att samla in alla relaterade resurser i ditt konto. På så sätt kan du visa dem som en enhet och ta bort dem med ett enda kommando när du är klar med dem.

Använd följande kommando för att skapa en resursgrupp:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Skapa sedan en namnområde för Azure Event Hubs, händelsenav och auktoriseringsregel med hjälp av följande kommandon:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

Namnområdet Event Hubs innehåller den faktiska händelsehubben och dess auktoriseringsregel. Auktoriseringsregeln gör det möjligt för dina funktioner att skicka meddelanden till navet och lyssna efter motsvarande händelser. En funktion skickar meddelanden som representerar telemetridata. En annan funktion lyssnar efter händelser, analyserar händelsedata och lagrar resultaten i Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Skapa ett Azure Cosmos DB-konto

Skapa sedan ett Azure Cosmos DB-konto, databas och samling med följande kommandon:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

`partition-key-path` Värdet partitionerar dina data `temperatureStatus` baserat på värdet för varje objekt. Partitionsnyckeln gör det möjligt för Cosmos DB att öka prestanda genom att dela upp dina data i olika delmängder som den kan komma åt oberoende av dem.

### <a name="create-a-storage-account-and-function-app"></a>Skapa en lagringskonto- och funktionsapp

Skapa sedan ett Azure Storage-konto, som krävs av Azure Functions och skapa sedan funktionsappen. Använd följande kommandon:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

När `az functionapp create` kommandot skapar din funktionsapp skapas också en Application Insights-resurs med samma namn. Funktionsappen konfigureras automatiskt med `APPINSIGHTS_INSTRUMENTATIONKEY` en inställning med namnet som ansluter den till Application Insights. Du kan visa apptelemetri när du distribuerar dina funktioner till Azure, som beskrivs senare i den här självstudien.

## <a name="configure-your-function-app"></a>Konfigurera din funktionsapp

Din funktionsapp måste komma åt de andra resurserna för att fungera korrekt. I följande avsnitt visas hur du konfigurerar din funktionsapp så att den kan köras på den lokala datorn.

### <a name="retrieve-resource-connection-strings"></a>Hämta resursanslutningssträngar

Använd följande kommandon för att hämta anslutningssträngarna lagring, händelsehubb och Cosmos DB och spara dem i miljövariabler:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Dessa variabler är inställda på värden som hämtas från Azure CLI-kommandon. Varje kommando använder en JMESPath-fråga för att extrahera anslutningssträngen från den returnerade JSON-nyttolasten. Anslutningssträngarna visas också `echo` med så att du kan bekräfta att de har hämtats.

### <a name="update-your-function-app-settings"></a>Uppdatera inställningarna för funktionsappar

Använd sedan följande kommando för att överföra anslutningssträngvärdena till appinställningar i ditt Azure Functions-konto:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Dina Azure-resurser har nu skapats och konfigurerats för att fungera korrekt tillsammans.

## <a name="create-and-test-your-functions"></a>Skapa och testa dina funktioner

Därefter ska du skapa ett projekt på din lokala dator, lägga till Java-kod och testa den. Du använder kommandon som fungerar med Azure Functions Plugin för Maven och Azure Functions Core Tools. Dina funktioner körs lokalt, men använder de molnbaserade resurser som du har skapat. När du har fått funktioner som fungerar lokalt kan du använda Maven för att distribuera dem till molnet och se dina data och analyser ackumuleras.

Om du använde Cloud Shell för att skapa dina resurser, då du inte kommer att vara ansluten till Azure lokalt. I så fall `az login` använder du kommandot för att starta den webbläsarbaserade inloggningsprocessen. Ange sedan standardprenumerationen med `az account set --subscription` följt av prenumerations-ID: t. Kör slutligen följande kommandon för att återskapa vissa miljövariabler på den lokala datorn. Ersätt `<value>` platshållarna med samma värden som du använde tidigare.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Skapa ett projekt för lokala funktioner

Använd följande Maven-kommando för att skapa ett funktionsprojekt och lägga till nödvändiga beroenden.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Det här kommandot genererar `telemetry-functions` flera filer i en mapp:

* En `pom.xml` fil för användning med Maven
* En `local.settings.json` fil som innehåller appinställningar för lokal testning
* En `host.json` fil som aktiverar Azure Functions Extension Bundle, som krävs för Cosmos DB-utdatabindning i dataanalysfunktionen
* En `Function.java` fil som innehåller en standardfunktionsimplementering
* Några testfiler som den här självstudien inte behöver

För att undvika kompileringsfel måste du ta bort testfilerna. Kör följande kommandon för att navigera till den nya projektmappen och ta bort testmappen:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Hämta inställningarna för funktionsappar för lokal användning

För lokala tester behöver ditt funktionsprojekt de anslutningssträngar som du har lagt till i funktionsappen i Azure tidigare i den här självstudien. Använd följande Azure Functions Core Tools-kommando, som hämtar alla funktionsappinställningar `local.settings.json` som lagras i molnet och lägger till dem i filen:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Lägg till Java-kod

Öppna `Function.java` sedan filen och ersätt innehållet med följande kod.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Som du kan se innehåller den `generateSensorData` här `processSensorData`filen två funktioner och . Funktionen `generateSensorData` simulerar en sensor som skickar temperatur- och tryckavläsningar till händelsenavet. En timerutlösare kör funktionen var 10:e sekund och en händelsehubbutdatabindning skickar returvärdet till händelsehubben.

När händelsehubben tar emot meddelandet genererar den en händelse. Funktionen `processSensorData` körs när den tar emot händelsen. Den bearbetar sedan händelsedata och använder en Azure Cosmos DB-utdatabindning för att skicka resultaten till Azure Cosmos DB.

De data som används av dessa `TelemetryItem`funktioner lagras med hjälp av en klass som kallas , som du måste implementera. Skapa en ny `TelemetryItem.java` fil som `Function.java` anropas på samma plats som och lägg till följande kod:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Lokal körning

Du kan nu skapa och köra funktionerna lokalt och se data visas i din Azure Cosmos DB.

Använd följande Maven-kommandon för att skapa och köra funktionerna:

```bash
mvn clean package
mvn azure-functions:run
```

När vissa bygg- och startmeddelanden visas utdata som liknar följande exempel för varje gång funktionerna körs:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Du kan sedan gå till [Azure-portalen](https://portal.azure.com) och navigera till ditt Azure Cosmos DB-konto. Välj **Data Explorer**, expandera **TelemetryInfo**och välj sedan **Objekt** som ska visas när de anländer.

![Utforskare för Fmo DB-data](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Distribuera till Azure och visa apptelemetri

Slutligen kan du distribuera din app till Azure och kontrollera att den fortsätter att fungera på samma sätt som den gjorde lokalt.

Distribuera projektet till Azure med följande kommando:

```bash
mvn azure-functions:deploy
```

Dina funktioner körs nu i Azure och fortsätter att ackumulera data i din Azure Cosmos DB. Du kan visa din distribuerade funktionsapp i Azure-portalen och visa apptelemetri via den anslutna Application Insights-resursen, som visas i följande skärmbilder:

**Live Metrics Stream:**

![Live-mätström för programinsikter](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Prestanda:**

![Prestandablad för programinsikter](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med de Azure-resurser du har skapat i den här självstudien kan du ta bort dem med hjälp av följande kommando:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar en Azure-funktion som hanterar händelser i händelsehubben och uppdaterar en Cosmos DB. Mer information finns i [utvecklarhandboken för Azure Functions Java](/azure/azure-functions/functions-reference-java). Information om de anteckningar som används finns i referensen [com.microsoft.azure.functions.annoteation.](/java/api/com.microsoft.azure.functions.annotation)

Den här självstudien använde miljövariabler och programinställningar för att lagra hemligheter som anslutningssträngar. Information om hur du lagrar dessa hemligheter i Azure Key Vault finns i [Använda nyckelvalvsreferenser för App-tjänst och Azure-funktioner](/azure/app-service/app-service-key-vault-references).

Lär dig sedan hur du använder Azure Pipelines CI/CD för automatiserad distribution:

> [!div class="nextstepaction"]
> [Skapa och distribuera Java till Azure-funktioner](/azure/devops/pipelines/ecosystems/java-function)
