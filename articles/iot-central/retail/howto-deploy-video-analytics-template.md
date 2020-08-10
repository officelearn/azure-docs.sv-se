---
title: Så här distribuerar du Azure IoT Central program mal len video analys – objekt och rörelse identifiering
description: Den här guiden sammanfattar stegen för att distribuera ett Azure IoT Central-program med hjälp av program mal len video analys – objekt och rörelse identifiering.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d2ab93708950743eafdb6cf733273b602cfb5a68
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038282"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Distribuera ett IoT Central program med hjälp av program mal len video analys – objekt och rörelse identifiering

En översikt över program komponenterna för nyckel *video analys – objekt och motion-identifiering* finns i [program arkitektur för objekt-och motion-identifiering](architecture-video-analytics.md).

## <a name="deploy-the-application"></a>Distribuera programmet

Utför följande steg för att distribuera ett IoT Central program med hjälp av program mal len video analys:

1. Slutför självstudien [skapa ett video analys program i Azure IoT Central](tutorial-video-analytics-create-app.md) för att:
    - Skapa ett Azure Media Services-konto.
    - Skapa IoT Central-programmet från program mal len video analys – objekt och rörelse identifiering.
    - Konfigurera en gateway-enhet i IoT Central-programmet. Gatewayen gör det möjligt för kamera enheter att ansluta till programmet.

1. Slutför självstudien [skapa en IoT Edge instans för video analys (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) för att:
    - Skapa en virtuell Azure-dator med Azure IoT Edge Runtime installerat. – Förbered IoT Edge-installationen som värd för video analys modulen.
    - Anslut IoT Edge-enheten till ditt IoT Central-program.

1. Slutför guiden [övervaka och hantera en video analys program](tutorial-video-analytics-manage.md) för att:
    - Lägg till kameror för objekt-och rörelse avkänning till gatewayen i ditt IoT Central-program.
    - Starta kamera bearbetningen.
    - Installera en lokal medie spelare för att visa fångade videor i AMS.
    - Visa fångade videor som visar identifierade objekt.
    - Städa upp.

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över stegen för att distribuera och använda program mal len video analys, se [skapa ett video analys n-program i Azure IoT Central](tutorial-video-analytics-create-app.md) för att komma igång.
