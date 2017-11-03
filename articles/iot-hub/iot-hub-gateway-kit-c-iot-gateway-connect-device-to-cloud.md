---
title: "Använda en IoT-gateway för att ansluta en enhet till Azure IoT Hub | Microsoft Docs"
description: "Lär dig hur du använder Intel NUC som en IoT-gateway för att ansluta en TI SensorTag och skicka sensordata till Azure IoT-hubb i molnet."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: IOT-gateway ansluter enheten till molnet
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Använd IoT-gateway för att ansluta saker till molnet - SensorTag till Azure IoT-hubb

> [!NOTE]
> Innan du börjar den här självstudiekursen, kontrollera att du har slutfört [konfigurera Intel NUC som en IoT-gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). I [konfigurera Intel NUC som en IoT-gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), du konfigurerar Intel NUC enheten som en IoT-gateway.

## <a name="what-you-will-learn"></a>Vad får du lära dig

Du lär dig hur du använder en IoT-gateway för att ansluta en Texas instrument SensorTag (CC2650STK) till Azure IoT Hub. IoT-gateway skickar temperatur- och fuktighetskonsekvens data som samlas in från SensorTag till Azure IoT Hub.

## <a name="what-you-will-do"></a>Vad du ska göra

- Skapa en IoT-hubb.
- Registrera en enhet i IoT-hubb för SensorTag.
- Aktivera anslutningen mellan IoT-gateway och SensorTag.
- Kör ett TIVERA exempelprogram SensorTag data ska skickas till din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

- Kursen [konfigurera Intel NUC som en IoT-gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) slutförts i som du konfigurerar Intel NUC som en IoT-gateway.
- * En aktiv Azure-prenumeration. Om du inte har ett Azure-konto [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) i bara några minuter.
- En SSH-klient som körs på värddatorn. PuTTY rekommenderas i Windows. Linux- och macOS har redan en SSH-klient.
- IP-adressen och användarnamn och lösenord för åtkomst till gatewayen från SSH-klienten.
- En Internetanslutning.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Här registrerar du enheten för dina SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Aktivera anslutningen mellan IoT-gateway och SensorTag

I det här avsnittet kan du utföra följande uppgifter:

- Hämta MAC-adressen för SensorTag för Bluetooth-anslutning.
- Initiera en Bluetooth-anslutning från IoT-gateway till SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Hämta MAC-adressen för SensorTag för Bluetooth-anslutning

1. Kör SSH-klienten på värddatorn och ansluta till IoT-gateway.
1. Tillåt Bluetooth genom att köra följande kommando:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Starta tjänsten Bluetooth på IoT-gateway och ange ett Bluetooth-gränssnitt för att konfigurera Bluetooth genom att köra följande kommandon:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Slå på Bluetooth-domänkontrollant genom att köra följande kommando i gränssnittet Bluetooth:

   ```bash
   power on
   ```

   ![Slå på Bluetooth-controller i IoT-gateway med bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Starta sökning efter Bluetooth-enheter i närheten genom att köra följande kommando:

   ```bash
   scan on
   ```

   ![Skanna Närliggande Bluetooth-enheter med bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Tryck på knappen kopplat på SensorTag. Gröna LETT på SensorTag gånger.
1. Du bör se SensorTag hittas på Bluetooth-gränssnittet. Anteckna SensorTag MAC-adress. I det här exemplet är MAC-adressen för SensorTag `24:71:89:C0:7F:82`.
1. Inaktivera genomsökningen genom att köra följande kommando:

   ```bash
   scan off
   ```

   ![Stoppa inläsning Närliggande Bluetooth-enheter med bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Initiera en bluetoothanslutning från IoT-gateway till SensorTag

1. Anslut till SensorTag genom att köra följande kommando:

   ```bash
   connect <MAC address>
   ```

   ![Ansluta till SensorTag med bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Koppla från SensorTag och lämna Bluetooth-gränssnittet genom att köra följande kommandon:

   ```bash
   disconnect
   exit
   ```

   ![Koppla från SensorTag med bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Anslutningen mellan SensorTag- och IoT-gateway har aktiverats.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Köra en TIVERA exempelprogrammet SensorTag data ska skickas till din IoT-hubb

Exempelprogrammet Bluetooth låg energiförbrukning (TIVERA) tillhandahålls av Azure IoT kant. Exempelprogrammet samlar in data från TIVERA anslutning och skicka data till IoT hub. Om du vill köra exempelprogrammet måste du:

1. Konfigurera exempelprogrammet.
1. Kör exempelprogrammet i IoT-gateway.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Gå till mappen på exempelprogrammet genom att köra följande kommando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. Öppna konfigurationsfilen genom att köra följande kommando:

   ```bash
   vi ble_gateway.json
   ```

1. Fyll i följande värden i konfigurationsfilen:

   **IoTHubName**: namnet på din IoT-hubb.

   **IoTHubSuffix**: hämta IoTHubSuffix från den primära nyckeln i anslutningssträngen för enheten som du antecknade ned. Se till att du får den primära nyckeln i anslutningssträngen för enheten, inte den primära nyckeln i IoT-hubb anslutningssträngen. Primärnyckeln för anslutningssträngen enheten är i formatet `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transport**: standardvärdet är `amqp`. Det här värdet visar protokollet under transpotation. Det kan vara `http`, `amqp`, eller `mqtt`.

   **macAddress**: MAC-adressen för SensorTag som du antecknade ned.

   **deviceID**: ID för den enhet som du skapade i din IoT-hubb.

   **deviceKey**: primärnyckel för anslutningssträngen för enheten.

   ![Slutföra konfigurationsfilen av TIVERA exempelprogrammet](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Tryck på `ESC` och skriv `:wq` att spara filen.

### <a name="run-the-sample-application"></a>Kör exempelprogrammet

1. Kontrollera att SensorTag är påslagen.
1. Kör följande kommando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Nästa steg

[Använd IoT-gateway för omvandling av sensor data med Azure IoT kant](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
