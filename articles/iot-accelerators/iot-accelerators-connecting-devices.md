---
title: Etablera Windows-enheter till fjärrövervakning i C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som skrivits i C som körs på Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: cc2b813efe31822b273a98d21d0dcf0b62385b92
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734309"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Anslut enheten till lösningsacceleratorn för fjärrövervakning (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en fysisk enhet till lösningsacceleratorn för fjärrövervakning.

Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhet-programmet skrivna i C. I den här självstudien skapar du enheten-klientprogrammet på en dator som kör Windows.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här guiden följer du stegen i [konfigurera din Windows-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) att lägga till de nödvändiga utvecklingsverktygen och -bibliotek i din Windows-dator.

## <a name="view-the-code"></a>Visa koden

Den [exempelkoden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring) används i den här guiden är tillgänglig i Azure IoT C SDK: er GitHub-lagringsplatsen.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ned källkoden och Förbered projektet

För att förbereda projektet, klona eller ladda ned den [databasen Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) från GitHub.

Du hittar exemplet i den **samples/lösningar/remote_monitoring** mapp.

Öppna den **remote_monitoring.c** fil i den **samples/lösningar/remote_monitoring** mapp i en textredigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Redigera den **remote_monitoring.c** filen för att ersätta `<connectionstring>` med enhetens anslutningssträng som du antecknade i början av den här guiden när du har lagt till en enhet för solution accelerator.

1. Följ stegen i [skapa C SDK i Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) att bygga SDK: N och fjärransluten övervakning klientprogrammet.

1. Vid kommandotolken använde du för att skapa lösningen genom att köra:

    ```cmd
    samples\solutions\remote_monitoring\Release\remote_monitoring_client.exe
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempel telemetri till solution accelerator.
    - Svarar på metoderna som anropas från lösningens instrumentpanel.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
