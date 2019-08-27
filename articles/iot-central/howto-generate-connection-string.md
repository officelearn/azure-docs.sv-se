---
title: Skapa en enhets anslutnings sträng för Azure IoT Central | Microsoft Docs
description: Hur skapar jag en anslutnings sträng för enheten som behöver ansluta till ett IoT Central-program som en enhets utvecklare?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019781"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Skapa en anslutnings sträng för enheten för att ansluta till ett Azure IoT Central-program

Den här artikeln beskriver hur, som en enhets utvecklare, för att skapa en anslutnings sträng för enheten som måste ansluta till ett IoT Central-program. Proceduren som beskrivs i den här artikeln visar hur du snabbt ansluter en enskild enhet med hjälp av en signatur för delad åtkomst (SAS). Den här metoden är användbar när du experimenterar med IoT Central eller testning av enheter. Alternativa metoder för att använda i en produktions miljö finns i [enhets anslutning i Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

- Ett Azure IoT Central-program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
- En utvecklings dator med [Node. js](https://nodejs.org/) version 8.0.0 eller senare installerad. Du kan köra `node --version` på kommando raden för att kontrol lera din version. Node.js är tillgängligt för många olika operativsystem.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Följande steg beskriver hur du hämtar den information som du behöver för att skapa en SAS-anslutningssträng för en enhet:

1. Leta upp den enhet som du vill ansluta till ditt program i **Device Explorer**:

    ![Välj en riktig enhet](media/howto-generate-connection-string/real-devices.png)

1. På sidan **enhet** väljer du **Anslut**:

    ![Välj Anslut](media/howto-generate-connection-string/connect.png)

1. Anteckna anslutnings information, **omfattnings-ID**, enhets **-ID**och **primär nyckel för enheten**för att använda i följande steg:

    ![Anslutningsinformation](media/howto-generate-connection-string/device-connect.png)

    Du kan kopiera värdena från den här sidan för att spara.

## <a name="generate-the-connection-string"></a>Generera anslutnings strängen

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en anslutnings sträng för en riktig enhet för att ansluta till ditt Azure IoT Central-program, så är det förslag på nästa steg:

* [Förbereda och ansluta en DevKit-enhet (C)](howto-connect-devkit.md)
* [Förbereda och ansluta en Raspberry Pi (python)](howto-connect-raspberry-pi-python.md)
* [Förbereda och ansluta en Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Förbereda och ansluta en Windows 10 IoT Core-enhetC#()](howto-connect-windowsiotcore.md)
* [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)