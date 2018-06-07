---
title: Ladda upp bilder till din Azure IoT centralt program | Microsoft Docs
description: Som en builder lär du dig hur du förbereder och ladda upp bilder till din Azure IoT centralt program.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fd9c8ed5559b00bc755e3f04c768dceeb487562
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628029"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Förbereda och ladda upp bilder till din Azure IoT centralt program

Den här artikeln beskriver hur du gör en builder du anpassa Microsoft Azure IoT Central programmet genom att överföra anpassade avbildningar. Exempelvis kan du anpassa en instrumentpanel för enheten med en bild av enheten.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT centrala program. Mer information finns i [skapa Azure IoT centrala programmet](howto-create-application.md).
1. Ett verktyg för att skala och ändra storlek på bildfiler.

## <a name="choose-where-to-use-custom-images"></a>Välj var du vill använda anpassade avbildningar

Du kan lägga till anpassade avbildningar i ett Azure IoT centrala program på följande platser:

* Den **Programhanterare** sidan

    ![Bild på sidan för programmet manager](media/howto-prepare-images/applicationmanager.png)

* Startsidan

    ![Bild på startsidan](media/howto-prepare-images/homepage.png)

* En mall för enhet

    ![Bild på enheten mall](media/howto-prepare-images/devicetemplate.png)

* En panel på instrumentpanelen för enhet

    ![Bild på ikonen för enheten](media/howto-prepare-images/devicetile.png)

* En panel på en enhet ange instrumentpanelen

    ![Bild på enheten set-ikonen](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Förbereda avbildningar

Du kan använda PNG, GIF- eller JPEG-bilder i alla fyra platser.

I följande tabell sammanfattas bildstorleken som du kan använda:

| Plats | Storlekar |
| -------- | ------ |
| **Programhanterare** | 268 x 160 px |
| Mall för enhet | 64 x 64 px |
| Startsida och instrumentpaneler | Minsta storlek panelen är 200 x 200 px, större paneler kan vara antingen eller kvadrat multiplar av små paneler. Till exempel 200 x 400 px, 400 x 200 px eller 400 x 400 px |

Den bästa visningen i programmet, bör du skapa bilder som motsvarar de dimensioner som visas i föregående tabell.

## <a name="upload-the-images"></a>Ladda upp bilder

I följande avsnitt beskrivs hur du överför bilder som ska användas på olika platser:

### <a name="application-manager"></a>Programhanterare

Att ladda upp en bild som ska använda på den **Programhanterare**, navigera till den **programinställningar** sidan i den **Administration** avsnitt. Du måste vara administratör för att kunna slutföra den här aktiviteten:

![Överför programavbildning](media/howto-prepare-images/uploadapplicationmanager.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från den lokala datorn.

### <a name="home-page"></a>Startsida

Om du vill ladda upp en bild som ska användas på startsidan, navigera till den **webbsida** för programmet och växel designläge på. Du måste vara ett verktyg för att slutföra den här uppgiften:

![Överför bild på startsidan](media/howto-prepare-images/uploadhomepage.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från den lokala datorn.

När avbildningen överför ändrar du den medan designläge slås på.

### <a name="device-template"></a>Mall för enhet

Om du vill ladda upp en bild som ska användas på en mall för enheten, gå till **enheten Explorer**, väljer mallen enhet och en enhet och aktivera designläge. Du måste vara ett verktyg för att slutföra den här uppgiften:

![Överför mallavbildningen för enhet](media/howto-prepare-images/uploaddevicetemplate.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från den lokala datorn.

### <a name="device-dashboard"></a>Instrumentpanel för enhet

Om du vill ladda upp en bild som ska användas på en instrumentpanel för enheten, gå till **enheten Explorer**, väljer mallen enhet och en enhet. Välj den **instrumentpanelen** sida och växel designläge på. Du måste vara ett verktyg för att slutföra den här uppgiften:

![Överför enhet instrumentpanelen bild](media/howto-prepare-images/uploaddevicedashboard.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från den lokala datorn.

När överföringar, du kan ändra storlek på och flytta den när **designläge** är påslagen.

### <a name="device-set-dashboard"></a>Enheten ange instrumentpanelen

Om du vill ladda upp en bild som ska användas på en enhet set instrumentpanel, gå till **enheten anger** och välj enhetsuppsättningen och en enhet. Välj den **instrumentpanelen** sida och växel **designläge** på:

![Överför enhet set instrumentpanelen bild](media/howto-prepare-images/uploaddevicesetdashboard.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från den lokala datorn.

När överföringar, du ändra storlek på och flytta den medan designläge slås på.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du förbereder och ladda upp bilder till din Azure IoT centralt program, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Hantera enheter i din Azure IoT centralt program](howto-manage-devices.md)