---
title: Skapa personliga instrumentpaneler för Azure IoT Central | Microsoft Docs
description: Lär dig hur du skapar och hanterar dina personliga instrumentpaneler som en användare.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a7de9126716437e4e90185f46c9cf95f7de20d40
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220538"
---
# <a name="create-and-manage-personal-dashboards"></a>Skapa och hantera personliga instrumentpaneler

Den **instrumentpanelen** är den sida som läses in när du navigerar till programmet. En **builder** definierar standard-instrumentpanel för program för alla användare i ditt program. Du kan ersätta den här standardinstrumentpanelen med din egen, anpassade program-instrumentpanel. Du kan ha flera instrumentpaneler som visar olika data och växla mellan dem.

## <a name="create-dashboard"></a>Skapa instrumentpanel

I följande skärmbild visas på instrumentpanelen i ett program som skapats från den **exempel Contoso** mall. Du kan ersätta standardinstrumentpanelen för program med en personlig instrumentpanel. Om du vill göra det klickar du på **+ ny** längst upp höger på sidan.

![Instrumentpanel för program baserat på mallen ”Contoso exemplet”](media/howto-personalize-dashboard-experimental/defaultdashboard.png)

Klicka på **+ ny**, öppnas Instrumentpanelsredigeraren. I redigeraren, du kan ge instrumentpanelen ett namn och välja objekt från biblioteket. Biblioteket innehåller paneler och instrumentpanelen primitiver som du kan använda för att anpassa instrumentpanelen.

![Instrumentpanelen bibliotek](media/howto-personalize-dashboard-experimental/dashboardeditor.png)

Du kan till exempel lägga till en **inställningar och egenskaper** panelen för att inställningar och egenskaper för en av de enheter du hanterar visas. Om du vill göra det väljer du först en **enheten mallen** Välj sedan en **enhetsinstansen**. Ge sedan panelen en rubrik och väljer en **inställningen** eller en **egenskapen** ska visas. Följande skärmbild visar de **sprider hastighet** inställningen har valts för att lägga till i panelen. Klicka på **klar** att spara ändringarna på instrumentpanelen.

![”Konfigurera enhetsinformation” formuläret med information om inställningar och egenskaper](media/howto-personalize-dashboard-experimental/dashboardsetting.png)

Nu när du visar din instrumentpanel kan du se den nya panelen med den **sprider hastighet** för enheten:

![Fliken ”instrumentpanel” med visas inställningar och egenskaper för panelen](media/howto-personalize-dashboard-experimental/personaldashboard.png)

Du kan utforska andra typer av paneler i biblioteket för att se hur att ytterligare anpassa dina personliga instrumentpaneler.

## <a name="manage-dashboards"></a>Hantera instrumentpaneler

Du kan ha flera personliga instrumentpaneler och växla mellan dem eller välj standardinstrumentpanelen för programmet:

![Instrumentpanel för växeln](media/howto-personalize-dashboard-experimental/switchdashboards.png)

Du kan redigera dina personliga instrumentpaneler och ta bort de som du inte längre behöver:

![Ta bort instrumentpanel](media/howto-personalize-dashboard-experimental/managedashboards.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och hanterar personliga instrumentpaneler, kan du:

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar din programinställningar](howto-manage-preferences.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
