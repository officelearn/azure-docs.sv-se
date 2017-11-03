---
title: "Använd en fysisk enhet med Azure IoT kant | Microsoft Docs"
description: "Hur du använder en Texas instrument SensorTag-enhet för att skicka data till en IoT-hubb via en IoT-Edge gateway körs på en hallon Pi 3-enhet. Gatewayen skapas med Azure IoT kant."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: andbuc
ms.openlocfilehash: b24828ee1a09ba8e5f657954e11936f124270173
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="forward-device-to-cloud-messages-to-iot-hub-using-azure-iot-edge-on-a-raspberry-pi"></a>Vidarebefordra meddelanden från enhet till moln till IoT-hubb med hjälp av Azure IoT kanten på en hallon Pi

Den här genomgången av den [Bluetooth låg energiförbrukning exempel] [ lnk-ble-samplecode] visar hur du använder [Azure IoT kant] [ lnk-sdk] till:

* Vidarebefordra enhet till moln telemetri till IoT-hubb från en fysisk enhet.
* Väg kommandon från IoT-hubben till en fysisk enhet.

Den här genomgången omfattar:

* **Arkitektur för**: viktig arkitektur information om Bluetooth låg energiförbrukning exemplet.
* **Skapa och kör**: stegen som krävs för att bygga och köra exemplet.

## <a name="architecture"></a>Arkitektur

Den här genomgången visar hur du skapar och kör en IoT-gräns-gatewayen på en hallon Pi 3 som kör Raspbian Linux. Gatewayen skapas med IoT kant. En Texas instrument SensorTag Bluetooth låg energiförbrukning (a)-enhet används för att samla in data om temperatur.

När du kör IoT gräns-gatewayen den:

* Ansluter till en SensorTag-enhet med hjälp av Bluetooth låg energiförbrukning (a)-protokollet.
* Ansluter till IoT-hubb med HTTPS-protokollet.
* Vidarebefordrar telemetri från SensorTag-enhet till IoT-hubb.
* Vägar kommandon från IoT-hubb SensorTag-enheter.

Gatewayen innehåller följande IoT kant moduler:

* En *TIVERA modulen* som gränssnitt med TIVERA enheten ska ta emot temperatur data från enheten och skicka kommandon till enheten.
* En *TIVERA moln till enhet modulen* som omvandlar JSON-meddelanden som skickats från IoT-hubb i TIVERA instruktioner för den *TIVERA modulen*.
* En *loggaren modulen* som loggar alla gateway-meddelanden till en lokal fil.
* En *identitet mappningsmodul* som omvandlar mellan TIVERA enhetens MAC-adresser och identiteter för Azure IoT Hub-enhet.
* En *IoT-hubb modulen* som överför telemetridata till en IoT-hubb och tar emot enhetskommandon från en IoT-hubb.
* En *TIVERA skrivare modulen* som tolkar telemetri från TIVERA enheten och skriver ut formaterade data i konsolen för att aktivera felsökning.

### <a name="how-data-flows-through-the-gateway"></a>Hur data flödar via gatewayen

Blockera följande diagram illustrerar flödet-pipeline telemetri överför data:

