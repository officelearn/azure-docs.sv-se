---
title: Ladda upp bilder till programmet Azure IoT Central | Microsoft Docs
description: Lär dig hur du förbereder och ladda upp bilder till Azure IoT Central programmet som en builder.
author: dominicbetts
ms.author: dobett
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 9d4de2a8be13718f7989f064b76b586407ca3a48
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198617"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Förbereda och ladda upp bilder till Azure IoT Central programmet

Den här artikeln beskrivs hur, som ett verktyg du kan anpassa ditt Azure IoT Central program genom att ladda upp anpassade avbildningar. Du kan exempelvis anpassa en instrumentpanel för enheten med en bild av enheten.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
1. Ett verktyg för att skala och ändra storlek på avbildningar filer.

## <a name="choose-where-to-use-custom-images"></a>Välj var du vill använda anpassade avbildningar

Du kan lägga till anpassade avbildningar på följande platser i en Azure IoT Central program:

* Den **Programhanterare** sidan

    ![Bild på program manager-sidan](media/howto-prepare-images/applicationmanager.png)

* Startsidan

    ![Bild på startsidan](media/howto-prepare-images/homepage.png)

* En mall för enhet

    ![Bild på enheten mall](media/howto-prepare-images/devicetemplate.png)

* En panel på en instrumentpanel för enhet

    ![Bild på enhetspanelen för](media/howto-prepare-images/devicetile.png)

* En panel på en enhet ange instrumentpanel

    ![Bild på enheten set panel](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Förbered avbildningar

Du kan använda PNG-, GIF- eller JPEG-bilder på alla fyra platser.

I följande tabell sammanfattas bildstorleken som du kan använda:

| Plats | Storlekar |
| -------- | ------ |
| Application Manager | 268 x 160 bpt |
| Enhetsmall | 64 x 64 px |
| Startsida och paneler på instrumentpanelen | Den minsta storlek panelen är 200 x 200 bildpunkter, större paneler kan vara antingen kvadratisk eller rektangulär multiplar av små paneler. Till exempel 200 x 400 bildpunkter, 400 x 200 bildpunkter eller 400 x 400 bildpunkter |

För bästa utseende i programmet, bör du skapa bilder som motsvarar de dimensioner som visas i föregående tabell.

## <a name="upload-the-images"></a>Ladda upp avbildningar

I följande avsnitt beskrivs hur du laddar upp bilder på olika platser:

### <a name="application-manager"></a>Programhanterare

Ladda upp en bild på den **Programhanterare**, navigera till den **programinställningar** sidan i den **Administration** avsnittet. Du måste vara administratör för att slutföra den här åtgärden:

![Ladda upp programavbildning](media/howto-prepare-images/uploadapplicationmanager.png)

Klicka på panelen programavbildningen att ladda upp en förberedd bild (268 x 160 bpt) från din lokala dator.

### <a name="home-page"></a>Startsida

Om du vill ladda upp en bild på startsidan, navigera till den **startsidan** för programmet och klicka på **redigera**. Du måste vara ett verktyg för att slutföra den här aktiviteten:

![Överför bild på startsidan](media/howto-prepare-images/uploadhomepage.png)

Under Konfigurera bild klickar du på ikonen bild att ladda upp en förberedd bild från din lokala dator. Den minsta storlek panelen är 200 x 200 bildpunkter, större paneler kan vara antingen kvadratisk eller rektangulär multiplar av små paneler. Till exempel 200 x 400 bildpunkter, 400 x 200 bildpunkter eller 400 x 400 bildpunkter.

**Spara** den uppladdade avbildningen. Du kan ändra storlek på den i redigeringsläget. Klicka på **klar** när du är klar. 

### <a name="device-template"></a>Enhetsmall

Om du vill ladda upp en bild i en mall för enhet, gå till **Device Explorer**, väljer mallen enhet och sedan på en enhet. Du måste vara ett verktyg för att slutföra den här aktiviteten:

![Ladda upp enheten mall-bild](media/howto-prepare-images/uploaddevicetemplate.png)

Klicka på bildpanel att ladda upp en förberedd bild (64 x 64 px) från din lokala dator. 

### <a name="device-dashboard"></a>Instrumentpanel för enhet

Om du vill ladda upp en bild på en instrumentpanel för enhet, gå till **Device Explorer**, väljer mallen enhet och en enhet. Välj sedan den **instrumentpanelen** och klickar på **Redigera mall**. Du måste vara ett verktyg för att slutföra den här aktiviteten:

![Ladda upp enhetsavbildning för instrumentpanelen](media/howto-prepare-images/uploaddevicedashboard.png)

Klicka på ikonen för avbildningen under Konfigurera bild och klicka på filen som ska överföras från din lokala dator. Den minsta storlek panelen är 200 x 200 bildpunkter, större paneler kan vara antingen kvadratisk eller rektangulär multiplar av små paneler. Till exempel 200 x 400 bildpunkter, 400 x 200 bildpunkter eller 400 x 400 bildpunkter.

**Spara** den uppladdade avbildningen. Du kan ändra storlek på och flytta den medan du är i redigeringsläget. Klicka på **klar** när du är klar.

### <a name="device-set-dashboard"></a>Instrumentpanelen för set

Om du vill ladda upp en bild på en enhet set-instrumentpanel, gå till **enhetsuppsättningar** och välj enhetsuppsättningen och en enhet. Välj sedan den **instrumentpanelen** och klickar på **redigera**:

![Ladda upp enhetsavbildning set instrumentpanel](media/howto-prepare-images/uploaddevicesetdashboard.png)

Under Konfigurera bild klickar du på ikonen bild att ladda upp en förberedd bild från din lokala dator. Den minsta storlek panelen är 200 x 200 bildpunkter, större paneler kan vara antingen kvadratisk eller rektangulär multiplar av små paneler. Till exempel 200 x 400 bildpunkter, 400 x 200 bildpunkter eller 400 x 400 bildpunkter.

**Spara** den uppladdade avbildningen. Du kan ändra storlek på och flytta den medan du är i redigeringsläget. Klicka på **klar** när du är klar.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du förbereder och ladda upp bilder till Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hantera enheter i ditt program med Azure IoT Central](howto-manage-devices.md)