---
title: Använda protokollbuffertar med enhetssimulering – Azure| Microsoft-dokument
description: I den här programguiden får du lära dig hur du använder protokollbuffertar för att serialisera telemetri som skickas från device simulation-lösningsacceleratorn.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom:
- mvc
- amqp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c49745b30d2c4acc115a72af095f3e941dc4d509
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683992"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialisera telemetri med hjälp av protokollbuffertar

Protokollbuffertar (Protobuf) är ett binärt serialiseringsformat för strukturerade data. Protobuf är utformat för att betona enkelhet och prestanda med målet att vara mindre och snabbare än XML.

Enhetssimulering stöder **proto3-versionen** av protokollets buffertarspråk.

Eftersom Protobuf kräver kompilerad kod för att serialisera data måste du skapa en anpassad version av Enhetssimulering.

Stegen i den här guiden visar hur du:

1. Förbereda en utvecklingsmiljö
1. Ange användning av Protobuf-formatet i en enhetsmodell
1. Definiera ditt Protobuf-format
1. Generera Protobuf-klasser
1. Testa lokalt

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här programguiden behöver du:

* Visual Studio Code. Du kan ladda ner [Visual Studio Code för Mac, Linux och Windows](https://code.visualstudio.com/download).
* .NET-kärna. Du kan hämta [.NET Core för Mac, Linux och Windows](https://www.microsoft.com/net/download).
* Brevbäraren. Du kan ladda ner [Postman för Mac, Windows eller Linux](https://www.getpostman.com/apps).
* En [IoT-hubb som distribueras till din Azure-prenumeration](../iot-hub/iot-hub-create-through-portal.md). Du behöver IoT-hubbens anslutningssträng för att kunna slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.
* En [Cosmos DB-databas som distribueras till din Azure-prenumeration](../cosmos-db/create-sql-api-dotnet.md#create-account) som använder SQL API och som är konfigurerad för [stark konsekvens](../cosmos-db/manage-account.md). Du behöver Cosmos DB-databasens anslutningssträng för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.
* Ett [Azure-lagringskonto som distribueras till din Azure-prenumeration](../storage/common/storage-account-create.md). Du behöver anslutningssträngen för lagringskontot för att kunna slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Slutför följande uppgifter för att förbereda utvecklingsmiljön:

* Hämta källan för mikrotjänsten för enhetssimulering.
* Hämta källan för lagringskortets mikrotjänst.
* Kör mikrotjänsten för lagringsadapter lokalt.

Instruktionerna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av filsökvägarna och kommandona så att de passar din miljö.

### <a name="download-the-microservices"></a>Ladda ner mikrotjänsterna

Ladda ned och packa upp [microservicesna för fjärrövervakning](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) från GitHub till en lämplig plats på din lokala dator. Den här databasen innehåller den mikrotjänst för lagringskortet som du behöver för den här indatatjänsten.

Ladda ned och packa upp [enhetssimuleringsmikrotjänsten](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) från GitHub till en lämplig plats på din lokala dator.

### <a name="run-the-storage-adapter-microservice"></a>Kör mikrotjänsten för lagringsadapter

Öppna mappen **fjärrövervakning-tjänster-dotnet-master\storage-adapter** i Visual Studio-kod. Klicka på **valfria återställningsknappar** om du vill åtgärda olösta beroenden.

Öppna **filen .vscode/launch.json** och tilldela cosmos DB-anslutningssträngen till miljövariabeln **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING.**

> [!NOTE]
> När du kör mikrotjänsten lokalt på datorn kräver den fortfarande en Cosmos DB-instans i Azure för att fungera korrekt.

Om du vill köra mikrotjänsten för lagringskortet lokalt klickar du på ** \> Felsökning av startfelsökning**.

**Terminalfönstret** i Visual Studio-koden visar utdata från den mikrotjänst som <http://127.0.0.1:9022/v1/status>körs, inklusive en URL för hälsokontrollen av webbtjänsten: . När du navigerar till den här adressen ska statusen vara "OK: Alive and well".

Låt lagringskortets mikrotjänst köras i den här instansen av Visual Studio-kod medan du slutför följande steg.

## <a name="define-your-device-model"></a>Definiera din enhetsmodell

Öppna **mappen device-simulation-dotnet-master** som du hämtade från GitHub i en ny instans av Visual Studio Code. Klicka på **valfria återställningsknappar** om du vill åtgärda eventuella olösta beroenden.

I den här guiden skapar du en ny enhetsmodell för en tillgångsspårare:

1. Skapa en ny enhetsmodellfil som heter **assettracker-01.json** i mappen **Tjänster\data\enhetsmodeller.**

1. Definiera enhetsfunktionen i filen device model **assettracker-01.json.** Telemetridelen av en Protobuf-enhetsmodell måste

   * Inkludera namnet på den Protobuf-klass som du genererar för din enhet. I följande avsnitt visas hur du skapar den här klassen.
   * Ange Protobuf som meddelandeformat.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Skapa skript för enhetsbeteenden

Skriv beteendeskriptet som definierar hur enheten beter sig. Mer information finns i [Skapa en avancerad simulerad enhet](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definiera ditt Protobuf-format

När du har en enhetsmodell och har bestämt ditt meddelandeformat kan du skapa en **protofil.** I **protofilen** lägger du till:

* A `csharp_namespace` som matchar egenskapen **ClassName** i enhetsmodellen.
* Ett meddelande för varje datastruktur att serialisera.
* Ett namn och en typ för varje fält i meddelandet.

1. Skapa en ny fil som heter **assettracker.proto** i mappen **Tjänster\Modeller\Protobuf\proto.**

1. Definiera syntax-, namnområdes- och meddelandeschemat i **protofilen** enligt följande:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

`=1`De `=2` , markörer på varje element anger en unik tagg som fältet använder i den binära kodningen. Nummer 1-15 kräver en mindre byte för att koda än högre tal.

## <a name="generate-the-protobuf-class"></a>Generera protobuf-klassen

När du har en **protofil** är nästa steg att generera de klasser som behövs för att läsa och skriva meddelanden. För att slutföra det här steget behöver du **Protoc** Protobuf-kompilatorn.

1. [Ladda ner Protobuf-kompilatorn från GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Kör kompilatorn och ange källkatalogen, målkatalogen och namnet på **protofilen.** Ett exempel:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Det här kommandot genererar en **Assettracker.cs-fil** i mappen **Tjänster\Modeller\Protobuf.**

## <a name="test-protobuf-locally"></a>Testa Protobuf lokalt

I det här avsnittet testar du den tillgångsspårningsenhet som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Kör mikrotjänsten för enhetssimulering

Öppna **filen vscode/launch.json** och tilldela:

* IoT Hub-anslutningssträng till **variabeln PCS\_IOTHUB CONNSTRING.IoT\_** Hub connection string to the PCS IOTHUB CONNSTRING environment variable.
* Anslutningssträng för lagringskonto till miljövariabeln **PCS\_AZURE\_STORAGE\_ACCOUNT.**
* Cosmos DB-anslutningssträng till **variabeln\_PCS\_\_STORAGEADAPTER DOCUMENTDB CONNSTRING.**

Öppna **filen WebService/Properties/launchSettings.json** och tilldela:

* IoT Hub-anslutningssträng till **variabeln PCS\_IOTHUB CONNSTRING.IoT\_** Hub connection string to the PCS IOTHUB CONNSTRING environment variable.
* Anslutningssträng för lagringskonto till miljövariabeln **PCS\_AZURE\_STORAGE\_ACCOUNT.**
* Cosmos DB-anslutningssträng till **variabeln\_PCS\_\_STORAGEADAPTER DOCUMENTDB CONNSTRING.**

Öppna **filen WebService\appsettings.ini** och ändra inställningarna på följande sätt:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurera lösningen så att den innehåller dina nya enhetsmodellfiler

Som standard kopieras inte den nya enhetsmodellen JSON- och JS-filer till den inbyggda lösningen. Du måste uttryckligen inkludera dem.

Lägg till en post i **filen services\services.csproj** för varje fil som du vill ska inkludera. Ett exempel:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Om du vill köra mikrotjänsten lokalt klickar du på ** \> Felsökning Avsöka startsökning**.

**Terminalfönstret** i Visual Studio-koden visar utdata från den mikrotjänst som körs.

Låt mikrotjänsten för enhetssimulering köras i den här instansen av Visual Studio-kod medan du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en bildskärm för enhetshändelser

I det här avsnittet använder du Azure CLI för att konfigurera en händelseövervakare för att visa telemetrin som skickas från de enheter som är anslutna till din IoT-hubb.

Följande skript förutsätter att namnet på IoT-hubben är **enhetssimuleringstest**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Låt händelseövervakaren vara igång medan du testar de simulerade enheterna.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Skapa en simulering med enhetstypen asset tracker

I det här avsnittet använder du Postman-verktyget för att begära att mikrotjänsten för enhetssimulering ska köra en simulering med hjälp av enhetstypen asset tracker. Brevbäraren är ett verktyg som låter dig skicka REST-begäranden till en webbtjänst.

Så här ställer du in Postman:

1. Öppna Brevbäraren på din lokala dator.

1. Klicka på **Filimport \> **. Klicka sedan på **Välj filer**.

1. Välj **Azure IoT Device Simulation\_solution accelerator.postman collection** och **Azure\_IoT Device Simulation solution accelerator.postman-miljö** och klicka på **Öppna**.

1. Expandera **Azure IoT Device Simulation-lösningsacceleratorn** för att visa de begäranden du kan skicka.

1. Klicka på **Ingen miljö** och välj **Azure IoT Device Simulation solution accelerator**.

Du har nu en samling och miljö inläst i postmanarbetsytan som du kan använda för att interagera med mikrotjänsten för enhetssimulering.

Så här konfigurerar och kör du simuleringen:

1. I Postman-samlingen väljer du **Skapa simulering av tillgångsmätare** och klickar på **Skicka**. Den här begäran skapar fyra instanser av den simulerade tillgångstrackerenhetstypen.

1. Händelseövervakarens utdata i Azure CLI-fönstret visar telemetrin från de simulerade enheterna.

Om du vill stoppa simuleringen väljer du **stop simulation-begäran** i Postman och klickar på **Skicka**.

### <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa de två lokalt körande mikrotjänsterna i deras Visual Studio-kodinstanser (**Debug \> Stop Debugging**).

Om du inte längre behöver IoT Hub- och Cosmos DB-instanserna tar du bort dem från din Azure-prenumeration för att undvika onödiga debiteringar.

## <a name="iot-hub-support"></a>IoT Hub Support

Många IoT Hub-funktioner stöder inte Protobuf eller andra binära format. Du kan till exempel inte cirkulera baserat på meddelandenyttolast eftersom IoT Hub inte kan bearbeta meddelandenyttolasten. Du kan dock cirkulera baserat på meddelanderubriker.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du anpassar Enhetssimulering för att använda Protobuf för att skicka telemetri, nästa steg är att lära sig nu att [distribuera en anpassad avbildning till molnet](iot-accelerators-device-simulation-deploy-image.md).
