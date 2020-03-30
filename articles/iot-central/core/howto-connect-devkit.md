---
title: Ansluta en DevKit-enhet till ditt Azure IoT Central-program | Microsoft-dokument
description: Som enhetsutvecklare kan du läsa om hur du ansluter en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program med IoT Plug and Play (förhandsversion).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 0a393ae8629f1742002344ee717a6719269a6722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158663"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program

Den här artikeln visar hur du ansluter en MXChip IoT DevKit-enhet (DevKit) till ett Azure IoT Central-program. Enheten använder den certifierade IoT Plug and Play -modellen (preview) för DevKit-enheten för att konfigurera anslutningen till IoT Central.

I den här artikeln gäller hur du gör:

- Hämta anslutningsinformation från ditt IoT Central-program.
- Förbered enheten och anslut den till ditt IoT Central-program.
- Visa telemetri och egenskaper från enheten i IoT Central.

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln behöver du följande resurser:

- En [DevKit-enhet](https://aka.ms/iot-devkit-purchase).
- Ett IoT Central-program. Du kan följa stegen i [Skapa ett IoT Central-program](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Hämta information om enhetsanslutning

1. I ditt Azure IoT Central-program väljer du fliken **Enhetsmallar** och väljer **+ Nytt**. Välj **MXChip IoT DevKit**i avsnittet **Använd en förkonfigurerad enhetsmall**.

    ![Enhetsmall för MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Välj **Nästa: Anpassa** och **skapa**sedan .

1. Fliken **Välj enheter.** I enhetslistan väljer du **MXChip IoT DevKit** och väljer **+ Nytt** för att skapa en ny enhet från mallen.

    ![Ny enhet](media/howto-connect-devkit/new-device.png)

1. I popup-fönstret anger du **enhets-ID:t** som `SampleDevKit` och **Enhetsnamn** som `MXChip IoT DevKit - Sample`. Kontrollera att alternativet Simulerad är **inaktiverat.** Välj sedan **Skapa**.

    ![Enhets-ID och namn](media/howto-connect-devkit/device-id-name.png)

1. Markera den enhet du skapade och välj sedan **Anslut**. Anteckna **ID-scope,** **enhets-ID**och **Primärnyckel**. Du behöver dessa värden senare i den här how-to artikeln.

    ![Information om enhetsanslutning](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Förbered enheten

1. Ladda ned den senaste [färdiga Azure IoT Central Plug and Play (preview) firmware](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) för DevKit-enheten från GitHub.

1. Anslut DevKit-enheten till utvecklingsmaskinen med en USB-kabel. I Windows öppnas ett utforskarfönster på en enhet som mappas till lagringen på DevKit-enheten. Enheten kan till exempel heta **AZ3166 (D:)**.

1. Dra **filen iotc_devkit.bin** till enhetsfönstret. När kopieringen är klar startas enheten om med den nya inbyggda programvaran.

    > [!NOTE]
    > Om du ser fel på skärmen, till exempel **Inget Wi-Fi,** beror det på att DevKit ännu inte har anslutits till WiFi.

1. Håll ned **knapp B**på DevKit, tryck och släpp **återställningsknappen** och släpp sedan **knappen B**. Enheten är nu i åtkomstpunktsläge. För att bekräfta, visar skärmen "IoT DevKit - AP" och konfigurationsportalen IP-adress.

1. Anslut till det wi-fi-nätverksnamn som visas på enhetens skärm på datorn eller surfplattan. WiFi-nätverket börjar med AZ- följt av **MAC-adressen.** När du ansluter till det här nätverket har du inte tillgång till internet. Det här tillståndet förväntas och du ansluter bara till det här nätverket under en kort tid medan du konfigurerar enheten.

1. Öppna webbläsaren och navigera [http://192.168.0.1/](http://192.168.0.1/)till . Följande webbsida visas:

    ![Config-användargränssnittet](media/howto-connect-devkit/config-ui.png)

    På webbsidan anger du:

    - Namnet på ditt WiFi-nätverk (SSID).
    - Ditt lösenord för ditt WiFi-nätverk.
    - Anslutningsinformationen: ange **enhets-ID-** **och ID-scopet**och **SAS primärnyckel som** du har noterat tidigare.

    > [!NOTE]
    > För närvarande kan IoT DevKit bara ansluta till 2,4 GHz Wi-Fi, 5 GHz stöds inte på grund av maskinvarubegränsningar.

1. Välj **Konfigurera enhet**startar DevKit-enheten om och kör programmet:

    ![Starta om användargränssnittet](media/howto-connect-devkit/reboot-ui.png)

    På devkit-skärmen visas en bekräftelse på att programmet körs:

    ![DevKit körs](media/howto-connect-devkit/devkit-running.png)

DevKit registrerar först en ny enhet i IoT Central-programmet och börjar sedan skicka data.

## <a name="view-the-telemetry"></a>Visa telemetrin

I det här steget visar du telemetrin i ditt Azure IoT Central-program.

I ditt IoT Central-program väljer du fliken **Enheter,** väljer den enhet du har lagt till. På fliken **Översikt** kan du se telemetrin från DevKit-enheten:

![Översikt över IoT Central-enheten](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Granska koden

Om du vill granska koden eller ändra och kompilera den går du till [kodexemplen](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till ditt Azure IoT Central-program, är det föreslagna nästa steget att lära dig hur [du konfigurerar en anpassad enhetsmall](./howto-set-up-template.md) för din egen IoT-enhet.
