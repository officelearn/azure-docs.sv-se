---
title: Använda programmallar i Azure IoT Central | Microsoft Docs
description: Som operatör använder du enhets uppsättningar i ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d682c49aa833b9e11dbbddc5e9f6afd52cbb6e84
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952880"
---
# <a name="use-application-templates"></a>Använd programmallar

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som lösnings hanterare, för att skapa och använda programmallar.

När du skapar ett Azure IoT Central-program kan du välja mellan inbyggda exempel mallar. Du kan också skapa egna programmallar från befintliga IoT Central-program. Du kan sedan använda dina egna programmallar när du skapar nya program.

När du skapar en Programmall innehåller den följande objekt från ditt befintliga program:

- Instrument panelen för standard program, inklusive instrument panelens layout och alla paneler som du har definierat.
- Mallar för enheter, inklusive mätningar, inställningar, egenskaper, kommandon och instrument paneler.
- Uttryck. Alla regel definitioner ingår. Åtgärder, förutom e-poståtgärder, ingår dock inte.
- Enhets uppsättningar, inklusive deras villkor och instrument paneler.

> [!WARNING]
> Om en instrument panel innehåller paneler som visar information om specifika enheter visas **inte den begärda resursen** i det nya programmet. Du måste konfigurera om panelerna för att visa information om enheter i det nya programmet.

När du skapar en Programmall innehåller den inte följande objekt:

- Enheter
- Användare
- Jobb definitioner
- Definitioner av kontinuerliga data exporter

Lägg till dessa objekt manuellt i alla program som skapats från en Programmall.

## <a name="create-an-application-template"></a>Skapa en Programmall

Så här skapar du en Programmall från ett befintligt IoT Central-program:

1. Gå till avsnittet **Administration** i ditt program.
1. Välj **export av program mal len**.
1. Ange ett namn och en beskrivning för mallen på sidan **export av program mal len** .
1. Välj knappen **Exportera** för att skapa program mal len. Nu kan du kopiera den **delnings bara länken** som gör att någon kan skapa ett nytt program från mallen:

![Skapa en Programmall](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Använd en program mall

Om du vill använda en Programmall för att skapa ett nytt IoT Central-program behöver du en tidigare skapad **delnings bara länk**. Klistra in den **delnings bara länken** i webbläsarens Adress fält. Sidan **skapa ett program** visas med din anpassade program mal len vald:

![Skapa ett program från en mall](media/howto-use-app-templates/create-app.png)

Välj din betalnings plan och fyll i de andra fälten i formuläret. Välj sedan **skapa** för att skapa ett nytt IoT Central program från program mal len.

## <a name="manage-application-templates"></a>Hantera programmallar

På sidan **Exportera program mal len** kan du ta bort eller uppdatera program mal len.

Om du tar bort en Programmall kan du inte längre använda den delnings bara länken som skapats tidigare för att skapa nya program.

Om du vill uppdatera program mal len ändrar du mallens namn eller beskrivning på export sidan för **program mal len** . Välj sedan knappen **Exportera** igen. Den här åtgärden skapar en ny **delnings bara länk** och invaliderar alla tidigare **delnings** bara URL-adresser.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder programmallar är det föreslagna nästa steg att lära dig hur du [hanterar IoT Central från Azure Portal](howto-manage-iot-central-from-portal.md)
