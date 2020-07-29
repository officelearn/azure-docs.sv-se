---
title: Ansluta IoT Plug and Play Preview exempel python enhets kod till Azure IoT Hub | Microsoft Docs
description: Använd python för att skapa och köra IoT Plug and Play för hands versions exempel enhets kod som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352985"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>Snabb start: ansluta ett exempel på IoT Plug and Play Preview enhets program till IoT Hub (python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet för python och ingår i Azure IoT Hub Device SDK för python. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du python 3,7 på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/). Du kan kontrol lera din python-version med följande kommando:  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här snabb starten använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Det här paketet publiceras som en PIP för den offentliga förhands gransknings uppdateringen. Paket versionen ska vara senaste eller`2.1.4`

I din lokala python-miljö installerar du filen på följande sätt:

```cmd/sh
pip install azure-iot-device
```

Klona din python SDK IoT-lagringsplats och kolla ut **Master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

Mappen *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* innehåller exempel koden för IoT plug and Play-enheten. I den här snabb starten används filen *pnp_thermostat. py* . Den här exempel koden implementerar en IoT Plug and Play-kompatibel enhet och använder klient biblioteket för Azure IoT python-enheter.

Skapa en miljö variabel med namnet **IOTHUB_DEVICE_CONNECTION_STRING** för att lagra enhets anslutnings strängen som du antecknade tidigare.

Öppna filen **pnp_thermostat. py** i en text redigerare. Observera hur det:

1. Definierar en enskild enhets dubbla modell identifierare (DTMI) som unikt representerar [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). En DTMI måste vara känd för användaren och varierar beroende på användnings scenariot för enheten. För det aktuella exemplet representerar modellen en termostat som har telemetri, egenskaper och kommandon som är associerade med övervaknings temperaturen.

1. Har funktioner för att definiera implementeringar av kommando hanterare. Du skriver dessa hanterare för att definiera hur enheten svarar på kommando begär Anden.

1. Har en funktion för att definiera ett kommando svar. Du skapar funktioner för kommando svar för att skicka tillbaka ett svar till IoT Hub.

1. Definierar en lyssnar funktion för inmatade tangent bord så att du kan avsluta programmet.

1. Har en **huvud** funktion. **Huvud** funktionen:

    1. Använder enhets-SDK för att skapa en enhets klient och ansluta till IoT Hub.

    1. Egenskaper för uppdateringar. Modellen vi använder, **termostat**, definierar `targetTemperature` och `maxTempSinceLastReboot` som de två egenskaperna för vår termostat, så det är vad vi ska använda. Egenskaperna uppdateras med den `patch_twin_reported_properties` metod som definierats i `device_client` .

    1. Börjar lyssna efter kommando begär Anden med hjälp av funktionen **execute_command_listener** . Funktionen konfigurerar en lyssnare för att lyssna efter kommandon som kommer från tjänsten. När du ställer in den lyssnare som du anger `method_name` , `user_command_handler` , och `create_user_response_handler` . 
        - `user_command_handler`Funktionen definierar vad enheten ska göra när den får ett kommando. Om ditt larm till exempel inaktive ras, aktive ras resultatet av att ta emot det här kommandot. Tänk på detta som "påverkan" på det kommando som anropas.
        - `create_user_response_handler`Funktionen skapar ett svar som skickas till din IoT-hubb när ett kommando körs utan problem. Om ditt larm till exempel inaktive ras, svarar du genom att trycka på vilo läge, som är en feedback till tjänsten. Tänk på detta som det svar du ger tjänsten. Du kan visa det här svaret i portalen.

    1. Börjar skicka telemetri. **Pnp_send_telemetry** definieras i filen pnp_methods. py. Exempel koden använder en slinga för att anropa den här funktionen var åttonde sekund.

    1. Inaktiverar alla lyssnare och uppgifter och finns i slingan när du trycker på **q** eller **q**.

Nu när du har sett koden använder du följande kommando för att köra exemplet:

```cmd/sh
python pnp_thermostat.py
```

Följande utdata visas, som anger att enheten skickar telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar:

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning](quickstart-service-python.md)
