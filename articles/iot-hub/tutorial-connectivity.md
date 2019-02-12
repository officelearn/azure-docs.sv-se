---
title: Kontrollera enhetens anslutning till Azure IoT-hubb
description: Använd IoT-hubb-verktyg för att felsöka anslutningsproblem för enheten till din IoT-hubb under utvecklingen.
services: iot-hub
author: dominicbetts
manager: timlt
ms.author: dobett
ms.custom: mvc
ms.date: 05/29/2018
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: bb9bcfcc5f78ee82f187d331055e8f2fd2ed9e64
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745817"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Självstudier: Använda en simulerad enhet för att testa anslutningen med din IoT-hubb

I den här kursen använder du Azure IoT Hub-portalverktyg och Azure CLI-kommandon för att testa anslutningen för enheten. Den här kursen använder också en enkel enhetssimulator som du kör på din stationära dator.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Kontrollera din enhetsautentisering
> * Kontrollera anslutningen från enhet till moln
> * Kontrollera anslutningen moln till enhet
> * Kontrollera dubbelsynkronisering

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

CLI-skript som du kör i den här självstudiekursen använder [Microsoft Azure IoT-tillägg för Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md). Kör följande CLI-kommando om du vill installera tillägget:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Enhetssimulatorprogrammet som körs i den här självstudiekursen har skrivits med Node.js. Du behöver ha Node.js v4.x.x eller senare installerat på utvecklingsdatorn.

