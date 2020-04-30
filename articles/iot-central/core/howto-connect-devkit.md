---
title: Ansluta en DevKit-enhet till ditt Azure IoT Central-program | Microsoft Docs
description: Som enhets utvecklare lär du dig att ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program med IoT Plug and Play (för hands version).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756805"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program

*Den här artikeln gäller för enhets utvecklare.*

Den här artikeln visar hur du ansluter en DevKit-enhet (MXChip IoT DevKit) till ett Azure IoT Central-program. Enheten använder den certifierade IoT Plug and Play-modellen (för hands version) för DevKit-enheten för att konfigurera anslutningen till IoT Central.

I den här instruktions artikeln kan du:

- Hämta anslutnings informationen från IoT Central-programmet.
- Förbered enheten och Anslut den till ditt IoT Central-program.
- Visa telemetri och egenskaper från enheten i IoT Central.

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln behöver du följande resurser:

- En [DevKit-enhet](https://aka.ms/iot-devkit-purchase).
- Ett IoT Central-program. Du kan följa stegen i [skapa ett IoT Central-program](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Hämta information om enhets anslutning

1. I ditt Azure IoT Central-program väljer du fliken **enhets mallar** och sedan **+ ny**. I avsnittet **Använd en förkonfigurerad enhets mall**väljer du **MXChip IoT DevKit**.

    ![Enhets mal len för MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Välj **Nästa: anpassa** och **skapa**.

1. Fliken Välj **enheter** . I listan enheter väljer du **MXChip IoT DevKit** och väljer **+ ny** för att skapa en ny enhet från mallen.

    ![Ny enhet](media/howto-connect-devkit/new-device.png)

1. I popup-fönstret anger du **enhets-ID: t** `SampleDevKit` och **enhets namnet** som `MXChip IoT DevKit - Sample`. Se till att alternativet **simulerat** är inaktiverat. Välj sedan **skapa**.

    ![Enhets-ID och namn](media/howto-connect-devkit/device-id-name.png)

1. Välj den enhet som du skapade och välj sedan **Anslut**. Anteckna **ID-omfånget**, **enhets-ID: t**och **primär nyckeln**. Du behöver dessa värden senare i den här instruktions artikeln.

    ![Information om enhets anslutning](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Förbered enheten

1. Ladda ned den senaste [inbyggda Azure IoT Central plug and Play-programvaran (för hands version)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) för DevKit-enheten från GitHub.

1. Anslut DevKit-enheten till utvecklings datorn med en USB-kabel. I Windows öppnas ett Utforskaren-fönster på en enhet som är mappad till lagringen på DevKit-enheten. Enheten kan till exempel kallas **AZ3166 (D:)**.

1. Dra filen **iotc_devkit. bin** till enhets fönstret. När kopieringen är klar startas enheten om med den nya inbyggda program varan.

    > [!NOTE]
    > Om du ser fel på skärmen, till exempel **inget Wi-Fi**, beror det på att DevKit ännu inte har anslutits till WiFi.

1. På DevKit, håller du ned **knapp B**, push och släpper knappen **Återställ** och släpper sedan **knappen b**. Enheten är nu i åtkomst punkt läge. För att bekräfta, visar skärmen "IoT DevKit-AP" och IP-adressen för konfigurations portalen.

1. På din dator eller surfplatta ansluter du till det WiFi-nätverks namn som visas på enhetens skärm. WiFi-nätverket börjar med **AZ –** följt av Mac-adressen. När du ansluter till det här nätverket har du inte till gång till Internet. Detta tillstånd förväntas och du ansluter bara till det här nätverket under en kort tid medan du konfigurerar enheten.

1. Öppna webbläsaren och gå till [http://192.168.0.1/](http://192.168.0.1/). Följande webb sida visas:

    ![Konfigurations gränssnitt](media/howto-connect-devkit/config-ui.png)

    På webb sidan anger du:

    - Namnet på ditt WiFi-nätverk (SSID).
    - Ditt WiFi-nätverks lösen ord.
    - Anslutnings information: ange **enhets-ID**, **ID-omfång**och **primär SAS-nyckel** som du antecknade tidigare.

    > [!NOTE]
    > IoT-DevKit kan för närvarande bara ansluta till 2,4 GHz Wi-Fi, 5 GHz stöds inte på grund av maskin varu begränsningar.

1. Välj **Konfigurera enhet**, DevKit-enheten startar om och kör programmet:

    ![Starta om användar gränssnitt](media/howto-connect-devkit/reboot-ui.png)

    Skärmen DevKit visar en bekräftelse på att programmet körs:

    ![DevKit körs](media/howto-connect-devkit/devkit-running.png)

DevKit registrerar först en ny enhet i IoT Central programmet och börjar sedan skicka data.

## <a name="view-the-telemetry"></a>Visa Telemetrin

I det här steget visar du Telemetrin i ditt Azure IoT Central-program.

Välj fliken **enheter** i ditt IoT Central program och välj den enhet som du har lagt till. På fliken **Översikt** kan du se Telemetrin från DevKit-enheten:

![Översikt över IoT Central enhet](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Granska koden

Om du vill granska koden eller ändra och kompilera den går du till [kod exemplen](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Nästa steg

Om du är enhets utvecklare är några förslag på nästa steg att:

- Läs om [enhets anslutning i Azure IoT Central](./concepts-get-connected.md)
- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
