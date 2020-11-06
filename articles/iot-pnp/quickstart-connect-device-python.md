---
title: Snabb start – ansluta IoT Plug and Play exempel på python-enhets kod till Azure IoT Hub | Microsoft Docs
description: Snabb start – Använd python för att skapa och köra IoT Plug and Play enhets kod som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 38c6b835f0a3ddaaf4c049ca6ea3aeb484ad3280
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421574"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-python"></a>Snabb start: ansluta ett exempel på IoT Plug and Play enhets program till IoT Hub (python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet för python och ingår i Azure IoT Hub Device SDK för python. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten behöver du python 3,7 på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/). Du kan kontrol lera din python-version med följande kommando:  

```cmd/sh
python --version
```

I din lokala python-miljö installerar du paketet på följande sätt:

```cmd/sh
pip install azure-iot-device
```

Klona din python SDK IoT-lagringsplats och kolla ut **Master** :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

Mappen *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* innehåller exempel koden för IoT plug and Play-enheten. I den här snabb starten används filen *simple_thermostat. py* . Den här exempel koden implementerar en IoT Plug and Play-kompatibel enhet och använder klient biblioteket för Azure IoT python-enheter.

Öppna filen **simple_thermostat. py** i en text redigerare. Observera hur det:

1. Definierar en enskild enhets dubbla modell identifierare (DTMI) som unikt representerar [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). En DTMI måste vara känd för användaren och varierar beroende på användnings scenariot för enheten. För det aktuella exemplet representerar modellen en termostat som har telemetri, egenskaper och kommandon som är associerade med övervaknings temperaturen.

1. Har funktioner för att definiera implementeringar av kommando hanterare. Du skriver dessa hanterare för att definiera hur enheten svarar på kommando begär Anden.

1. Har en funktion för att definiera ett kommando svar. Du skapar funktioner för kommando svar för att skicka tillbaka ett svar till IoT Hub.

1. Definierar en lyssnar funktion för inmatade tangent bord så att du kan avsluta programmet.

1. Har en **huvud** funktion. **Huvud** funktionen:

    1. Använder enhets-SDK för att skapa en enhets klient och ansluta till IoT Hub.

    1. Egenskaper för uppdateringar. Modellen vi använder, **termostat** , definierar `targetTemperature` och `maxTempSinceLastReboot` som de två egenskaperna för vår termostat, så det är vad vi ska använda. Egenskaperna uppdateras med den `patch_twin_reported_properties` metod som definierats i `device_client` .

    1. Börjar lyssna efter kommando begär Anden med hjälp av funktionen **execute_command_listener** . Funktionen konfigurerar en lyssnare för att lyssna efter kommandon som kommer från tjänsten. När du ställer in den lyssnare som du anger `method_name` , `user_command_handler` , och `create_user_response_handler` .
        - `user_command_handler`Funktionen definierar vad enheten ska göra när den får ett kommando. Om ditt larm till exempel inaktive ras, aktive ras resultatet av att ta emot det här kommandot. Tänk på detta som "påverkan" på det kommando som anropas.
        - `create_user_response_handler`Funktionen skapar ett svar som skickas till din IoT-hubb när ett kommando körs utan problem. Om ditt larm till exempel inaktive ras, svarar du genom att trycka på vilo läge, som är en feedback till tjänsten. Tänk på detta som det svar du ger tjänsten. Du kan visa det här svaret i portalen.

    1. Börjar skicka telemetri. **Pnp_send_telemetry** definieras i filen pnp_methods. py. Exempel koden använder en slinga för att anropa den här funktionen var åttonde sekund.

    1. Inaktiverar alla lyssnare och uppgifter och finns i slingan när du trycker på **q** eller **q**.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Nu när du har sett koden använder du följande kommando för att köra exemplet:

```cmd/sh
python simple_thermostat.py
```

Följande utdata visas, som anger att enheten skickar telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Interagera med en IoT Plug and Play-enhet som är ansluten till din lösning](quickstart-service-python.md)
