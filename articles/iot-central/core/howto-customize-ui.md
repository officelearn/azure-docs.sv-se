---
title: Anpassa azure IoT Central UI | Microsoft-dokument
description: Anpassa tema- och hjälplänkarna för ditt centrala Azure IoT-program
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158047"
---
# <a name="customize-the-azure-iot-central-ui"></a>Anpassa azure IoT Central UI

I den här artikeln beskrivs hur du som administratör kan anpassa programmets användargränssnitt genom att använda anpassade teman och ändra hjälplänkarna så att de pekar på dina egna anpassade hjälpresurser. 



Följande skärmbild visar en sida med standardtemat:

![Centralt IoT-tema](./media/howto-customize-ui/standard-ui.png)

Följande skärmbild visar en sida med en anpassad skärmbild med de anpassade gränssnittselementen markerade:

![Anpassat IoT-centralt tema](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Skapa tema

Om du vill skapa ett anpassat tema navigerar du till sidan **Anpassa programmet** i avsnittet **Administration:**

![IoT Centrala teman](./media/howto-customize-ui/themes.png)

På den här sidan kan du anpassa följande aspekter av ditt program:

### <a name="application-logo"></a>Logotyp för program

En PNG-bild, inte större än 1 MB, med en genomskinlig bakgrund. Den här logotypen visas till vänster i IoT Central-programmets namnlist.

Om logotypen innehåller namnet på programmet kan du dölja programnamnstexten. Mer information finns i [Hantera ditt program](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ikon för webbläsare (favicon)

En PNG-bild, inte större än 32 x 32 pixlar, med en genomskinlig bakgrund. En webbläsare kan använda den här bilden i adressfältet, historik, bokmärken och webbläsarfliken.

### <a name="browser-colors"></a>Webbläsarfärger

Du kan ändra färg på sidhuvudet och färgen som används för accentknappar och andra högdagrar. Använd ett färgvärde med sex `##ff6347`tecken i formatet . Mer information om färg notation för **HEX-värde** finns i [HTML-färger](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Du kan alltid återgå till standardalternativen på **sidan Anpassa programmet.**

### <a name="changes-for-operators"></a>Ändringar för operatörer

Om en administratör skapar ett anpassat tema kan operatorer och andra användare av programmet inte längre välja ett tema i **Inställningar**.

## <a name="replace-help-links"></a>Ersätt hjälplänkar

Om du vill ge anpassad hjälpinformation till operatörer och andra användare kan du ändra länkarna på **programhjälpmenyn.**

Om du vill ändra hjälplänkarna navigerar du till **hjälpsidan Anpassa** i avsnittet **Administration:**

![Anpassa hjälplänkar för IoT Central](./media/howto-customize-ui/help-links.png)

Du kan också lägga till nya poster på hjälpmenyn och ta bort standardposter:

![Anpassad IoT Central-hjälp](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Du kan alltid återgå till standardhjälplänkarna på **hjälpsidan Anpassa.**

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du anpassar användargränssnittet i ditt IoT Central-program, här är några förslag på nästa steg:

- [Administrera ditt program](./howto-administer.md)
- [Lägga till paneler på din instrumentpanel](howto-add-tiles-to-your-dashboard.md)