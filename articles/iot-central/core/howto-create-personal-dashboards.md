---
title: Skapa personliga instrumentpaneler för Azure IoT Central | Microsoft-dokument
description: Som användare kan du lära dig hur du skapar och hanterar dina personliga instrumentpaneler.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985498"
---
# <a name="create-and-manage-multiple-dashboards"></a>Skapa och hantera flera instrumentpaneler

**Instrumentpanelen** är den sida som läses in när du navigerar till ditt program. En **byggare** i programmet definierar standardprograminstrumentpanelen för alla användare. Du kan dessutom skapa en egen, personlig programinstrumentpanel. Du kan ha flera instrumentpaneler som visar olika data och växlar mellan dem.

Om du är **administratör för** programmet kan du också skapa upp till 10 instrumentpaneler på programnivå som du kan dela med andra användare av programmet. Endast **administratörer** har möjlighet att skapa, redigera och ta bort instrumentpaneler på programnivå. 

## <a name="create-dashboard"></a>Skapa instrumentpanel

Följande skärmbild visar instrumentpanelen i ett program som skapats från mallen **Anpassat program.** Du kan ersätta standardinstrumentpanelen för program med en personlig instrumentpanel, eller om du är administratör, en annan instrumentpanel på programnivå. Om du vill göra det väljer du **+ Nytt** längst upp till vänster på sidan.
 
> [!div class="mx-imgBorder"]
> ![Instrumentpanel för program som baseras på mallen "Anpassat program"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Om du väljer **+ Nytt** öppnas instrumentpanelsredigeraren. I redigeraren kan du ge instrumentpanelen ett namn och välja objekt från biblioteket. Biblioteket innehåller de paneler och instrumentpanelsprimitiver som du kan använda för att anpassa instrumentpanelen.

> [!div class="mx-imgBorder"]
> ![Instrumentpanelsbibliotek](media/howto-create-personal-dashboards/dashboard-library.png)

Om du är **administratör för** programmet får du möjlighet att växla om du vill skapa en instrumentpanel på personlig nivå eller en instrumentpanel på programnivå. Om du skapar en instrumentpanel på personlig nivå är det bara du som kan se den. Om du skapar en instrumentpanel på programnivå kan alla användare av programmet se den. När du har angett en rubrik och valt vilken typ av instrumentpanel du vill skapa kan du spara och lägga till paneler senare. Om du är redo nu och har lagt till en enhetsmall och enhetsinstans kan du skapa den första panelen. 

> [!div class="mx-imgBorder"]
> ![Konfigurera enhetsinformation" formulär med information om temperatur](media/howto-create-personal-dashboards/device-details.png)

Du kan till exempel lägga till en **telemetripanel** för enhetens aktuella temperatur. Gör så här:
1. Välj en **enhetsmall**
1. Välj en **enhetsinstans** för den enhet som du vill se på en instrumentpanelspanel. Då ser du en lista över enhetens egenskaper som kan användas på panelen.
1. Om du vill skapa panelen på instrumentpanelen klickar du på **Temperatur** och drar den till instrumentpanelsområdet. Du kan också klicka på kryssrutan bredvid **Temperatur** och klicka på **Kombinera**. Följande skärmbild visar hur du väljer en enhetsmall och enhetsinstans och sedan skapar en temperaturtelemetripanel på instrumentpanelen.
1. Välj **Spara** längst upp till vänster om du vill spara panelen på instrumentpanelen.

> [!div class="mx-imgBorder"]
> ![Fliken Dashboard" med information för panelen Temperatur](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Nu när du visar din personliga instrumentpanel visas den nya panelen med **inställningen Temperatur** för enheten:

> [!div class="mx-imgBorder"]
> ![Fliken Dashboard" med information för panelen Temperatur](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Du kan utforska andra paneltyper i biblioteket för att upptäcka hur du kan anpassa dina personliga instrumentpaneler ytterligare.

Mer information om hur du använder paneler i Azure IoT Central finns i [Lägga till paneler på instrumentpanelen](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Hantera instrumentpaneler

Du kan ha flera personliga instrumentpaneler och växla mellan dem eller välja från någon av standardinstrumentpanelerna för program:

> [!div class="mx-imgBorder"]
> ![Växla mellan instrumentpaneler](media/howto-create-personal-dashboards/switch-dashboards.png)

Du kan redigera dina personliga instrumentpaneler och ta bort alla instrumentpaneler som du inte längre behöver. Om du är **administratör**har du också möjlighet att redigera eller ta bort instrumentpaneler på programnivå också.

> [!div class="mx-imgBorder"]
> ![Ta bort instrumentpaneler](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och hanterar personliga instrumentpaneler kan du [läsa om hur du hanterar dina programinställningar](howto-manage-preferences.md)
