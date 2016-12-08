---
title: "Komma igång med IoT Hub Gateway SDK | Microsoft Docs"
description: "I den här genomgången av Azure IoT Gateway SDK används Linux för att illustrera viktiga begrepp som du bör känna till när du använder Azure IoT Gateway SDK."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: a76320718f0cefa015728cb79df944e0d34bbf74
ms.openlocfilehash: cbb909adc2d29f9b80a4c97d06176fe74b64a75a


---
# <a name="azure-iot-gateway-sdk---get-started-using-linux"></a>Azure IoT Gateway SDK – Komma igång med Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Hur du skapar provet
Innan du börjar, måste du [ställa in din utvecklingsmiljö][lnk-setupdevbox] för att arbeta med SDK i Linux.

1. Öppna ett gränssnitt.
2. Navigera till rotmappen i den lokala kopian av databasen **azure-iot-gateway-sdk**.
3. Kör skriptet **tools/build.sh**. Det här skriptet använder verktyget **cmake** för att skapa en mapp som kallas **build** i rotmappen på den lokala kopian av databasen **azure-iot-gateway-sdk** och generera en make-fil. Skriptet bygger sedan lösningen och kör testerna.

> [!NOTE]
> Varje gång du kör skriptet **build.sh** tar det bort och återskapar sedan mappen **build** i rotmappen på den lokala kopian av databasen **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Köra exemplet
1. Skriptet **build.sh** genererar sina utdata i mappen **build** i din lokala kopia av databasen **azure-iot-gateway-sdk**. Detta omfattar de två moduler som används i det här exemplet.
   
    Build-skriptet placerar **liblogger.so** i mappen **build/modules/logger/** och **libhello_world.so** i mappen **build/modules/hello_world/**. Använd dessa sökvägar för **modulsökvägens** värde som visas i JSON-inställningsfilen nedan.
2. Hello_world_sample-processen använder sökvägen till en JSON-konfigurationsfil som ett argument på kommandoraden. En JSON-exempelfil tillhandahålls i databasen på **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** och kopieras nedan. Den fungerar som den är såvida du inte har ändrat build-skriptet och placerat moduler eller körbara exempelfiler på andra platser än standardplatserna.

   > [!NOTE]
   > Modulernas sökvägar är relativa till den aktuella arbetskatalogen som den körbara filen hello_world_sample startas från, inte katalogen som den körbara filen finns i. JSON-exempelkonfigurationsfilen skriver som standard ”log.txt” i din aktuella arbetskatalog.
   
    ```
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links": 
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
3. Gå till mappen **azure-iot-gateway-sdk**.
4. Kör följande kommando:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO4-->


