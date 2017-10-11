---
title: "Datakonvertering på IoT-gateway med Azure IoT kant | Microsoft Docs"
description: "Använd IoT-gateway för att konvertera formatet sensordata via en anpassad modul från Azure IoT kant."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IOT-gateway datakonvertering, DTS för iot-gateway"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Använd IoT-gateway för omvandling av sensor data med Azure IoT kant

> [!NOTE]
> Innan du börjar den här självstudiekursen, kontrollera att du har slutfört följande erfarenheter i följd:
> * [Konfigurera Intel NUC som en IoT-gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Använd IoT-gateway för att ansluta saker till molnet - SensorTag till Azure IoT-hubb](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

En syftar Iot-gateway till att bearbeta insamlade data innan du skickar den till molnet. Azure IoT-Edge introducerar moduler som kan skapas och monteras så att arbetsflödet för databearbetning. En modul tar emot ett meddelande, utför en åtgärd på den och flytta den på för andra moduler för att bearbeta.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar en modul för att konvertera meddelanden från SensorTag till ett annat format.

## <a name="what-you-do"></a>Vad du gör

* Skapa en modul för att konvertera ett mottaget meddelande till JSON-format.
* Kompilera modulen.
* Lägg till modulen exempelprogrammet tabell från Azure IoT kant.
* Kör exempelprogrammet.

## <a name="what-you-need"></a>Vad du behöver

* Följande kurser slutförts i ordning:
  * [Konfigurera Intel NUC som en IoT-gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Använd IoT-gateway för att ansluta saker till molnet - SensorTag till Azure IoT-hubb](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* En SSH-klient som körs på värddatorn. PuTTY rekommenderas i Windows. Linux- och macOS har redan en SSH-klient.
* IP-adressen och användarnamn och lösenord för åtkomst till gatewayen från SSH-klienten.
* En Internetanslutning.

## <a name="create-a-module"></a>Skapa en modul

1. Kör SSH-klienten på värddatorn och ansluta till IoT-gateway.
1. Klona källfiler för modulen konvertering från GitHub till arbetskatalogen för IoT-gateway genom att köra följande kommandon:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Det här är en inbyggd Azure Edge-modul som skrivits i programmeringsspråket C. Modulen konverterar formatet för mottagna meddelanden till följande:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>Kompilera modulen

Kompilera modulen genom att köra följande kommandon:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

Du får en `libmy_module.so` filen när kompileringen har slutförts. Anteckna den absoluta sökvägen till den här filen.

## <a name="add-the-module-to-the-ble-sample-application"></a>Lägga till modulen TIVERA exempelprogrammet

1. Gå till mappen exempel genom att köra följande kommando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Öppna konfigurationsfilen genom att köra följande kommando:

   ```bash
   vi ble_gateway.json
   ```

1. Lägga till en modul genom att lägga till följande kod i `modules` avsnitt.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Ersätt `[Your libmy_module.so path]` i koden med den absoluta sökvägen till libmy_module.so-fil.
1. Ersätt Koden i den `links` avsnitt med följande:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Tryck på `ESC`, och skriv sedan `:wq` att spara filen.

## <a name="run-the-sample-application"></a>Kör exempelprogrammet

1. Starta SensorTag.
1. Ange miljövariabeln SSL_CERT_FILE genom att köra följande kommando:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Kör exempelprogrammet med modulen lagts till genom att köra följande kommando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Nästa steg

Du har kunna använda IoT-gateway för att konvertera meddelandet från SensorTag till JSON-format.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
