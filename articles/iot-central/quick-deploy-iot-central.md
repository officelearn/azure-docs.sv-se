---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program som ska hantera kylande varuautomater. Visa telemetridata som genereras från dina simulerade enheter.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af06766d89804b2f3d0aaf061494fb836f6ec262
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465613"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera ditt Microsoft Azure IoT Central-program. I den här snabbstarten får du veta hur du:

- Skapa ett Azure IoT Central-program som innehåller ett exempel på en _enhetsmall_ och simulerade _enheter_.
- Visa funktionerna i enhetsmallen **Kylande varuautomat** i ditt program.
- Visa telemetri och analys från dina simulerade **kylskåp**.

I den här snabbstarten ser du ett simulerat **kylskåp** från en enhetsmall. Den simulerade enheten:

* Skickar telemetri, till exempel temperatur och tryck, till ditt program.
* Rapporterar enhetens egenskapsvärden, till exempel en rörelseavisering, till ditt program.
* Innehåller enhetsinställningar, till exempel fläkthastighet, som du kan ställa in i programmet.

När du skapar en simulerad enhet från en enhetsmall i Azure IoT Central-programmet, kan du med den simulerade enheten testa programmet innan du ansluter en riktig enhet.

## <a name="create-the-application"></a>Skapa programmet

För att kunna slutföra den här snabbstarten måste du skapa ett Azure IoT Central-program från programmallen **Contoso-exempel**.

Gå till sidan [Application Manager](https://aka.ms/iotcentral) (Programhanterare) i Azure IoT Central. Ange sedan den e-postadress och det lösenord som du använder för att få åtkomst till din Azure-prenumeration:

![Ange ditt organisationskonto](media/quick-deploy-iot-central/sign-in.png)

Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**:

![Sidan Application Manager (Programhanterare) i Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Skapa ett nytt Azure IoT Central-program:

1. Välj betalningsplanen **Kostnadsfri utvärderingsversion**.
1. Välj ett eget programnamn, exempelvis **Contoso IoT**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra URL-prefixet till något som är enklare att komma ihåg.
1. Välj programmallen **Contoso-exempel**.
1. Välj sedan **Skapa**.

![Sidan Skapa program i Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett förifyllt Azure IoT Central-program som innehåller enhetsmallen **Kylande varuautomat** och simulerade enheter. Se [Definiera en ny enhetsmall i ditt program](tutorial-define-device-type.md) för mer information om hur du som byggare kan definiera dina egna enhetsmallar.
