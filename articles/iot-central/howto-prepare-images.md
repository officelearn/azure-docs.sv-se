---
title: Ladda upp bilder till programmet Azure IoT Central | Microsoft Docs
description: Lär dig hur du förbereder och ladda upp bilder till Azure IoT Central programmet som en builder.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 18c44a3d91a4964d054c8e142394da7d69772ed0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960709"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Förbereda och ladda upp bilder till Azure IoT Central programmet

Den här artikeln beskrivs hur, som ett verktyg du kan anpassa ditt Microsoft Azure IoT Central program genom att ladda upp anpassade avbildningar. Du kan exempelvis anpassa en instrumentpanel för enheten med en bild av enheten.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central program. Mer information finns i den [en program-snabbstarten för att skapa](quick-deploy-iot-central.md).
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
| **Programhanterare** | 268 x 160 bpt |
| Mall för enhet | 64 x 64 px |
| Startsida och paneler på instrumentpanelen | Den minsta storlek panelen är 200 x 200 bildpunkter, större paneler kan vara antingen kvadratisk eller rektangulär multiplar av små paneler. Till exempel 200 x 400 bildpunkter, 400 x 200 bildpunkter eller 400 x 400 bildpunkter |

För bästa utseende i programmet, bör du skapa bilder som motsvarar de dimensioner som visas i föregående tabell.

## <a name="upload-the-images"></a>Ladda upp avbildningar

I följande avsnitt beskrivs hur du överför avbildningarna som ska användas på olika platser:

### <a name="application-manager"></a>Programhanterare

Ladda upp en bild som används på den **Programhanterare**, navigera till den **programinställningar** sidan i den **Administration** avsnittet. Du måste vara administratör för att slutföra den här åtgärden:

![Ladda upp programavbildning](media/howto-prepare-images/uploadapplicationmanager.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från din lokala dator.

### <a name="home-page"></a>Startsida

Om du vill ladda upp en bild som används på startsidan, navigera till den **startsidan** programmet och växel designläget på. Du måste vara ett verktyg för att slutföra den här aktiviteten:

![Överför bild på startsidan](media/howto-prepare-images/uploadhomepage.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från din lokala dator.

När avbildningen överför du kan ändra storlek på den medan designläget är påslagen.

### <a name="device-template"></a>Mall för enhet

Om du vill ladda upp en bild som används i en mall för enhet, gå till **Device Explorer**, väljer mallen enhet och sedan på en enhet och aktivera designläge. Du måste vara ett verktyg för att slutföra den här aktiviteten:

![Ladda upp enheten mall-bild](media/howto-prepare-images/uploaddevicetemplate.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från din lokala dator.

### <a name="device-dashboard"></a>Instrumentpanel för enhet

Om du vill ladda upp en bild som används på en instrumentpanel för enhet, gå till **Device Explorer**, väljer mallen enhet och en enhet. Välj sedan den **instrumentpanelen** sidan och växel designläget på. Du måste vara ett verktyg för att slutföra den här aktiviteten:

![Ladda upp enhetsavbildning för instrumentpanelen](media/howto-prepare-images/uploaddevicedashboard.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från din lokala dator.

När avbildningen överför du ändra storlek på och flytta den vid **designläget** är påslagen.

### <a name="device-set-dashboard"></a>Instrumentpanelen för set

Om du vill ladda upp en bild som används på en enhet set-instrumentpanel, gå till **enhetsuppsättningar** och välj enhetsuppsättningen och en enhet. Välj sedan den **instrumentpanelen** sidan och växel **designläget** på:

![Ladda upp enhetsavbildning set instrumentpanel](media/howto-prepare-images/uploaddevicesetdashboard.png)

Klicka på ladda upp bilden och välj sedan den fil som ska överföras från din lokala dator.

När avbildningen överför du ändra storlek på och flytta den medan designläget slås på.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du förbereder och ladda upp bilder till Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hantera enheter i ditt program med Azure IoT Central](howto-manage-devices.md)