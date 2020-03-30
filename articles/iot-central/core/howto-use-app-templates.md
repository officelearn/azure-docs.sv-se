---
title: Exportera ett Azure IoT Central-program | Microsoft-dokument
description: Som lösningshanterare vill jag exportera en programmall för att kunna återanvända den.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157657"
---
# <a name="export-your-application"></a>Exportera ditt program



I den här artikeln beskrivs hur du som lösningshanterare exporterar ett IoT Central-program för att kunna återanvända det.

Du kan välja mellan två alternativ:

- Du kan skapa en kopia av ditt program om du bara behöver skapa en kopia av programmet.
- Du kan skapa en programmall från programmet om du planerar att skapa flera kopior.

## <a name="copy-your-application"></a>Kopiera programmet

Du kan skapa en kopia av alla program, minus alla enhetsinstanser, enhetsdatahistorik och användardata. Kopian använder en standardprisplan som du ska faktureras för. Du kan inte skapa ett program som använder den kostnadsfria prisplanen genom att kopiera ett program.

Välj **Kopiera**. Ange information om det nya programmet i dialogrutan. Välj sedan **Kopiera** för att bekräfta att du vill fortsätta. Mer information om fälten i formuläret finns i snabbstarten [Skapa ett program.](quick-deploy-iot-central.md)

![Sidan Programinställningar](media/howto-use-app-templates/appcopy2.png)

När appkopieringen har slutförts kan du navigera till det nya programmet med hjälp av länken.

![Sidan Programinställningar](media/howto-use-app-templates/appcopy3a.png)

När du kopierar ett program kopieras också definitionen av regler och e-poståtgärd. Vissa åtgärder, till exempel Flow och Logic Apps, är kopplade till specifika regler via regel-ID. När en regel kopieras till ett annat program får den ett eget regel-ID. I det här fallet måste användarna skapa en ny åtgärd och sedan associera den nya regeln med den. I allmänhet är det en bra idé att kontrollera regler och åtgärder för att se till att de är uppdaterade i den nya appen.

> [!WARNING]
> Om en instrumentpanel innehåller paneler som visar information om specifika enheter visas inte **den begärda resursen** i det nya programmet. Du måste konfigurera om dessa paneler för att visa information om enheter i det nya programmet.

## <a name="create-an-application-template"></a>Skapa en programmall

När du skapar ett Azure IoT Central-program kan du välja mellan inbyggda exempelmallar. Du kan också skapa egna programmallar från befintliga IoT Central-program. Du kan sedan använda egna programmallar när du skapar nya program.

När du skapar en programmall innehåller den följande objekt från ditt befintliga program:

- Standardinstrumentpanelen för program, inklusive instrumentpanelslayouten och alla paneler som du har definierat.
- Enhetsmallar, inklusive mått, inställningar, egenskaper, kommandon och instrumentpanel.
- Regler. Alla regeldefinitioner ingår. Åtgärder, förutom e-poståtgärder, inkluderas dock inte.
- Enhetsuppsättningar, inklusive deras villkor och instrumentpaneler.

> [!WARNING]
> Om en instrumentpanel innehåller paneler som visar information om specifika enheter visas inte **den begärda resursen** i det nya programmet. Du måste konfigurera om dessa paneler för att visa information om enheter i det nya programmet.

När du skapar en programmall innehåller den inte följande objekt:

- Enheter
- Användare
- Jobbdefinitioner
- Definitioner för kontinuerlig dataexport

Lägg till dessa objekt manuellt i alla program som skapats från en programmall.

Så här skapar du en programmall från ett befintligt IoT Central-program:

1. Gå till avsnittet **Administration** i programmet.
1. Välj **Exportera programmall**.
1. Ange ett namn och en beskrivning för mallen på sidan **Exportera programmall.**
1. Välj knappen **Exportera** om du vill skapa programmallen. Du kan nu kopiera den **delbara länken** som gör det möjligt för någon att skapa ett nytt program från mallen:

![Skapa en programmall](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Använda en programmall

Om du vill använda en programmall för att skapa ett nytt IoT Central-program behöver du en tidigare skapad **delningsbar länk**. Klistra in den **delbara länken** i webbläsarens adressfält. Sidan **Skapa ett program** visas med den anpassade programmallen markerad:

![Skapa ett program från en mall](media/howto-use-app-templates/create-app.png)

Välj prisplan och fyll i de andra fälten i formuläret . Välj sedan **Skapa** för att skapa ett nytt IoT Central-program från programmallen.

### <a name="manage-application-templates"></a>Hantera programmallar

På sidan **Exportera programmall** kan du ta bort eller uppdatera programmallen.

Om du tar bort en programmall kan du inte längre använda den tidigare genererade delningsbara länken för att skapa nya program.

Om du vill uppdatera programmallen ändrar du mallens namn eller beskrivning på sidan **Exportera programmall.** Välj sedan knappen **Exportera** igen. Den här åtgärden genererar en ny **delningsbar länk** och ogiltigförklarar alla tidigare **shareable länk** URL.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder programmallar är det föreslagna nästa steget att lära dig hur du [hanterar IoT Central från Azure-portalen](howto-manage-iot-central-from-portal.md)
