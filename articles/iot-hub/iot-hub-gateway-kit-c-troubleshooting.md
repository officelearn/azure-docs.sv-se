---
title: "SensorTag enhet & Azure IoT-Gateway - felsökning | Microsoft Docs"
description: "Felsökning för Intel NUC gateway"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: IOT-problem, internet saker problem
ROBOTS: NOINDEX
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 7e80951de55ade6b5140608dcff8ebb064f942ca
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshooting"></a>Felsökning

## <a name="hardware-issues"></a>Problem med maskinvara

### <a name="ti-sensortag-cannot-be-connected"></a>Det går inte att ansluta TI SensorTag

Använd felsökning av problem med nätverksanslutningen SensorTag den [SensorTag app](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Har ett problem med Intel NUC

Felsökning av startproblem med avser [Felsökning nr Start på Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Felsökning av problem med operativsystemet avser [felsökning av problem med operativsystemet på Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Felsökning av problem med andra avser [blinkar och signal koder för Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html).

## <a name="nodejs-package-issues"></a>Problem med node.js-paket

### <a name="no-response-during-gulp-tasks"></a>Inget svar under gulp uppgifter

Om du stöter på problem i gulp aktiviteter som körs, kan du lägga till den `--verbose` alternativet för felsökning. Försöker avsluta aktuella gulp uppgifter med hjälp av `Ctrl + C`, och kör sedan följande kommando i din konsolfönster Se felsökningsmeddelanden. Du kan visa detaljerade felmeddelanden i konsolens utdata.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problem vid identifiering av enheter

Felsökning av vanliga problem med den `discover-sensortag` kommando, kontrollera den [wiki-sida](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>npm-problem

Försök att uppdatera npm-paketet genom att köra följande kommando:

```bash
npm install -g npm
```

Om problemet kvarstår lämna kommentarer i slutet av den här artikeln eller skapa ett GitHub-problem i våra [exempel databasen](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Fjärrfelsökning
> Nedan instruktioner är avsedda för att felsöka node.js-skript som används i den här kursen.
### <a name="run-the-sample-application-in-debug-mode"></a>Kör exempelprogrammet i felsökningsläge

Kör exempelprogrammet i felsökningsläge genom att köra följande kommando:

```bash
gulp run --debug
```

När debug-motorn är klar, bör du se `Debugger listening on port 5858` i konsolens utdata.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Konfigurera Visual Studio-koden för att ansluta till den fjärranslutna enheten

1. Öppna den **felsöka** panelen till vänster.
2. Klicka på gröna **Start Debugging** (F5)-knappen. Visual Studio Code öppnar en `launch.json` fil.
3. Uppdatera den `launch.json` filen med följande innehåll. Ersätt `[device hostname or IP address]` med det faktiska IP-adress eller värdnamnet enhetsnamnet.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Fjärrkonfiguration felsökning](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Anslut till fjärrprogrammet

Klicka på gröna **Start Debugging** (F5) för att starta felsökning.

Läs [JavaScript i VS kod](https://code.visualstudio.com/docs/languages/javascript#_debugging) för mer information om felsökning.

![Felsökning Bell-exempel](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Azure CLI-problem

Azure-kommandoradsgränssnittet (Azure CLI) är en förhandsversionen. Om du vill söka efter lösningar du kan använda den [Preview installera guiden](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Om det uppstår några fel med verktyget filen ett [problemet](https://github.com/Azure/azure-cli/issues) i den **problem** avsnitt i GitHub-lagringsplatsen.

Hjälp vid felsökning av vanliga problem, kontrollera den [viktigt](https://github.com/Azure/azure-cli/blob/master/README.rst).

Kör följande kommando för att uppgradera pip till senaste versionen om du uppfyller ”det gick inte att hitta en version som uppfyller krav”.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problem med installation av Python

### <a name="legacy-installation-issues-macos"></a>Äldre installationsproblem (macOS)

När du installerar pip, en behörighet felmeddelande när äldre paket installeras med **su** behörigheter. Detta inträffar eftersom en tidigare installation av Python med brew (macOS) inte har avinstallerats helt. Vissa pip-paket från en tidigare installation skapades rot, som orsakar felet behörighet. Lösningen är att ta bort de paket som installerats av roten. Använd följande steg för att slutföra den här aktiviteten:

1. Gå till `/usr/local/lib/python2.7/site-packages`
2. Lista över paket som skapats av roten:`ls -l | grep root`
3. Avinstallera paket från steg 2:`sudo rm -rf {package name}`
4. Installera om Python.

## <a name="azure-iot-hub-issues"></a>Azure IoT-hubb-problem

Om din Azure IoT-hubb med Azure CLI har etablerats och du behöver ett verktyg för att hantera enheter som ansluter till din IoT-hubb, kan du prova följande verktyg.

### <a name="device-explorer"></a>Enheten Explorer

[Enheten Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) körs på den lokala Windows-datorn och ansluter till din IoT-hubb i Azure. Den kommunicerar med följande [IoT-hubbslutpunkter](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- Identitetshantering för enheten för att etablera och hantera enheter som registrerats med din IoT-hubb.
- Ta emot enhet till moln så att du kan övervaka meddelanden som skickas från enheten till din IoT-hubb.
- Skicka moln till enhet så att du kan skicka meddelanden till dina enheter från din IoT-hubb.

Konfigurera anslutningssträngen IoT-hubb i det här verktyget och använda dess funktioner.

### <a name="iothub-explorer"></a>iothub explorer

[iothub explorer](https://github.com/Azure/iothub-explorer) är ett exempel flera plattformar CLI verktyg för att hantera klienter. Du kan använda verktyget för att hantera enheter i identitetsregistret, övervaka meddelanden från enhet till moln och skicka kommandon moln till enhet.

Installera den senaste (förhandsversion) versionen av verktyget iothub explorer genom att köra följande kommando:

```bash
npm install -g iothub-explorer@latest
```

För ytterligare information om alla iothub explorer kommandon och deras parametrar, kör du följande kommando:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Azure-portalen

En fullständig CLI-upplevelse hjälper dig att skapa och hantera alla dina Azure-resurser. Du kanske också vill använda den [Azure-portalen](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) för att etablera, hantera och felsöka Azure-resurser.

## <a name="azure-storage-issues"></a>Azure Storage-problem

[Microsoft Azure Lagringsutforskaren (förhandsversion)](http://storageexplorer.com/) är en fristående app från Microsoft som du kan använda för att arbeta med Azure Storage data på Windows och macOS Linux. Med det här verktyget kan du ansluta till din tabell och visa data i den. Du kan använda det här verktyget för att felsöka problem med ditt Azure Storage.
