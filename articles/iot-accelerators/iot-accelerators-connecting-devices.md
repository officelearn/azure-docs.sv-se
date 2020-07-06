---
title: Etablera Windows-enheter till fjärrövervakning i C-Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till lösningen för Fjärrövervaknings lösning med hjälp av ett program som är skrivet i C som körs i Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "61450231"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Anslut enheten till den fjärrstyrda lösningen för övervakning av lösningar (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du se hur du ansluter en riktig enhet till en lösnings Accelerator med fjärr styrning.

Precis som med de flesta inbäddade program som körs på begränsade enheter, skrivs klient koden för enhets programmet i C. I den här självstudien skapar du enhets klient programmet på en dator som kör Windows.

Om du föredrar att simulera en enhet kan du läsa [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden följer du stegen i [Konfigurera din Windows Development-miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) för att lägga till nödvändiga utvecklingsverktyg och bibliotek på din Windows-dator.

## <a name="view-the-code"></a>Visa koden

[Exempel koden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) som används i den här guiden finns i GitHub-lagringsplatsen för Azure IoT C SDK: er.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ned käll koden och Förbered projektet

Förbered projektet genom att [klona SDK-lagringsplatsen för Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) från GitHub.

Exemplet finns i mappen **samples/Solutions/remote_monitoring_client** .

Öppna filen **remote_monitoring. c** i mappen **samples/Solutions/remote_monitoring_client** i en text redigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Redigera filen **remote_monitoring. c** och Ersätt `<connectionstring>` med enhets anslutnings strängen som du antecknade i början av den här instruktions guiden när du lade till en enhet i Solution Accelerator.

1. Följ stegen i [bygga C SDK i Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) för att bygga klient programmet för SDK och fjärr övervakning.

1. I kommando tolken som du använde för att skapa lösningen kör du:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempel telemetri till Solution Accelerator.
    - Svarar på metoder som anropas från instrument panelen för lösningen.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
