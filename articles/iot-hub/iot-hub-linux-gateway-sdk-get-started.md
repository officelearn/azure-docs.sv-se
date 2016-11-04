---
title: Komma igång med IoT Hub Gateway SDK | Microsoft Docs
description: Den här genomgången av Azure IoT Hub Gateway SDK använder Linux för att illustrera viktiga begrepp som du bör känna till när du använder Azure IoT Hub Gateway SDK.
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: andbuc

---
# IoT Gateway SDK (beta) – komma igång med Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Hur du skapar provet
Innan du börjar, måste du [ställa in din utvecklingsmiljö][lnk-setupdevbox] för att arbeta med SDK i Linux.

1. Öppna ett gränssnitt.
2. Navigera till rotmappen i den lokala kopian av databasen **azure-iot-gateway-sdk**.
3. Kör skriptet **tools/build.sh**. Det här skriptet använder verktyget **cmake** för att skapa en mapp som kallas **build** i rotmappen på den lokala kopian av databasen **azure-iot-gateway-sdk** och generera en make-fil. Skriptet bygger sedan lösningen och kör testerna.

> [!NOTE]
> Varje gång du kör skriptet **build.sh** tar det bort och återskapar sedan mappen **build** i rotmappen på den lokala kopian av databasen **azure-iot-gateway-sdk**.
> 
> 

## Köra exemplet
1. Skriptet **build.sh** genererar sina utdata i mappen **build** i din lokala kopia av databasen **azure-iot-gateway-sdk**. Detta omfattar de två moduler som används i det här exemplet.
   
    Build-skriptet placerar **liblogger_hl.so** i mappen **build/modules/logger/** och **libhello_world_hl.so** i mappen **build/modules/hello_world/**. Använd dessa sökvägar för **modulsökvägens** värde som visas i JSON-inställningsfilen nedan.
2. Filen **hello_world_lin.json** i mappen **src-prover per hello_world** är ett exempel på en JSON-inställningsfil för Linux som du kan använda för att köra exemplet. De exempel på JSON-inställningar som visas nedan förutsätter att du kör exemplet från roten på din lokala kopia av **azure-iot-gateway-sdk**-databasen.
3. För modulen **logger_hl** i avsnittet **args** anger du värdet för **filnamn** till namn och sökväg för den fil som kommer att innehålla loggdata.
   
    Det här är ett exempel på en JSON-inställningsfil för Linux som skriver till **log.txt** till den mapp där du kör exemplet.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. I ditt gränssnitt navigerar du till mappen **azure-iot-gateway-sdk**.
5. Kör följande kommando:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Oct16_HO1-->


