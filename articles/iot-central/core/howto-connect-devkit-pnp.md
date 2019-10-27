---
title: Ansluta en DevKit-enhet till ditt Azure IoT Central-program | Microsoft Docs
description: Som enhets utvecklare lär du dig att ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program med IoT Plug and Play.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: b7d2e1b08653cb8023ef6a5190ab53ecc3d568a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951515"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program

Den här artikeln visar hur du ansluter en DevKit-enhet (MXChip IoT DevKit) till ett Azure IoT Central-program. Enheten använder den certifierade IoT Plug and Play-modellen för DevKit-enheten för att konfigurera anslutningen till IoT Central.

I den här instruktions artikeln kan du:

- Hämta anslutnings informationen från IoT Central-programmet.
- Förbered enheten och Anslut den till ditt IoT Central-program.
- Visa telemetri och egenskaper från enheten i IoT Central.

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln behöver du följande resurser:

1. En [DevKit-enhet](https://aka.ms/iot-devkit-purchase).
1. Ett IoT Central-program som skapats från program mal len för för **hands versionen** . Du kan följa stegen i [skapa ett IoT plug and Play-program](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="get-device-connection-details"></a>Hämta information om enhets anslutning

I ditt Azure IoT Central-program väljer du fliken **Administration** och sedan **enhets anslutning**. Anteckna **ID-omfånget** och **primär nyckeln**.

![Anslutnings information för enhets grupp](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Förbered enheten

1. Ladda ned den senaste [inbyggda Azure IoT Central plug and Play-programvaran](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) för enheten DevKit från GitHub.

1. Anslut DevKit-enheten till utvecklings datorn med en USB-kabel. I Windows öppnas ett Utforskaren-fönster på en enhet som är mappad till lagringen på DevKit-enheten. Enheten kan till exempel kallas **AZ3166 (D:)** .

1. Dra filen **iotc_devkit. bin** till enhets fönstret. När kopieringen är klar startas enheten om med den nya inbyggda program varan.

    > [!NOTE]
    > Om du ser fel på skärmen, till exempel **inget Wi-Fi**, beror det på att DevKit ännu inte har anslutits till WiFi.

1. På DevKit, håller du ned **knapp B**, push och släpper knappen **Återställ** och släpper sedan **knappen b**. Enheten är nu i åtkomst punkt läge. För att bekräfta, visar skärmen "IoT DevKit-AP" och IP-adressen för konfigurations portalen.

1. På din dator eller surfplatta ansluter du till det WiFi-nätverks namn som visas på enhetens skärm. WiFi-nätverket börjar med **AZ –** följt av Mac-adressen. När du ansluter till det här nätverket har du inte till gång till Internet. Detta tillstånd förväntas och du ansluter bara till det här nätverket under en kort tid medan du konfigurerar enheten.

1. Öppna webbläsaren och gå till [http://192.168.0.1/](http://192.168.0.1/). Följande webb sida visas:

    ![Konfigurations gränssnitt](media/howto-connect-devkit-pnp/config-ui.png)

    På webb sidan anger du:

    - Namnet på ditt WiFi-nätverk (SSID).
    - Ditt WiFi-nätverks lösen ord.
    - Anslutnings information: det **enhets-ID** som du kan välja själv och **ID-omfånget** och **primär grupp SAS-nyckeln** som du antecknade tidigare.

    > [!NOTE]
    > IoT-DevKit kan för närvarande bara ansluta till 2,4 GHz Wi-Fi, 5 GHz stöds inte på grund av maskin varu begränsningar.

1. Välj **Konfigurera enhet**, DevKit-enheten startar om och kör programmet:

    ![Starta om användar gränssnitt](media/howto-connect-devkit-pnp/reboot-ui.png)

    Skärmen DevKit visar en bekräftelse på att programmet körs:

    ![DevKit körs](media/howto-connect-devkit-pnp/devkit-running.png)

DevKit registrerar först en ny enhet i IoT Central programmet och börjar sedan skicka data.

## <a name="view-the-telemetry"></a>Visa Telemetrin

I det här steget visar du Telemetrin i ditt Azure IoT Central-program.

Välj fliken **enheter** i ditt IoT Central program och välj den enhet som du har lagt till. På fliken **Översikt** kan du se Telemetrin från DevKit-enheten:

   ![Översikt över IoT Central enhet](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>Granska koden

Om du vill granska koden eller ändra och kompilera den går du till [kod exemplen](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) för din egen IoT-enhet.