Du kan ladda ned Node.js för flera plattformar från [nodejs.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

Ladda ned exempelprojektet för Node.js från https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har skapat en IoT Hub på kostnadsfri eller standardnivå i en föregående snabbstart eller kurs kan du hoppa över detta steg.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Kontrollera din enhetsautentisering

En enhet måste autentiseras mot din hubb innan den kan utbyta data med hubben. Du kan använda verktyget **IoT-enheter** -verktyget i området **enhetshantering** i portalen för att hantera dina enheter och kontrollera de autentiseringsnycklar som de använder. I denna del av kursen lägger du till en ny testenhet, tar emot dess nyckel och kontrollerar att en testenhet kan anslutas till hubben. Senare kan du återställa autentiseringsnyckeln om du vill se vad som händer när en enhet försöker använda en inaktuell nyckel. Det här avsnittet av kursen använder Azure-portalen för att skapa, hantera och övervaka en enhet och exempelenhetssimulatorn Node.js.

Logga in på portalen och gå till IoT-hubben. Gå sedan till verktyget **IoT-enheter**:

![Verktyget IoT-enheter](media/tutorial-connectivity/iot-devices-tool.png)

Om du vill registrera en ny enhet klickar du på **+ Lägg till**, ange **MyTestDevice** som **enhets-ID** och klicka på **Spara**:

![Lägg till en ny enhet](media/tutorial-connectivity/add-device.png)

För att hämta anslutningssträngen för **MyTestDevice**, klicka på den i listan över enheter och kopiera sedan värdet **sträng-primär anslutningsnyckel**. Anslutningssträngen innehåller den *delade åtkomstnyckeln* för enheten.

![Hämta enhetens anslutningssträng](media/tutorial-connectivity/copy-connection-string.png)

För att simulera när **MyTestDevice** skickar telemetri till din IoT-hubb kan du köra den Node.js simulerade enheten programmet du hämtade tidigare.

Navigera till rotmappen för Node.js-exempelprojektet som du hämtade i ett terminalfönster på din utvecklingsdator. Gå sedan till mappen **iot-hub\Tutorials\ConnectivityTests**.

Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret. Använd enhetsanslutningssträngen som du antecknade när du lade till enheten i portalen.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Terminalfönstret visar information när det försöker ansluta till din hubb:

![Anslut en simulerad enhet](media/tutorial-connectivity/sim-1-connected.png)

Du har nu autentiserats från en enhet med en enhetsnyckel som har genererats av din IoT-hubb.

### <a name="reset-keys"></a>Återställning av nycklar

I det här avsnittet får du återställa enhetsnyckeln och se felet när den simulerade enheten försöker ansluta.

För att återställa nyckeln för primära enheten för **MyTestDevice**, kör följande kommandon:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

Kör det simulerade enhetsprogrammet igen i terminalfönstret på utvecklingsdatorn:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Den här gången ser du ett autentiseringsfel när programmet försöker ansluta:

![Anslutningsfel](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Skapa token för signaturer för delad åtkomst (SAS)

Om enheten använder en av SDK:erna för IoT Hub-enheten, genererar SDK-bibliotekskoden en SAS-token som används för att autentisera mot hubben. En SAS-token skapas från namnet på din hubb, namnet på din enhet och enhetsnyckeln.

I vissa scenarier, till exempel en molnprotokollgateway eller en del av ett anpassat autentiseringsschema, kan du behöva skapa en SAS-token själv. När du felsöker problem med din SAS-kod är det användbart att kunna skapa en fungerande SAS-token som kan användas vid test.

> [!NOTE]
> SimulatedDevice-2.js-exemplet innehåller exempel på hur du genererar en SAS-token både med och utan SDK.

Om du vill skapa en fungerande SAS-token med hjälp av CLI kör du följande kommando:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Anteckna den fullständiga texten i din genererade SAS-token. En SAS-token ser ut så här: `'SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307'`

Navigera till rotmappen för Node.js-exempelprojektet som du hämtade i ett terminalfönster på din utvecklingsdator. Gå sedan till mappen **iot hub\Tutorials\ConnectivityTests\simulated-device**.

Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

Terminalfönstret visar information när det försöker ansluta till din hubb med hjälp av en SAS-token:

![Anslut en simulerad enhet med token](media/tutorial-connectivity/sim-2-connected.png)

Du har nu autentiserats från en enhet med en test-SAS-token som har genererats av ett CLI-kommando. Filen **SimulatedDevice 2.js** innehåller exempelkod som visar hur du genererar en SAS-token i kod.

### <a name="protocols"></a>Protokoll

En enhet kan använda något av följande protokoll för att ansluta till din IoT-hubb:

| Protokoll | Utgående port |
| --- | --- |
| MQTT |8883 |
| MQTT över WebSockets |443 |
| AMQP |5671 |
| AMQP över WebSockets |443 |
| HTTPS |443 |

Om den utgående porten blockeras av en brandvägg kan enheten inte anslutas:

![Blockerad port](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Kontrollera anslutningen från enhet till moln

Vanligtvis när en enhet har anslutit försöker den skicka telemetri till din IoT-hubb. Det här avsnittet visar hur du kan verifiera att telemetri som skickas av enheten når din hubb.

Först måste du hämta den aktuella anslutningssträngen för den simulerade enheten med följande kommando:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Om du vill köra en simulerad enhet som skickar meddelanden, navigera till mappen **iot hub\Tutorials\ConnectivityTests\simulated-device** i koden som du hämtat.

Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

Terminalfönstret visar information när det skickar telemetri till din hubb:

![Simulerade enhet som skickar meddelanden](media/tutorial-connectivity/sim-3-sending.png)

Du kan använda **mått** i portalen för att kontrollera att telemetrimeddelanden kommer fram till din IoT-hubb:

![Navigera till mått för IoT-hubb](media/tutorial-connectivity/metrics-portal.png)

Välj IoT-hubb i listrutan **Resurs**, välj **Skickade telemetrimeddelanden** som mått och ange **Senaste timmen** som tidsintervall. Diagrammet visar det sammanlagda antalet meddelanden som har skickats av den simulerade enheten:

![Visa mått för IoT-hubb](media/tutorial-connectivity/metrics-active.png)

Det tar några minuter för mätvärdena att bli tillgängliga efter att du har startat den simulerade enheten.

## <a name="check-cloud-to-device-connectivity"></a>Kontrollera anslutningen moln till enhet

Hur du kan testa ett direkt metodanrop till en enhet för att kontrollera anslutningen från moln till enhet. Du kör en simulerad enhet på din utvecklingsdator för att lyssna efter direkta metodanrop från din hubb.

Kör det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Använd ett CLI-kommando för att anropa en direkt metod på enheten:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Den simulerade enheten skriver ut ett meddelande till konsolen när den tar emot ett direkt metodanrop:

![Den simulerade enheten tar emot direkt metodanrop](media/tutorial-connectivity/receive-method-call.png)

När den simulerade enheten tar emot ett direkt metodanrop skickar den en bekräftelse till hubben:

![Ta emot bekräftelse för direktmetod](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Kontrollera dubbelsynkronisering

Enheter använder direktsynkronisering för att synkronisera tillstånd mellan enheten och hubben. I det här avsnittet kan du använda CLI-kommandon för att skicka _önskade egenskaper_ till en enhet och läsa _rapporterade egenskaper_ som skickas av enheten.

Den simulerade enheten som du använder i det här avsnittet skickar rapporterade egenskaper till hubben när den startas och skriver ut önskade egenskaper i konsolen när den tar emot dem.

Kör det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Kontrollera att hubben tog emot rapporterade egenskaper från enheten genom att använda CLI-kommandot:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Du kan se egenskapen **devicelaststarted** i området med rapporterade egenskaper. Den här egenskapen visar datum och tid då den simulerade enheten senast startades.

![Visa rapporterade egenskap](media/tutorial-connectivity/reported-properties.png)

Kontrollera att hubben kan skicka önskade egenskapsvärden till enheten genom att använda CLI-kommandot:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Den simulerade enheten skriver ut ett meddelande när den tar emot en önskad egenskapsuppdatering från hubben:

![Ta emot önskade egenskaper](media/tutorial-connectivity/desired-properties.png)

Förutom att ta emot önskade egenskapsändringar när de har utförts, söker simulerade enheten automatiskt efter egenskaper när den startas.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver IoT-hubben längre kan du ta bort den och resursgruppen i portalen. Det gör du genom att markera **tutorials-iot-hub-rg**-resursgruppen som innehåller din IoT-hubb och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du fått lära dig att kontrollera dina enhetsnycklar, kontrollera anslutningen från enhet till moln, kontrollera anslutningen från moln till enhet och kontrollera dubbelsynkronisering. Gå till artikeln om övervakning av IoT Hub för att lära dig mer om hur du övervakar din IoT Hub.

> [!div class="nextstepaction"]
> [Övervaka med diagnostik](iot-hub-monitor-resource-health.md)