![Telemetri överför gateway pipeline](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

De steg som ett objekt av telemetri tar reser från en tabell enhet till IoT-hubben är:

1. Aktivera enheten genererar ett exempel på en temperatur och skickar den via Bluetooth till modulen TIVERA i gatewayen.
1. Modulen TIVERA tar emot exemplet och publicerar till Service broker tillsammans med MAC-adressen till enheten.
1. Mappningsmodul identitet hämtar det här meddelandet och använder en intern tabell för att översätta MAC-adressen till enheten till en IoT-hubb enhetens identitet. En IoT-hubb enhetsidentitet består av en enhets-ID och nyckeln för enheten.
1. Modulen för mappning identitet publicerar ett nytt meddelande som innehåller följande information:
   - Temperatur exempeldata
   - MAC-adressen för enheten
   - Enhets-ID
   - Nyckeln för enheten  
1. IoT-hubb-modulen får ett nytt meddelande (som genererats av modulen för mappning identitet) och publicerar till IoT-hubb.
1. Modulen loggaren loggar alla meddelanden från Service broker till en lokal fil.

Blockera följande diagram illustrerar enheten kommandot data flödet pipeline:

![Enheten kommandot gateway pipeline](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. Modulen IoT-hubb avsöker med jämna mellanrum IoT-hubb för nya kommandomeddelanden.
1. När modulen IoT-hubb tar emot ett nytt kommando-meddelande, publicerar den till Service broker.
1. Modulen för mappning identitet hämtar kommandot meddelandet och använder en intern tabell för att översätta IoT-hubb enhets-ID till en enhet MAC-adress. Den publicerar sedan ett nytt meddelande som innehåller MAC-adressen för målenheten i Egenskaper för kartan i meddelandet.
1. Modulen TIVERA moln till enhet tar upp det här meddelandet och översätter till rätt TIVERA instruktionen för modulen tabell. Den publicerar sedan ett nytt meddelande.
1. Modulen TIVERA hämtar det här meddelandet och utför i/o-instruktion genom att kommunicera med Bell-enheter.
1. Modulen loggaren loggar alla meddelanden från Service broker på en disk.

## <a name="prerequisites"></a>Krav

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [!NOTE]
> Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

Du måste SSH-klienten på den stationära datorn så att du kan fjärransluta till kommandoraden på hallon Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](http://www.putty.org/).
- De flesta distributioner för Linux och Mac OS inkluderar SSH-kommandoradsverktyget. Mer information finns i [SSH med Linux- eller Mac OS x](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

## <a name="prepare-your-hardware"></a>Förbered maskinvaran

Den här kursen förutsätter att du använder en [Texas instrument SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) enheten ansluten till en hallon Pi 3 kör Raspbian.

### <a name="install-raspbian"></a>Installera Raspbian

Du kan använda något av följande alternativ för att installera Raspbian på enheten hallon Pi 3.

* Du kan installera den senaste versionen av Raspbian den [NOOBS] [ lnk-noobs] grafiskt användargränssnitt.
* Manuellt [hämta] [ lnk-raspbian] och skriva den senaste avbildningen av operativsystemet Raspbian till ett SD-kort.

### <a name="sign-in-and-access-the-terminal"></a>Logga in och öppna terminalen

Du har två alternativ för att komma åt en terminal miljö på din hallon Pi:

* Om du har ett tangentbord och bildskärm ansluten till din hallon Pi, kan du använda det grafiska Användargränssnittet Raspbian att komma åt ett terminalfönster.

* Åtkomst till kommandoraden på din hallon Pi använder SSH från den stationära datorn.

#### <a name="use-a-terminal-window-in-the-gui"></a>Använd ett terminalfönster i det grafiska Användargränssnittet

Standardautentiseringsuppgifterna för Raspbian är användarnamn **pi** och lösenord **hallon**. I Aktivitetsfältet i det grafiska Användargränssnittet kan du starta den **Terminal** verktyget med hjälp av ikonen som ser ut som en bildskärm.

#### <a name="sign-in-with-ssh"></a>Logga in med SSH

Du kan använda SSH för kommandoraden åtkomst till din hallon Pi. Artikeln [SSH (Secure Shell)] [ lnk-pi-ssh] beskriver hur du konfigurerar SSH på din hallon Pi och hur du ansluter från [Windows] [ lnk-ssh-windows] eller [ Linux- och Mac OS][lnk-ssh-linux].

Logga in med användarnamn **pi** och lösenord **hallon**.

### <a name="install-bluez-537"></a>Installera BlueZ 5.37

TIVERA moduler prata med Bluetooth maskinvaran via BlueZ stacken. Du behöver version 5.37 av BlueZ för modulerna ska fungera korrekt. Dessa anvisningar se till att rätt version av BlueZ är installerad.

1. Stoppa den aktuella bluetooth-daemonen:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Installera BlueZ beroenden:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf libtool glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Ladda ned källkoden BlueZ från bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Packa upp källkoden:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Ändra sökvägen till den nya mappen:

    ```sh
    cd bluez-5.37
    ```

1. Konfigurera BlueZ koden skapas:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Skapa BlueZ:

    ```sh
    make
    ```

1. Installera BlueZ när det är klart byggnads:

    ```sh
    sudo make install
    ```

1. Ändra systemd tjänstkonfigurationen för bluetooth så att den pekar på den nya bluetooth-daemonen i filen `/lib/systemd/system/bluetooth.service`. Ersätt 'ExecStart' raden med följande text:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Aktivera anslutning till SensorTag-enheter från enheten hallon Pi 3

Du måste verifiera att din hallon Pi 3 kan ansluta till SensorTag-enheten innan du kör exemplet.

1. Se till att den `rfkill` verktyg installeras:

    ```sh
    sudo apt-get install rfkill
    ```

1. Tillåt bluetooth på hallon Pi 3 och kontrollera att versionsnumret är **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Du anger interaktiv bluetooth-gränssnittet genom att starta tjänsten bluetooth och köra den **bluetoothctl** kommando:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Ange kommandot **effekt på** att starta bluetooth-styrenhet. Kommandot returnerar utdata som liknar följande exempel:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. Ange kommandot i interaktiva bluetooth-shell **genomsökning på** att söka efter Bluetooth-enheter. Kommandot returnerar utdata som liknar följande exempel:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Att den SensorTag kan identifieras genom att trycka på knappen liten (grön Indikator bör flash). Hallon Pi 3 ska identifiera SensorTag-enheter:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    I det här exemplet ser du att MAC-adressen för SensorTag-enheten är **A0:E6:F8:B5:F6:00**.

1. Inaktivera genomsökning genom att ange den **genomsökning av** kommando:

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Ansluta till enheten SensorTag med dess MAC-adress genom att ange **ansluta \<MAC-adress\>**. Följande exempel på utdata förkortas för tydlighetens skull:

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > Du kan ange GATT-egenskaperna för enheten igen med den **listan attribut** kommando.

1. Du kan nu koppla från enheten med den **koppla från** kommandot och avsluta sedan från bluetooth shell använder den **avsluta** kommando:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Du är nu redo att köra exemplet TIVERA IoT kanten på din hallon Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Kör IoT kant Bell-exempel

Om du vill köra exemplet IoT kant ort, måste du utföra tre uppgifter:

* Konfigurera två exempel enheter i din IoT-hubb.
* Skapa IoT kanten på enheten hallon Pi 3.
* Konfigurera och köra exemplet TIVERA på enheten hallon Pi 3.

Vid tidpunkten som skrivs stöder IoT kant endast TIVERA moduler i gateways som körs på Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Konfigurera två exempel enheter i din IoT-hubb

* [Skapa en IoT-hubb] [ lnk-create-hub] i din Azure-prenumeration, måste namnet på din hubb för att slutföra den här genomgången. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* Lägg till en enhet som kallas **SensorTag_01** till IoT-hubb och gjort en notering om dess Id och enheten nyckel. Du kan använda den [enheten explorer eller iothub-explorer] [ lnk-explorer-tools] verktyg för att lägga till den här enheten i IoT-hubb som du skapade i föregående steg och hämta dess nyckel. Du kan mappa den här enheten till SensorTag-enheter när du konfigurerar en gateway.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Skapa Azure IoT kanten på din Raspberry Pi 3

Installera beroenden för Azure IoT kant:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev libtool
```

Använd följande kommandon för att klona IoT kant och alla dess submodules till arbetskatalogen:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

När du har en fullständig kopia av databasen IoT kanten på din hallon Pi 3 kan du skapa den med hjälp av följande kommando från mappen som innehåller SDK:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Konfigurera och köra exemplet TIVERA på din hallon Pi 3

För att starta och köra exemplet, konfigurera varje IoT kant-modul som deltar i gatewayen. Den här konfigurationen finns i en JSON-fil och du måste konfigurera alla fem deltagande IoT kant moduler. Det finns ett exempel JSON-fil i databasen kallas **gateway\_sample.json** att du kan använda som utgångspunkt för att skapa egna konfigurationsfilen. Den här filen finns i den **src-exempel/ble_gateway** mapp i lokal kopia av databasen IoT kant.

I följande avsnitt beskrivs hur du redigerar den här konfigurationsfilen för tabell. Vi förutsätter att IoT kant-databasen finns i den **/home/pi/iot-edge /** mapp på din hallon Pi 3. Om databasen finns på en annan plats, justera sökvägarna i enlighet med detta.

#### <a name="logger-configuration"></a>Loggaren konfiguration

Under förutsättning att gateway-databasen finns i den **/home/pi/iot-edge /** mapp, konfigurera modulen loggaren på följande sätt:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>TIVERA Modulkonfiguration

Exempelkonfiguration för enheten TIVERA förutsätter en Texas instrument SensorTag-enhet. Alla standard Bell-enheter som kan fungera som en kringutrustning GATT bör fungera, men du kan behöva uppdatera GATT karakteristiken-ID: N och data. Lägg till MAC-adressen för SensorTag-enheter:

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

Om du inte använder en SensorTag-enhet, granska dokumentationen för enheten tabell för att avgöra om du behöver uppdatera GATT egenskaps-ID: N och datavärden.

#### <a name="iot-hub-module"></a>Modul för IoT-hubb

Lägg till namnet på din IoT-hubb. Suffixvärdet för är vanligtvis **azure devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Identitet mappning konfiguration

Lägg till MAC-adressen till enheten SensorTag och enhets-ID och nyckeln för den **SensorTag_01** enhet som du har lagt till din IoT-hubb:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Modulkonfiguration TIVERA skrivare

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>BLEC2D konfiguration

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Routningskonfiguration

Följande konfiguration ser följande routning mellan IoT kant moduler:

* Den **loggaren** modulen tar emot och loggar alla meddelanden.
* Den **SensorTag** modulen skickar meddelanden till både den **mappning** och **TIVERA skrivare** moduler.
* Den **mappning** modulen skickar meddelanden till den **IoTHub** modulen skickas till din IoT-hubb.
* Den **IoTHub** modulen skickar tillbaka till den **mappning** modul.
* Den **mappning** modulen skickar meddelanden till den **BLEC2D** modul.
* Den **BLEC2D** modulen skickar tillbaka till den **Sensor Tag** modul.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Om du vill köra exemplet skickar du sökvägen till JSON-konfigurationsfil som en parameter för den **tivera\_gateway** binär. Följande kommando förutsätter att du använder den **gateway_sample.json** konfigurationsfilen. Kör kommandot från den **iot-edge** mapp på hallon Pi:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Du kan behöva trycka på knappen små på SensorTag-enhet för att göra den synlig innan du kör exemplet.

När du kör exemplet, kan du använda den [enheten explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) eller [iothub explorer](https://github.com/Azure/iothub-explorer) verktyg för att övervaka de meddelanden som IoT gräns-gatewayen vidarebefordrar från SensorTag-enheter. Till exempel kan Utforskaren iothub du övervaka meddelanden från enhet till moln med följande kommando:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Modulen TIVERA stöder också skicka kommandon från IoT-hubb till enheten. Du kan använda den [enheten explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) eller [iothub explorer](https://github.com/Azure/iothub-explorer) verktyg för att skicka JSON-meddelanden som gateway-modulen TIVERA vidarebefordrar in Bell-enheter.

Om du använder Texas instrument SensorTag-enheter kan aktivera du den röda Indikator, grön Indikator eller Summer genom att skicka kommandon från IoT-hubb. Innan du kan skicka kommandon från IoT-hubb, måste du först skicka följande JSON meddelanden i ordning. Du kan sedan skicka något av kommandona för att aktivera ljus eller Summer.

1. Återställa alla indikatorer och Summer (inaktivera dem):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Konfigurera i/o som 'fjärr':

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Nu kan du skicka något av följande kommandon för att aktivera ljus eller Summer på SensorTag-enheter:

* Aktivera röd Indikator:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Aktivera grön Indikator:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Aktivera Summer:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Nästa steg

Om du vill få större kunskap om IoT kant och experimentera med vissa kodexempel finns följande kurser för utvecklare och resurser:

* [Azure IoT kant][lnk-sdk]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
