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
ms.openlocfilehash: 0199efbc1456206c67efb846b4381cae333e3749
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309050"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera ditt Microsoft Azure IoT Central-program. Den här snabbstarten visar hur du skapar ett Azure IoT Central-program som innehåller ett exempel på en _enhetsmall_ och simulerade _enheter_.

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
