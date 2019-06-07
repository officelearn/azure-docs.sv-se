---
title: Använd programmallar i Azure IoT Central | Microsoft Docs
description: Som operatör anger hur du använder enheten i Azure IoT Central programmet.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499226"
---
# <a name="use-application-templates"></a>Använda programmallar

Den här artikeln beskrivs hur som lösningen kan du skapa och Använd programmallar.

När du skapar ett Azure IoT Central program, har du möjlighet att välja inbyggda exempelmallar. Du kan också skapa egna mallar i program från befintliga IoT Central-program. Du kan sedan använda dina egna programmallar när du skapar nya program.

När du skapar en programmall innehåller följande objekt från ditt befintliga program:

- Standardinstrumentpanelen för program, inklusive instrumentpanelens layout och alla paneler som du har definierat.
- Enheten mallar, inklusive mått, inställningar, egenskaper, kommandon och instrumentpanelen.
- Regler. Alla Regeldefinitioner ingår. Men inkluderas inte åtgärder, förutom e-poståtgärder.
- Enhetsuppsättningar, inklusive villkoren och instrumentpaneler.

> [!WARNING]
> Om en instrumentpanel innehåller paneler som visar information om specifika enheter och sedan visa dessa paneler **gick inte att hitta den begärda resursen** i det nya programmet. Du måste konfigurera om dessa paneler för att visa information om enheter i det nya programmet.

När du skapar en programmall kan innehåller den inte följande objekt:

- Enheter
- Användare
- Jobbdefinitioner
- Löpande dataexport definitioner

Lägg till dessa objekt manuellt i alla program som har skapats från en mall för program.

## <a name="create-an-application-template"></a>Skapa en programmall

Skapa en programmall från ett befintligt IoT Central-program:

1. Gå till den **Administration** avsnittet i ditt program.
1. Välj **programmet Mallexporten**.
1. På den **program mall exportera** anger du ett namn och en beskrivning av mallen för.
1. Välj den **exportera** för att skapa mall för program. Nu kan du kopiera den **delbart länken** som gör det möjligt för någon att skapa ett nytt program från mallen:

![Skapa en programmall](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Använda en mall för program

Om du vill använda en programmall för att skapa en ny IoT Central-App, behöver du en tidigare skapad **delbart länk**. Klistra in den **delbart länken** i webbläsarens adressfält. Den **skapar ett program** sidan visas med din mall för anpassade program som har valt:

![Skapa ett program från en mall](media/howto-use-app-templates/create-app.png)

Välj din betalningsplan och fylla i de andra fälten i formuläret. Välj sedan **skapa** att skapa en ny IoT Central-App från mall för program.

## <a name="manage-application-templates"></a>Hantera mallar för program

På den **program mall exportera** kan du ta bort eller uppdatera programmallen.

Om du tar bort en programmall kan använda du inte längre tidigare genererade delbart länken för att skapa nya program.

Om du vill uppdatera en mall för ditt program, ändra mallens namn eller beskrivning på den **program mall exportera** sidan. Välj sedan den **exportera** igen. Den här åtgärden genererar en ny **delbart länken** och upphäver tidigare **delbart länken** URL: en.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder programmallar föreslagna nästa steg är att lära dig hur du [hantera IoT Central från Azure portal](howto-manage-iot-central-from-portal.md)
