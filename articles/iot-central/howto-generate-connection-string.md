---
title: Generera en enhetens anslutningssträng för Azure IoT Central | Microsoft Docs
description: Som utvecklare av enheten, hur jag skapa en anslutningssträng för enhet som behöver ansluta till ett program med IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426740"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Generera en anslutningssträng för enheten för att ansluta till ett program med Azure IoT Central

Den här artikeln beskrivs hur du som utvecklare enheten att generera en anslutningssträng för enhet som behöver ansluta till ett IoT Central-program. Proceduren som beskrivs i den här artikeln visar hur du snabbt ansluta en enskild enhet med hjälp av en signatur för delad åtkomst (SAS). Den här metoden är användbar när du experimentera med IoT Central eller testa enheter. Alternativa metoder för att använda i en produktionsmiljö, se [enhetsanslutning i Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
1. En utvecklingsdator med [Node.js](https://nodejs.org/) version 8.0.0 eller senare installerat. Du kan köra `node --version` i kommandoraden för att kontrollera vilken version. Node.js är tillgängligt för många olika operativsystem.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Följande steg beskriver hur du all information du behöver att generera en SAS-anslutningssträng för en enhet:

1. I den **Explorer**, hitta riktig enhet som du vill ansluta till ditt program:

    ![Välj en riktig enhet](media/howto-generate-connection-string/real-devices.png)

1. På den **enhet** väljer **Connect**:

    ![Välj ansluta](media/howto-generate-connection-string/connect.png)

1. Anteckna anslutningsinformation **Scopeid**, **enhets-ID**, och **enheten primärnyckel**ska gå att använda i följande steg:

    ![Anslutningsinformation](media/howto-generate-connection-string/device-connect.png)

    Du kan kopiera värdena från den här sidan för att spara.

## <a name="generate-the-connection-string"></a>Generera anslutningssträngen

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har genererat en anslutningssträng för en riktig enhet att ansluta till Azure IoT Central programmet, är här nästa föreslagna steg:

* [Förbereda och ansluta en DevKit enhet (C)](howto-connect-devkit.md)
* [Förbereda och ansluta Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Förbereda och ansluta Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Förbereda och Anslut en Windows 10 IoT core-enhet (C#)](howto-connect-windowsiotcore.md)
* [Ansluta en allmän Node.js-klient till Azure IoT Central programmet](howto-connect-nodejs.md)