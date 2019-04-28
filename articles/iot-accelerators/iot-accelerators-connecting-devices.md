---
title: Etablera Windows-enheter till fjärrövervakning i C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som skrivits i C som körs på Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450231"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Anslut enheten till lösningsacceleratorn för fjärrövervakning (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en riktig enhet till lösningsacceleratorn för fjärrövervakning.

Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhet-programmet skrivna i C. I den här självstudien skapar du enheten-klientprogrammet på en dator som kör Windows.

Om du föredrar att simulera en enhet, se [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen i den här guiden följer du stegen i [konfigurera din Windows-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) att lägga till de nödvändiga utvecklingsverktygen och -bibliotek i din Windows-dator.

## <a name="view-the-code"></a>Visa koden

Den [exempelkoden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) används i den här guiden är tillgänglig i Azure IoT C SDK: er GitHub-lagringsplatsen.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ned källkoden och Förbered projektet

Förbereda projektet, [klona databasen för Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) från GitHub.

Du hittar exemplet i den **samples/lösningar/remote_monitoring_client** mapp.

Öppna den **remote_monitoring.c** fil i den **samples/lösningar/remote_monitoring_client** mapp i en textredigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Redigera den **remote_monitoring.c** filen för att ersätta `<connectionstring>` med enhetens anslutningssträng som du antecknade i början av den här guiden när du har lagt till en enhet för solution accelerator.

1. Följ stegen i [skapa C SDK i Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) att bygga SDK: N och fjärransluten övervakning klientprogrammet.

1. Vid kommandotolken använde du för att skapa lösningen genom att köra:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempel telemetri till solution accelerator.
    - Svarar på metoderna som anropas från lösningens instrumentpanel.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
