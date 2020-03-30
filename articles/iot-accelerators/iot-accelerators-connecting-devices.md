---
title: Etablera Windows-enheter för fjärrövervakning i C - Azure | Microsoft-dokument
description: I artikeln beskrivs hur du ansluter en enhet till lösningsacceleratorn för fjärrövervakning med hjälp av ett program som är skrivet i C som körs i Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450231"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Ansluta enheten till lösningsacceleratorn för fjärrövervakning (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en riktig enhet till lösningsacceleratorn för fjärrövervakning.

Som med de flesta inbäddade program som körs på begränsade enheter skrivs klientkoden för enhetsprogrammet i C. I den här självstudien skapar du enhetsklientprogrammet på en dator som kör Windows.

Om du föredrar att simulera en enhet läser du [Skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Krav

Så här utför du stegen i den här guiden genom att [konfigurera windows-utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) för att lägga till de utvecklingsverktyg och bibliotek som krävs på din Windows-dator.

## <a name="view-the-code"></a>Visa koden

[Exempelkoden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) som används i den här guiden är tillgänglig i Azure IoT C SDKs GitHub-databasen.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ner källkoden och förbered projektet

Om du vill förbereda projektet [klonar du Azure IoT C SDKs-databasen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) från GitHub.

Exemplet finns i mappen **samples/solutions/remote_monitoring_client.**

Öppna **filen remote_monitoring.c** i mappen **samples/solutions/remote_monitoring_client** i en textredigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Redigera **filen remote_monitoring.c** för att `<connectionstring>` ersätta med enhetsanslutningssträngen som du noterade i början av den här programvägledningen när du lade till en enhet i lösningsacceleratorn.

1. Följ stegen i [Skapa C SDK i Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) för att skapa SDK och fjärrövervakning klientprogrammet.

1. Kör i kommandotolken som du använde för att skapa lösningen:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempeltelemetri till lösningsacceleratorn.
    - Svarar på metoder som anropas från lösningsinstrumentpanelen.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
