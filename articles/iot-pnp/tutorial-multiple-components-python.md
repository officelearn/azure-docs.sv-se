---
title: Ansluta IoT Plug and Play Preview-exempel python-komponent enhets kod till IoT Hub | Microsoft Docs
description: Skapa och kör IoT Plug and Play Preview exempel på python-enhets kod som använder flera komponenter och ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905864"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Självstudie: ansluta en exempel-IoT Plug and Play förhandsgranska flera komponent enhets program till IoT Hub (python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter och rot gränssnitt, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet skrivs i python och ingår i Azure IoT-enhetens SDK för python. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du python 3,7 på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/). Du kan kontrol lera din python-version med följande kommando:  

```cmd/sh
python --version
```

Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här självstudien använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här självstudien:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här självstudien:

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

Klona IoT SDK IoT-lagringsplatsen och kolla in **PnP-Preview-Refresh**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Modellen som det här exemplet implementerar använder [flera komponenter](concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Mappen *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* innehåller exempel koden för IoT plug and Play-enheten. Filerna för temperatur styrenhets exemplet är:

- pnp_temp_controller_with_thermostats. py
- pnp_helper_preview_refresh. py

Temperatur styrenheten har flera komponenter och ett rot gränssnitt baserat på temperatur styrenhetens DTDL-modell.

Öppna filen *pnp_temp_controller_with_thermostats. py* i valfritt redigerings program. Koden i den här filen:

1. Importer `pnp_helper_preview_refresh.py` för att få åtkomst till hjälp metoder.

2. Definierar två digitala dubbla modell identifierare (DTMIs) som unikt representerar två olika gränssnitt, definierade i DTDL-modellen. Komponenterna i en riktig temperatur kontroll bör implementera dessa två gränssnitt. Dessa två gränssnitt har redan publicerats på en central lagrings plats. Dessa DTMIs måste vara kända för användaren och varierar beroende på användnings scenariot för enheten. I det aktuella exemplet representerar dessa två gränssnitt:

  - En termostat
  - Enhets information som har utvecklats av Azure.

3. Definierar DTMI `model_id` för den enhet som implementeras. DTMI är användardefinierad och måste matcha DTMI i DTDL modell fil.

4. Definierar de namn som har angetts för komponenterna i DTDL-filen. Det finns två termostater i DTDL och en komponent för enhets information. En konstant `serial_number` som kallas är också definierad i rot gränssnittet. En `serial_number` enhet kan inte ändras.

5. Definierar implementeringar av kommando hanterare. Dessa definierar vad enheten gör när den tar emot kommando begär Anden.

6. Definierar funktioner för att skapa ett kommando svar. Dessa definierar hur enheten svarar med till-kommando begär Anden. Du skapar funktioner för kommando svar om ett kommando behöver skicka ett anpassat svar tillbaka till IoT Hub. Om en Response-funktion för ett kommando inte anges skickas ett allmänt svar. I det här exemplet har endast kommandot **getMaxMinReport** ett anpassat svar.

7. Definierar en funktion för att skicka telemetri från den här enheten. Både termostater och rot gränssnittet skickar telemetri. Den här funktionen tar i en valfri komponent namn parameter för att kunna identifiera vilken komponent som skickade telemetri.

8. Definierar en lyssnare för kommando begär Anden.

9. Definierar en lyssnare för önskade egenskaps uppdateringar.

10. Har en `main` funktion som:

    1. Använder enhets-SDK för att skapa en enhets klient och ansluta till IoT Hub. Enheten skickar `model_id` så att IoT Hub kan identifiera enheten som en IoT plug and Play-enhet.

    1. Använder `create_reported_properties` funktionen i Hjälp filen för att skapa egenskaperna. Överför komponent namnet och egenskaperna som nyckel värdes par till den här funktionen.

    1. Uppdaterar läsbara egenskaper för dess komponenter genom att anropa `patch_twin_reported_properties` .

    1. Börjar lyssna efter kommando begär Anden med hjälp av `execute_command_listener` funktionen. Funktionen konfigurerar en lyssnare för kommando begär Anden från tjänsten. När du konfigurerar lyssnaren anger du en `method_name` , `user_command_handler` och en valfri `create_user_response_handler` som-parameter.
        - `method_name`Definierar kommando förfrågan. I det här exemplet definierar modellen kommandona **reboot**och **getMaxMinReport**.
        - `user_command_handler`Funktionen definierar vad enheten ska göra när den får ett kommando.
        - `create_user_response_handler`Funktionen skapar ett svar som skickas till din IoT-hubb när ett kommando körs utan problem. Du kan visa det här svaret i portalen. Om den här funktionen inte anges skickas ett allmänt svar till tjänsten.

    1. Använder `execute_property_listener` för att lyssna efter egenskaps uppdateringar.

    1. Börjar skicka telemetri med `send_telemetry` . Exempel koden använder en slinga för att anropa tre telemetri som skickar funktioner. Var och en kallas var åttonde sekund

    1. Inaktiverar alla lyssnare och uppgifter och avslutar slingan när du trycker på **q** eller **q**.

Nu när du har sett koden skapar du en miljö variabel som heter **IOTHUB_DEVICE_CONNECTION_STRING** för att lagra enhets anslutnings strängen som du antecknade tidigare. Använd följande kommando för att köra exemplet:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

Exempel enheten skickar telemetridata med några sekunder till din IoT-hubb.

Följande utdata visas, som anger att enheten skickar telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar.

![Enhets bekräftelse meddelanden](media/tutorial-multiple-components-python/multiple-component.png)

Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter en IoT Plug and Play-enhet med komponenter till en IoT-hubb. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Preview Modeling Developer Guide](concepts-developer-guide.md)
