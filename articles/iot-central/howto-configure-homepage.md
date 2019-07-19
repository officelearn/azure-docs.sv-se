---
title: Konfigurera program instrument panelen för Azure IoT Central | Microsoft Docs
description: Som ett verktyg kan du läsa om hur du konfigurerar standard instrument panelen för Azure IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850218"
---
# <a name="configure-the-application-dashboard"></a>Konfigurera program instrument panelen

**Instrument panelen** är den sida som läses in när användare som har åtkomst till programmet navigerar till programmets URL. Om du har valt antingen **exempel** mal len contoso eller **exempel Devkits** för att skapa ditt program, har ditt program en fördefinierad instrument panel. Om du väljer program mal len **anpassad app** , är din instrument panel tom.

> [!NOTE]
> Användare kan också [skapa egna personliga instrument paneler](howto-personalize-dashboard.md) som ska användas i stället för standard instrument panelen.

## <a name="add-tiles"></a>Lägg till paneler

På följande skärm bild visas instrument panelen i ett program som skapats från **exempel contoso** -mallen. Om du vill anpassa standard instrument panelen för programmet väljer du **Redigera** högst upp till höger på sidan.

![Instrument panel för program som baseras på mallen "exempel contoso"](media/howto-configure-homepage/image1a.png)

När du väljer **Redigera**öppnas panelen instrument panel bibliotek. Biblioteket innehåller panelerna och de primitiver som du kan använda för att anpassa instrument panelen.

![Instrument panels bibliotek](media/howto-configure-homepage/image2a.png)

Du kan till exempel lägga till en panel med **enhets inställningar och egenskaper** för att visa ett urval av aktuella inställningar och egenskaps värden för en enhet. Det gör du genom att först välja en **enhets mal len** och sedan välja en **enhets instans**. Efter detta ger du panelen ett namn och väljer en **inställning** eller en **egenskap** som ska visas. Följande skärm bild visar inställningar och egenskaper som du väljer att lägga till i panelen. Välj **färdig** om du vill spara ändringen på instrument panelen.

![Formuläret "Konfigurera enhets information" med information om inställningar och egenskaper](media/howto-configure-homepage/image3a.png)

Nu när en operatör visar standard instrument panelen för programmet visas den nya panelen med inställningen **Ange temperatur** för enheten:

![Fliken instrument panel med de inställningar och egenskaper som visas för panelen](media/howto-configure-homepage/image4a.png)

Du kan utforska andra panel typer i biblioteket för att lära dig hur du anpassar standard instrument panelen ytterligare.

Mer information om hur du använder paneler i Azure IoT Central finns i [använda paneler i instrument paneler](howto-use-tiles.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din Azure IoT Central standard instrument panel kan du:

> [!div class="nextstepaction"]
> [Lär dig hur du förbereder och laddar upp bilder](howto-prepare-images.md)
