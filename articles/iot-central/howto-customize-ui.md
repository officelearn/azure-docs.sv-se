---
title: Anpassa Azure IoT Central Användargränssnittet | Microsoft Docs
description: Hur du anpassar tema och hjälper dig att länkar till din Azure IoT central-App
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0256396cd228898f3852772b113e6064a0656746
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237664"
---
# <a name="customize-the-azure-iot-central-ui"></a>Anpassa Azure IoT Central UI 

*Den här artikeln gäller för administratörer.*

IoT Central kan du anpassa Användargränssnittet för ditt program genom att använda anpassade teman och ändra hjälplänkar så att den pekar till dina egna anpassade hjälpresurser. I följande skärmbild visas en sida med standard temat:

![Standard IoT Central-tema](./media/howto-customize-ui/standard-ui.png)

I följande skärmbild visas en sida med en anpassad skärmbild med de anpassade UI-element som är markerade:

![Anpassat IoT Central tema](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Skapa tema

Skapa ett anpassat tema, gå till den **anpassa programmet** sidan i den **Administration** avsnittet:

![IoT Central teman](./media/howto-customize-ui/themes.png)

Du kan anpassa följande aspekter av ditt program på den här sidan:

### <a name="application-logo"></a>Programlogotyp

En PNG-bild, inte är större än 1 MB, med en genomskinlig bakgrund. Den här logotypen visas till vänster i namnlisten för IoT Central-programmet.

Om logotypen innehåller namnet på ditt program kan dölja du program namn text. Mer information finns i [Hantera tillämpningsinställningar](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Bläddringsikonen (favicon)

En PNG-bild, inte är större än 32 x 32 bildpunkter med genomskinlig bakgrund. En webbläsare kan använda den här avbildningen i adressfältet, tidigare, bokmärken och flik i webbläsaren.

### <a name="browser-colors"></a>Webbläsarfärger

Du kan ändra färg på sidhuvudet och den färg som används för accenting knappar och andra viktiga funktioner. Använda ett värde för sex tecken hexadecimal färg i formatet `##ff6347`. Mer information om **HEX värdet** färg notation, se [HTML färger](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Du kan alltid återgå till standardalternativen på den **anpassa programmet** sidan.

### <a name="changes-for-operators"></a>Ändringar för operatörer

Om en administratör skapar ett anpassat tema operatörer och användare av ditt program kan inte längre och välj sedan ett tema i **inställningar**.

## <a name="replace-help-links"></a>Ersätt hjälplänkar

För att tillhandahålla anpassade hjälpinformation åt dina operatörer och användare, kan du ändra länkarna på programmet **hjälpa** menyn.

Om du vill ändra hjälplänkar, navigera till den **Anpassa hjälp** sidan i den **Administration** avsnittet:

![Anpassa länkar i IoT Central](./media/howto-customize-ui/help-links.png)

Du kan också lägga till nya poster i Hjälp-menyn och ta bort standardposter:

![Anpassad IoT Central-hjälp](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Du kan alltid återgå till standard hjälplänkar på den **Anpassa hjälp** sidan.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du anpassar Användargränssnittet i ditt IoT Central-program, är här några nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](./howto-administer.md)
> [konfigurera instrumentpanelen för program](./howto-configure-homepage.md)