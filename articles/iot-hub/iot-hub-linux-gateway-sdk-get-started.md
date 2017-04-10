---
title: "Kom igång med Azure IoT Gateway SDK (Linux) | Microsoft Docs"
description: "Så här bygger du en gateway på en Linux-dator och lär dig om viktiga begrepp i SDK för Azure IoT Gateway, som moduler och JSON-konfigurationsfiler."
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
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 856ffeeeb8f9d8296ba972a9e070686171f7fde8
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>Utforska IoT Gateway SDK-arkitekturen i Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Hur du skapar provet

Innan du börjar måste du [ställa in din utvecklingsmiljö][lnk-setupdevbox] för att arbeta med SDK i Linux.

1. Öppna ett gränssnitt.
1. Navigera till rotmappen i den lokala kopian av databasen **azure-iot-gateway-sdk**.
1. Kör skriptet **tools/build.sh**. Det här skriptet använder verktyget **cmake** för att skapa en mapp som kallas **build** i rotmappen på den lokala kopian av databasen **azure-iot-gateway-sdk** och generera en make-fil. Skriptet bygger sedan lösningen och hoppar över enhetstester och slutpunkt till slutpunkt-tester. Lägg till parametern **--run-unittests** om du vill skapa och köra enhetstesterna. Lägg till **--run-e2e-tests** om du vill skapa och köra slutpunkt till slutpunkt-testerna.

> [!NOTE]
> Varje gång du kör skriptet **build.sh** tar det bort och återskapar sedan mappen **build** i rotmappen på den lokala kopian av databasen **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Köra exemplet

1. Skriptet **build.sh** genererar sina utdata i mappen **build** i din lokala kopia av databasen **azure-iot-gateway-sdk**. Dessa utdata omfattar de två moduler som används i det här exemplet.

    Build-skriptet placerar **liblogger.so** i mappen **build/modules/logger/** och **libhello\_world.so** i mappen **build/modules/hello_world/**. Använd dessa sökvägar för värdet **modulsökväg** som visas i följande exempel med en JSON-inställningsfil.
1. Hello\_world\_sample-processen använder sökvägen till en JSON-konfigurationsfil som ett argument på kommandoraden. I följande exempel anges JSON-filen i SDK-databasen under **samples/hello\_world/src/hello\_world\_lin.json**. Den här konfigurationsfilen fungerar som den är såvida du inte har ändrat build-skriptet och placerat moduler eller körbara exempelfiler på andra platser än standardplatserna.

   > [!NOTE]
   > Modulernas sökvägar är relativa till den aktuella arbetskatalog som den körbara hello\_world\_sample-filen startas från, inte katalogen som den körbara filen finns i. JSON-exempelkonfigurationsfilen skriver som standard ”log.txt” i din aktuella arbetskatalog.

    ```json
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
1. Gå till mappen **build**.
1. Kör följande kommando:

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

