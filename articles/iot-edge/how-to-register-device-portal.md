---
title: Registrera en ny Azure IoT Edge-enhet (portal) | Microsoft Docs
description: Använda Azure portal för att registrera en ny IoT Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b61594469df33e11c23c9cbe0b9542da374fefa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036306"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrera en ny Azure IoT Edge-enhet från Azure-portalen

Innan du kan använda din IoT-enheter med Azure IoT gräns, måste du registrera dem med din IoT-hubb. När du registrerar en enhet kan få du en anslutningssträng som kan användas för att konfigurera din enhet för Edge arbetsbelastningar. 

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration. 

## <a name="create-a-device"></a>Skapa en enhet

I Azure-portalen IoT-gränsenheterna skapas och hanteras separat från enheter som ansluter till din IoT-hubb och som inte edge-aktiverade. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb. 
2. Välj **IoT kant** på menyn.
3. Välj **lägga till IoT Gränsenheten**. 
4. Ange en beskrivande enhets-ID. 
5. Välj **Spara**. 

## <a name="view-all-devices"></a>Visa alla enheter

Alla edge-aktiverade enheter som ansluter till din IoT-hubb visas på den **IoT kant** sidan. 

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT-hubben.

1. Från den **IoT kant** på portalen, klicka på enhets-ID i listan över enheter. 
2. Kopiera värdet för antingen **anslutningssträngen – primärnyckel** eller **anslutningssträngen – sekundärnyckeln**. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler på en enhet med Azure-portalen](how-to-deploy-modules-portal.md)