---
title: "Ansluta en gateway för Azure IoT Suite med en Intel NUC | Microsoft Docs"
description: "Använd Microsoft IoT kommersiella Gateway Kit och fjärråtkomst övervakning förkonfigurerade lösningen. Använd Azure IoT gräns-gatewayen för att aktivera en SensorTag-enhet för att ansluta till den fjärranslutna övervakningslösning skicka telemetri till molnet och svara på metoderna som anropas från instrumentpanelen lösning."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: bda16be1094276fcecef1e708f9d7db307d94a89
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Anslut dina Azure IoT gräns-gatewayen till fjärråtkomst övervakning förkonfigurerade lösningen och skicka telemetri från en SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

Den här kursen visar hur du använder Azure IoT Edge för att skicka data för temperatur- och fuktighetskonsekvens från SensorTag enhet till fjärråtkomst övervakning förkonfigurerade lösningen. SensorTag ansluter till Intel NUC gatewayen med Bluetooth. I självstudiekursen används:

- Azure IoT-Edge att implementera en exempel-gateway.
- IoT Suite fjärråtkomst övervakning förkonfigurerade lösning som molnbaserade serverdelen.

## <a name="overview"></a>Översikt

I den här kursen kan du utföra följande steg:

- Distribuera en instans av fjärråtkomst övervakning förkonfigurerade lösningen till din Azure-prenumeration. Det här steget kan du automatiskt distribuerar och konfigurerar Azure-tjänster.
- Ställ in Intel NUC-gateway-enheten att kommunicera med datorn och den fjärranslutna övervakningslösning.
- Ställ in din Intel NUC gateway att ta emot telemetri från en SensorTag-enhet och skicka den till fjärranslutna instrumentpanelen för övervakning.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[Texas Instruments TIVERA SensorTag][lnk-sensortag]. Den här självstudiekursen hämtar telemetridata via Bluetooth från SensorTag-enheter.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> Fjärråtkomst övervakningslösning etablerar en mängd olika Azure-tjänster i din Azure-prenumeration. Distributionen visar en verklig enterprise-arkitektur. Ta bort din instans av förkonfigurerade lösningen vid azureiotsuite.com för att undvika onödiga Azure-förbrukningen avgifter, när du är klar med den. Om du behöver den förkonfigurerade lösningen, kan du enkelt återskapa den. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Konfigurera Bluetooth-anslutning

Konfigurera Bluetooth på Intel NUC så att SensorTag-enheten att ansluta och skicka telemetri.

### <a name="find-the-mac-address-of-the-sensortag"></a>Hitta MAC-adressen för SensorTag

1. Shell på Intel NUC, kör du följande kommando för att avblockera Bluetooth-tjänst:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Kör följande kommandon för att starta tjänsten Bluetooth på Intel NUC och ange Bluetooth-gränssnittet:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Kör följande kommando för att stänga på Bluetooth-domänkontrollant:

    ```bash
    power on
    ```

    När styrenheten är aktiverat kan du se ett meddelande **ändra power på lyckades**.

1. Kör följande kommando för att söka efter enheter i närheten Bluetooth:

    ```bash
    scan on
    ```

1. Tryck på strömknappen på SensorTag så att den kan identifieras. Grön Indikator blinkar.

1. När du ser ett meddelande i gränssnittet att kontrollanten har identifierats i SensorTag anteckna MAC-adressen till enheten. MAC-adressen ser ut som **A0:E6:F8:B5:F6:00**. Du behöver MAC-adressen senare under kursen när du konfigurerar en gateway.

1. Kör följande kommando för att inaktivera Bluetooth-sökning:

    ```bash
    scan off
    ```

1. Kör följande kommando för att kontrollera att du kan ansluta till SensorTag-enhet:

    ```bash
    connect <SensorTag MAC address>
    ```

    Om du ansluter har gränssnittet visas meddelandet **lyckad anslutning** och skriver ut information om SensorTag-enhet. Om du inte kan ansluta kontrollerar du SensorTag fortfarande är påslagen.

