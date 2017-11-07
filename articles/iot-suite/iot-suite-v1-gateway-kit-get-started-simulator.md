---
title: "Ansluta en gateway för Azure IoT Suite med en Intel NUC | Microsoft Docs"
description: "Använd Microsoft IoT kommersiella Gateway Kit och fjärråtkomst övervakning förkonfigurerade lösningen. Använda Azure IoT gräns-gatewayen för att ansluta till den fjärranslutna övervakningslösning skicka telemetri om simulerad till molnet och svara på metoderna som anropas från instrumentpanelen lösning."
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: f3b4a80b37b0b869847c90834731d4c23883a961
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Anslut dina Azure IoT gräns-gatewayen till fjärråtkomst övervakning förkonfigurerade lösningen och skicka telemetri om simulerad

[!INCLUDE [iot-suite-v1-gateway-kit-selector](../../includes/iot-suite-v1-gateway-kit-selector.md)]

Den här kursen visar hur du använder Azure IoT kant för att simulera temperatur- och fuktighetskonsekvens data ska skickas till den fjärranslutna förkonfigurerade övervakningslösning. I självstudiekursen används:

- Azure IoT-Edge att implementera en exempel-gateway.
- IoT Suite fjärråtkomst övervakning förkonfigurerade lösning som molnbaserade serverdelen.

## <a name="overview"></a>Översikt

I den här kursen kan du utföra följande steg:

- Distribuera en instans av fjärråtkomst övervakning förkonfigurerade lösningen till din Azure-prenumeration. Det här steget kan du automatiskt distribuerar och konfigurerar Azure-tjänster.
- Ställ in Intel NUC-gateway-enheten att kommunicera med datorn och den fjärranslutna övervakningslösning.
- Konfigurera IoT gräns-gatewayen om du vill skicka simulerade telemetri som kan visas på instrumentpanelen för lösningen.

[!INCLUDE [iot-suite-v1-gateway-kit-prerequisites](../../includes/iot-suite-v1-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Fjärråtkomst övervakningslösning etablerar en mängd olika Azure-tjänster i din Azure-prenumeration. Distributionen visar en verklig enterprise-arkitektur. Ta bort din instans av förkonfigurerade lösningen vid azureiotsuite.com för att undvika onödiga Azure-förbrukningen avgifter, när du är klar med den. Om du behöver den förkonfigurerade lösningen, kan du enkelt återskapa den. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-v1-gateway-kit-view-solution](../../includes/iot-suite-v1-gateway-kit-view-solution.md)]

Upprepa föregående steg för att lägga till en andra enhet med enhets-ID, till exempel **device02**. Exemplet skickar data från två simulerade enheter i gateway för fjärråtkomst övervakningslösning.

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-connectivity.md)]

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-software](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Skapa anpassad IoT kant-modul

Du kan nu skapa anpassade IoT kant-modulen som gör att en gateway att skicka meddelanden till den fjärranslutna övervakningslösning. Mer information om hur du konfigurerar en gateway- och IoT-Edge moduler finns [Azure IoT kant begrepp][lnk-gateway-concepts].

Ladda ned källkoden för de anpassade IoT kant-modulerna från GitHub med hjälp av följande kommandon:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Skapa anpassade IoT kant modulen med följande kommandon:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Build-skript placerar libsimulator.so anpassad IoT kant-modul i build-mappen.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Konfigurera och köra IoT gräns-gatewayen

Du kan nu konfigurera IoT gräns-gatewayen för att skicka telemetri om simulerad i instrumentpanelen för fjärråtkomst övervakning. Mer information om hur du konfigurerar en gateway- och IoT-Edge moduler finns [Azure IoT kant begrepp][lnk-gateway-concepts].

> [!TIP]
> I kursen får du använder standard `vi` textredigerare på Intel NUC. Om du inte har använt `vi` innan, bör du genomföra en inledande vägledning som [Unix - vi Editor kursen] [ lnk-vi-tutorial] att bekanta dig med den här redigeraren. Du kan också installera mer användarvänlig [nano](https://www.nano-editor.org/) redigeraren med hjälp av kommandot `smart install nano -y`.

Öppna exempelkonfigurationsfilen i den **vi** redigeraren med följande kommando:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Ersätt den **deviceID** och **deviceKey** platshållarna med ID: N och nycklarna för de två enheter som du skapade tidigare i den fjärranslutna övervakningslösning.

Spara ändringarna.

Du kan nu köra IoT gräns-gatewayen med följande kommandon:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

Gatewayen på Intel NUC påbörjas och skickar simulerade telemetri till fjärranslutna övervakningslösning:

![IoT-gräns-gatewayen genererar simulerade telemetri][img-simulated telemetry]

Tryck på **Ctrl-C** avsluta programmet när som helst.

## <a name="view-the-telemetry"></a>Visa telemetrin

IoT gräns-gatewayen är nu skicka simulerade telemetri till fjärranslutna övervakningslösning. Du kan visa telemetrin på instrumentpanelen för lösningen.

- Gå till instrumentpanelen lösning.
- Välj en av de två enheterna som du konfigurerade i gatewayen på den **enhet för att visa** listrutan.
- Telemetri från gatewayenheter visas på instrumentpanelen.

![Visa telemetri från simulerade gatewayenheter][img-telemetry-display]

> [!WARNING]
> Om du lämnar den fjärranslutna övervakningslösning som körs i ditt Azure-konto, debiteras du för den tid som den körs. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config]. Ta bort den förkonfigurerade lösningen från ditt Azure-konto när du är klar.

## <a name="next-steps"></a>Nästa steg

Besök den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) fler exempel och dokumentation om Azure IoT.

[img-simulated telemetry]: ./media/iot-suite-v1-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-v1-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started