---
title: "Kom igång med Azure IoT kant (Windows) | Microsoft Docs"
description: "Hur du skapar ett Azure IoT gräns-gatewayen på en Windows-dator och lär dig mer om nyckelbegrepp i Azure IoT kant som JSON-konfigurationsfiler och moduler."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Utforska Azure IoT kant arkitektur i Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Köra exemplet

Den **build.cmd** skriptet genererar utdata i den **skapa** mapp i den lokala kopian av den **iot-edge** databasen. Här ingår många filer, men det här exemplet fokuserar på tre:
- Två IoT kant moduler: **skapa\\moduler\\loggaren\\felsöka\\logger.dll** och **skapa\\moduler\\hello_world\\ Felsöka\\hello\_world.dll**. 
- En körbar fil: **skapa\\exempel\\hello\_world\\felsöka\\hello\_world\_sample.exe**. Den här processen tar en JSON-konfigurationsfil som kommandoradsargument.

Den fjärde filen som du använder i det här exemplet är inte i build-mappen, men ingick när du har klonat iot-edge-databasen:
- En JSON-konfigurationsfil: **exempel\\hello\_world\\src\\hello\_world\_win.json**. Den här filen innehåller sökvägar till de två modulerna. Det deklarerar där logger.dll skrivs utdata till. Standardvärdet är **log.txt** i den aktuella arbetskatalogen. 

   >[!NOTE]
   >Om du flyttar modulerna som exempel eller lägga till egna för testning, uppdatera det **module.path** värden i konfigurationsfilen för att matcha. Modul-sökvägar är i förhållande till katalogen där **hello\_world\_sample.exe** finns. 

Följ dessa steg om du vill köra exemplet:

1. Navigera till den **skapa** mapp i roten av den lokala kopian av den **iot-edge** databasen.

1. Kör följande kommando:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. Följande utdata innebär att provet körts:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Tryck på den **RETUR** för att stoppa processen.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