1. Du kan nu koppla från SensorTag och lämna Bluetooth-gränssnittet genom att köra följande kommandon:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Skapa anpassad IoT kant-modul

Du kan nu skapa anpassade IoT kant-modulen som gör att en gateway att skicka meddelanden till den fjärranslutna övervakningslösning. Mer information om hur du konfigurerar en gateway- och IoT-Edge moduler finns [Azure IoT kant begrepp][lnk-gateway-concepts].

Ladda ned källkoden för de anpassade IoT kant-modulerna från GitHub med hjälp av följande kommandon:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Skapa anpassade IoT kant modulen med följande kommandon:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Build-skript placerar libsensor2remotemonitoring.so anpassad IoT kant-modul i build-mappen.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Konfigurera och köra IoT gräns-gatewayen

Du kan nu konfigurera IoT gräns-gatewayen för att skicka telemetri från enheten SensorTag till instrumentpanelen för fjärråtkomst övervakning. Mer information om hur du konfigurerar en gateway- och IoT-Edge moduler finns [Azure IoT kant begrepp][lnk-gateway-concepts].

> [!TIP]
> I kursen får du använder standard `vi` textredigerare på Intel NUC. Om du inte har använt `vi` innan, bör du genomföra en inledande vägledning som [Unix - vi Editor kursen] [ lnk-vi-tutorial] att bekanta dig med den här redigeraren. Du kan också installera mer användarvänlig [nano](https://www.nano-editor.org/) redigeraren med hjälp av kommandot `smart install nano -y`.

Öppna exempelkonfigurationsfilen i den **vi** redigeraren med följande kommando:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Leta upp följande rader i konfigurationen för IoTHub-modulen:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Vill du ersätta platshållarvärdena med IoT-hubb-information som du skapade och sparade i början av den här kursen. Värdet för IoTHubName ser ut som **yourrmsolution37e08**, och värdet för IoTSuffix är vanligtvis **azure devices.net**.

Leta upp följande rader i konfigurationen för modulen mappning:

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Ersätt den **macAddress** med MAC-adressen för din SensorTag som du antecknade tidigare. Ersätt den **deviceID** och **deviceKey** platshållarna med ID: N och nycklarna för de två enheter som du skapade tidigare i den fjärranslutna övervakningslösning.

Leta upp följande rader i konfigurationen för SensorTag-modulen:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Ersätt den **enhet\_mac\_adress** med MAC-adressen för din SensorTag som du antecknade tidigare.

Spara ändringarna.

Du kan nu köra gatewayen med följande kommandon:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

IoT-gräns-gatewayen på Intel NUC påbörjas och skickar telemetri från SensorTag till fjärranslutna övervakningslösning:

![IoT-gräns-gatewayen skickar telemetri från SensorTag][img-telemetry]

Tryck på **Ctrl-C** avsluta programmet när som helst.

## <a name="view-the-telemetry"></a>Visa telemetrin

Gatewayen är nu skicka telemetri från SensorTag-enhet till fjärranslutna övervakningslösning. Du kan visa telemetrin på instrumentpanelen för lösningen. Du kan även skicka kommandon till enheten SensorTag via gatewayen från instrumentpanelen lösning.

- Gå till instrumentpanelen lösning.
- Välj den enhet som du konfigurerade i den gateway som representerar SensorTag i den **enhet för att visa** listrutan.
- Telemetri från SensorTag-enheten visas på instrumentpanelen.

![Visa telemetri från SensorTag-enheter][img-telemetry-display]

> [!WARNING]
> Om du lämnar den fjärranslutna övervakningslösning som körs i ditt Azure-konto, debiteras du för den tid som den körs. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config]. Ta bort den förkonfigurerade lösningen från ditt Azure-konto när du är klar.


## <a name="next-steps"></a>Nästa steg

Besök den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) fler exempel och dokumentation om Azure IoT.

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started