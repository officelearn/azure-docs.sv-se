---
title: Använda Protocol Buffers med enhetssimulering – Azure | Microsoft Docs
description: Du lär dig hur du använder Protocol Buffers för att serialisera telemetri som skickas från lösningsaccelerator Enhetssimulering i den här guiden.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 74bb2d181533f802e1428eaa8a855f60fb855193
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258843"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialisera telemetri som använder Protocol Buffers

Protocol Buffers (Protobuf) är en binärt serialiseringsformat för strukturerade data. Protobuf är utformad för att betona enkelhet och prestanda med ett mål för mindre och snabbare än XML.

Enhetssimulering stöder den **proto3** version av protokollet buffrar språk.

Eftersom Protobuf kräver kompilerad kod att serialisera data, måste du skapa en anpassad version av Enhetssimulering.

Stegen i den här How-to-till-guiden visar dig hur du:

1. Förbered en utvecklingsmiljö
1. Ange formatet Protobuf i en enhetsmodell
1. Definiera Protobuf-format
1. Generera Protobuf klasser
1. Testa lokalt

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här guiden behöver du:

* Visual Studio Code. Du kan ladda ned [Visual Studio Code för Mac, Linux och Windows](https://code.visualstudio.com/download).
* .NET Core. Du kan ladda ned [.NET Core för Mac, Linux och Windows](https://www.microsoft.com/net/download).
* Postman. Du kan ladda ned [Postman för Mac, windows eller Linux](https://www.getpostman.com/apps).
* En [IoT hub som är distribuerad på Azure-prenumerationen](../iot-hub/iot-hub-create-through-portal.md). Du behöver anslutningssträngen för IoT-hubben att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.
* En [Cosmos DB-databas som distribuerats till din Azure-prenumeration](../cosmos-db/create-sql-api-dotnet.md#create-account) som använder SQL-API och som är konfigurerad för [stark konsekvens](../cosmos-db/manage-account.md). Du behöver anslutningssträngen för Cosmos DB-databasen för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.
* En [Azure storage-konto som distribueras till din Azure-prenumeration](../storage/common/storage-quickstart-create-account.md). Du behöver anslutningssträngen för storage-konto för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Utför följande uppgifter för att förbereda utvecklingsmiljön:

* Hämta källan för enheten simulering mikrotjänst.
* Hämta källan för storage nätverkskort mikrotjänst.
* Köra storage nätverkskort mikrotjänster lokalt.

Anvisningarna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av de sökvägar och kommandon för att de passar din miljö.

### <a name="download-the-microservices"></a>Ladda ned mikrotjänster

Hämta och packa upp den [Remote Monitoring Mikrotjänster](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn. Den här databasen innehåller storage nätverkskort mikrotjänst som du behöver för den här anvisningen.

Hämta och packa upp den [enheten simulering mikrotjänst](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn.

### <a name="run-the-storage-adapter-microservice"></a>Kör storage nätverkskort mikrotjänst

I Visual Studio Code, öppna den **remote-monitoring-services-dotnet-master\storage-adapter** mapp. Klicka på någon **återställa** knappar för att åtgärda Olösta beroenden.

Öppna den **.vscode/launch.json** fil och tilldela din Cosmos DB-anslutningssträngen till den **datorer\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** miljö variabeln.

> [!NOTE]
> När du kör mikrotjänst lokalt på din dator, kräver fortfarande en Cosmos DB-instans i Azure för att fungera korrekt.

Om du vill köra storage nätverkskort mikrotjänster lokalt, klickar du på **felsöka \> Starta felsökning**.

Den **Terminal** fönstret i Visual Studio Code visas utdata från körs mikrotjänst, inklusive en URL för web service-hälsokontroll: <http://127.0.0.1:9022/v1/status>. När du navigerar till den här adressen statusen ska vara ”OK: Alive och väl ”.

Lämna storage adapter-mikrotjänster som körs i den här instansen av Visual Studio Code när du slutför följande steg.

## <a name="define-your-device-model"></a>Definiera din enhetsmodell

Öppna den **enhet-simulering-dotnet-master** mapp som du laddade ned från GitHub i en ny instans av Visual Studio Code. Klicka på någon **återställa** knappar för att åtgärda eventuella olöst beroenden.

I den här How-to-till-guiden, kan du skapa en ny enhetsmodell för en tillgång spåraren:

1. Skapa en ny enhet modellfil med namnet **assettracker 01.json** i den **Services\data\devicemodels** mapp.

1. Definiera enheten funktionerna i den nuvarande enhetsmodellen **assettracker 01.json** fil. Avsnittet telemetri i en Protobuf enhetsmodell måste:

   * Inkludera namnet på klassen Protobuf du generera för din enhet. Följande avsnitt visar hur du skapar den här klassen.
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

### <a name="create-device-behaviors-script"></a>Skapa beteenden-enhetsskript

Skriva skriptet beteende som definierar hur enheten fungerar. Mer information finns i [skapar en avancerad simulerad enhet](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definiera Protobuf-format

När du har en enhet och har fastställt meddelandeformat, kan du skapa en **proto** fil. I den **proto** registrerat kan du lägga till:

* En `csharp_namespace` som matchar den **ClassName** -egenskapen i din enhetsmodell.
* Ett meddelande för varje datastruktur för att serialisera.
* Ett namn och typ för varje fält i meddelandet.

1. Skapa en ny fil med namnet **assettracker.proto** i den **Services\Models\Protobuf\proto** mapp.

1. Definiera syntax, namnområde och Meddelandeschema i den **proto** så här:

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

Den `=1`, `=2` markörer på varje element ange en unik tagg som används i binär kodning. Nummer 1 – 15 kräver en mindre byte att koda än högre nummer.

## <a name="generate-the-protobuf-class"></a>Generera Protobuf-klass

När du har en **proto** filen, nästa steg är att generera de klasser som behövs för att läsa och skriva meddelanden. Du behöver för att slutföra det här steget, den **Protoc** Protobuf-kompilatorn.

1. [Ladda ned Protobuf-kompilatorn från GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Kör kompilatorn att ange källkatalogen, målmappen och namnet på din **proto** fil. Exempel:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Det här kommandot genererar ett **Assettracker.cs** fil i den **Services\Models\Protobuf** mapp.

## <a name="test-protobuf-locally"></a>Testa Protobuf lokalt

I det här avsnittet ska testa du tillgången spårningsverktyget för enheten som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Kör enheten simulering mikrotjänst

Öppna den **.vscode/launch.json** fil och tilldela din:

* IoT Hub-anslutningssträngen till den **datorer\_IOTHUB\_CONNSTRING** miljövariabeln.
* Anslutningssträng för lagringskonto till den **datorer\_AZURE\_STORAGE\_konto** miljövariabeln.
* Cosmos DB-anslutningssträngen till den **datorer\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** miljövariabeln.

Öppna den **WebService/Properties/launchSettings.json** fil och tilldela din:

* IoT Hub-anslutningssträngen till den **datorer\_IOTHUB\_CONNSTRING** miljövariabeln.
* Anslutningssträng för lagringskonto till den **datorer\_AZURE\_STORAGE\_konto** miljövariabeln.
* Cosmos DB-anslutningssträngen till den **datorer\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** miljövariabeln.

Öppna den **WebService\appsettings.ini** -filen och ändra följande inställningar:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurera lösningen för att inkludera de nya enheten modellen filerna

Som standard kopieras dina nya enhetsmodell JSON och JS-filer inte till den inbyggda lösningen. Du måste uttryckligen skrivs.

Lägg till en post som den **services\services.csproj** fil för varje fil som du vill inkludera. Exempel:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Om du vill köra mikrotjänst lokalt, klickar du på **felsöka \> Starta felsökning**.

Den **Terminal** utdata från körs mikrotjänst visas i fönstret i Visual Studio Code.

Lämna enheten simulering mikrotjänster som körs i den här instansen av Visual Studio Code när du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en Övervakare för enhetshändelser

I det här avsnittet använder du Azure CLI för att ställa in en Händelseövervakare visar den telemetri som skickas från enheter som är anslutna till din IoT hub.

Skriptet nedan förutsätter att namnet på din IoT-hubb **enhet-simulering-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lämna Händelseövervakare som körs medan du testa de simulerade enheterna.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Skapa en simulering med tillgångstyp spårningsverktyget för enhet

I det här avsnittet ska använda du verktyget Postman för att begära enheten simulering mikrotjänst att köra en simulering med tillgångstyp spårningsverktyget för enheten. Postman är ett verktyg som låter dig skicka REST-begäranden till en webbtjänst.

Konfigurera Postman:

1. Öppna Postman på den lokala datorn.

1. Klicka på **filen \> Import**. Klicka sedan på **Välj filer**.

1. Välj **Enhetssimulering för Azure IoT-lösning accelerator.postman\_samling** och **Enhetssimulering för Azure IoT-lösning accelerator.postman\_miljö** och Klicka på **öppna**.

1. Expandera den **Azure IoT-Enhetssimulering lösningsaccelerator** att se begäranden som du kan skicka.

1. Klicka på **nr miljö** och välj **Azure IoT-Enhetssimulering lösningsaccelerator**.

Nu har du en samling och läses in i din Postman-arbetsyta som du kan använda för att interagera med enheten simulering mikrotjänst-miljö.

Konfigurera och köra simuleringen:

1. Välj i Postman-samling **tillgången spåraren simulera** och klicka på **skicka**. Den här begäran skapar fyra instanser av den simulerade tillgång spåraren enhetstypen.

1. Händelsen övervaka utdata i Azure CLI-fönstret visas telemetri från simulerade enheter.

Om du vill stoppa simuleringen, Välj den **stoppa simulering** begäran i Postman och klicka på **skicka**.

### <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa två som körs lokalt mikrotjänster i sina Visual Studio Code-instanser (**felsöka \> stoppa felsökning**).

Om du behöver inte längre IoT Hub och Cosmos DB-instanser kan du ta bort dem från Azure-prenumerationen för att undvika onödiga kostnader.

## <a name="iot-hub-support"></a>IoT Hub Support

Många IoT Hub-funktioner stöder inte internt Protobuf eller andra binärt format. Du kan till exempel vidarebefordra baserat på meddelandenyttolast eftersom IoT Hub inte går att bearbeta meddelandet nyttolasten. Du kan dock vidarebefordra baserat på meddelandehuvudena.

## <a name="next-steps"></a>Nästa steg

Nu du har lärt dig hur du anpassar Enhetssimulering om du vill använda Protobuf för att skicka telemetri om nästa steg är att lära dig nu att [distribuera en anpassad avbildning till molnet](iot-accelerators-device-simulation-deploy-image.md).
