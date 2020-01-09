---
title: Ansluta IoT Plug and Play förhandsgranska exempel enhets kod till IoT Hub | Microsoft Docs
description: Med Java, skapa och köra IoT Plug and Play exempel enhets kod som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e1454a197620cacaa8c8303e8f36a8d4a87aec00
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531317"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Snabb start: ansluta ett exempel på IoT Plug and Play Preview enhets program till IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet i Java och tillhandahålls som en del av Azure IoT-exemplen för Java-insamling. En lösnings utvecklare kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du Java SE 8 på din utvecklings dator. Du måste också installera maven 3.

Mer information om hur du kommer igång med dessa finns i [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) i Microsoft Azure IoT-enhetens SDK för Java.

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databas](https://github.com/Azure/azure-iot-explorer/releases) sida genom att välja. msi-filen under "till gångar" för den senaste uppdateringen.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för din hubb (Anmärkning för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT-exempel för Java.

Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exemplen för Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub-lagringsplatsen till den här platsen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Den här åtgärden kan ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Du använder den klonade exempel koden för att bygga ett program som simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I ett lokalt terminalfönster går du till mappen för den klonade lagrings platsen och navigerar till mappen **/Azure-IoT-samples-Java/Digital-Twin/samples/Device/JdkSample** . Kör sedan följande kommando för att installera de bibliotek som krävs och skapa det simulerade enhets programmet:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Kör ett exempel program för att simulera en IoT Plug and Play-enhet som skickar telemetri till IoT Hub. Använd följande kommando för att köra exempel programmet:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Du ser meddelanden som säger att enheten är ansluten, utför olika installations steg och väntar på tjänst uppdateringar, följt av telemetri loggar. Detta anger att enheten nu är redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om du vill se till att verktyget kan läsa definitionerna för gränssnitts modellen från enheten väljer du **Inställningar**. På menyn Inställningar kan det hända att **den anslutna enheten** redan visas i Plug and Play konfigurationer. om den inte gör det väljer du **+ Lägg till modulens definitions källa** och sedan **på den anslutna enheten** för att lägga till den.

1. På sidan **enhets** Översikt hittar du enhets identiteten som du skapade tidigare. När enhets programmet fortfarande körs i kommando tolken kontrollerar du att enhetens **anslutnings status** i Azure IoT Explorer rapporterar som _ansluten_ (om inte klickar du på **Uppdatera** tills den är). Välj enheten om du vill visa mer information.

1. Expandera gränssnittet med ID **urn: java_sdk_sample: EnvironmentalSensor: 1** om du vill visa gränssnittet och IoT plug and Play-primitiver – egenskaper, kommandon och telemetri.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en IoT Plug and Play för hands version](howto-develop-solution.md)
