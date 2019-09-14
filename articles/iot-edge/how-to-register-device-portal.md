---
title: Registrera en ny enhet från Azure portal – Azure IoT Edge | Microsoft Docs
description: Använda Azure portal för att registrera en ny IoT Edge-enhet och hämta anslutningssträngen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7f3d0037bcf0fd33ae23c298679e3157046247cb
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983533"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrera en ny Azure IoT Edge-enhet från Azure portal

Innan du kan använda dina IoT-enheter med Azure IoT Edge, måste du registrera dem med IoT-hubben. När du registrerar en enhet får du en anslutnings sträng som kan användas för att konfigurera enheten för IoT Edge arbets belastningar.

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med hjälp av Azure portal.

## <a name="prerequisites"></a>Förutsättningar

En kostnads fri eller standard [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

## <a name="create-a-device"></a>Skapa en enhet

I Azure portal, IoT Edge-enheter skapas och hanteras separat från enheter som ansluter till IoT-hubben och som inte edge-aktiverade.

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
2. Välj **IoT Edge** på menyn.
3. Välj **Lägg till en IoT Edge enhet**.
4. Ange ett beskrivande enhets-ID. Använd standardinställningarna för att automatiskt generera autentiseringsinställningar och Anslut den nya enheten till hubben.
5. Välj **Spara**.

## <a name="view-all-devices"></a>Visa alla enheter

Alla edge-aktiverade enheter som ansluter till IoT-hubben visas på den **IoT Edge** sidan.

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub.

1. På sidan **IoT Edge** i portalen klickar du på enhets-ID i listan över IoT Edge enheter.
2. Kopiera värdet för antingen **anslutningssträngen (primärnyckel)** eller **anslutningssträngen (sekundärnyckel)** .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler till en enhet med Azure Portal](how-to-deploy-modules-portal.md).
