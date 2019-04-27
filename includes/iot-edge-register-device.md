---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bb0b140dd1f42cae1d5d4bb670af8780d66c1f80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768639"
---
Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Eftersom IoT Edge-enheter fungerar och kan hanteras på annat sätt än typiska IoT-enheter, kan du ange denna som en IoT Edge-enhet från början. 

1. Gå till din IoT-hubb på Azure Portal.
1. Välj **IoT Edge** och sedan **Lägg till IoT Edge-enhet**.

   ![Lägg till IoT Edge-enhet](./media/iot-edge-register-device/add-device.png)

1. Ge den simulerade enheten ett unikt enhets-ID.
1. Välj **Spara** för att lägga till din enhet.
1. Välj den nya enheten i enhetslistan.
1. Kopiera värdet för **Anslutningssträng – primär nyckel** och spara det. Du behöver det här värdet för att konfigurera IoT Edge-körningen i nästa avsnitt. 

