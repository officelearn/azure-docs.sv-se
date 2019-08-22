---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Som byggare bekantar du dig med de viktiga områden i Azure IoT Central-användargränssnittet som du använder när du skapar en IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6c06d789e6a005c0079f889cead6a75f4d0baf19
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875704"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ta en rundtur i Azure IoT Central-användargränssnittet

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver Microsoft Azure IoT Central-användargränssnittet. Du kan använda gränssnittet till att skapa, hantera och använda en Azure IoT Central-lösning och dess anslutna enheter.

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

- ange vilka enhetstyper som kan ansluta till din lösning
- konfigurera regler och åtgärder för dina enheter
- anpassa användargränssnittet för en _operatör_ som använder din lösning.

Som _operatör_ använder du Azure IoT Central-användargränssnittet till att hantera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

- övervaka dina enheter
- konfigurera dina enheter
- felsöka och åtgärda problem med dina enheter.
- Etablera nya enheter.

## <a name="use-the-left-navigation-menu"></a>Använda den vänstra navigeringsmenyn

Använd den vänstra navigerings menyn för att få åtkomst till olika delar av programmet. Du kan expandera eller komprimera navigerings fältet genom att välja **<** eller **>** :

| Meny | Beskrivning |
| ---- | ----------- |
| ![Vänster navigeringsmeny](media/overview-iot-central-tour/navigationbar.png) | <ul><li>På **instrument panelen** visas instrument panelen för programmet. Som ett verktyg kan du anpassa instrument panelen för dina operatörer. Användare kan också skapa egna instrument paneler.</li><li>Knappen **Device Explorer** (Enhetsutforskare) visar simulerade och verkliga enheter som är associerade med varje enhetsmall i programmet. Som operatör kan du använda **Device Explorer** till att hantera dina anslutna enheter.</li><li>Med knappen **Device Sets** (Enhetsuppsättningar) kan du visa och skapa enhetsuppsättningar. Som operatör kan du skapa enhetsuppsättningar som en logisk samling av enheter som anges av en fråga.</li><li>Knappen **Analytics** (Analys) visar analys som härletts från enhetstelemetrin för enheter och enhetsuppsättningar. Som operatör kan du skapa anpassade vyer ovanpå enhetsdata för att kunna härleda insikter från ditt program.</li><li>Med knappen **Jobb** kan du ägna dig åt massenhetshantering genom att skapa och köra jobb och utföra uppdateringar i skala.</li><li>Knappen **Enhetsmallar** visar de verktyg som byggare använder för att skapa och hantera enhetsmallar.</li><li>Knappen **Kontinuerlig dataexport** gör att en administratör kan konfigurera en löpande export till andra Azure-tjänster såsom lagring och köer.</li><li>Knappen **Administration** visar programmets administrationssidor där administratören kan hantera programinställningar, användare och roller.</li></ul> |

## <a name="search-help-and-support"></a>Sök, hjälp och support

Den översta menyn visas på varje sida:

![Verktygsfält](media/overview-iot-central-tour/toolbar.png)

- Ange ett sökvärde om du vill söka efter enhets mallar och enheter.
- Om du vill ändra språket eller temat för användar gränssnittet väljer du ikonen **Inställningar** .
- Om du vill logga ut från programmet väljer du **konto** ikonen.
- Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. I ett utvärderings program inkluderar support resurserna åtkomst till [Live Chat](howto-show-hide-chat.md).

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

![Välja ett tema för användargränssnittet](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> Alternativet att välja mellan ljust och mörkt teman är inte tillgängligt om administratören har konfigurerat ett anpassat tema för programmet.

## <a name="dashboard"></a>Instrumentpanel

![Instrumentpanel](media/overview-iot-central-tour/homepage.png)

* Instrument panelen är den första sidan som visas när du loggar in på ditt Azure IoT Central-program. Som ett verktyg kan du anpassa program instrument panelen för andra användare genom att lägga till paneler. Mer information finns i självstudien om att [anpassa operatörsvyn i Azure IoT Central](tutorial-customize-operator.md).

* Som operatör kan du skapa anpassade instrument paneler och växla mellan dem och standard instrument panelen. Mer information finns i artikeln [skapa och hantera personliga instrument paneler](howto-personalize-dashboard.md) instruktioner.

## <a name="device-explorer"></a>Enhetsutforskaren

![Sidan Explorer](media/overview-iot-central-tour/explorer.png)

På utforskningssidan visas de _enheter_ som finns i ditt Azure IoT Central-program grupperade efter _enhetsmall_.

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program. Läs mer i informationen om att [definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type.md).
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program. Läs mer i informationen om att [lägga till en ny enhet i Azure IoT Central-programmet](tutorial-add-device.md).

## <a name="device-sets"></a>Enhetsuppsättningar

![Sidan med enhetsuppsättningar](media/overview-iot-central-tour/devicesets.png)

På sidan med _enhetsuppsättningar_ visas de enhetsuppsättningar som har skapats av byggaren. En enhetsuppsättning är en samling relaterade enheter. En byggare definierar en fråga för att identifiera de enheter som ingår i en enhetsuppsättning. Du använder enhetsuppsättningar när du anpassar analyser i ditt program. Mer information finns i artikeln om att [använda enhetsuppsättningar i Azure IoT Central-programmet](howto-use-device-sets.md).

## <a name="analytics"></a>Analytics

![Analyssidan](media/overview-iot-central-tour/analytics.png)

På analyssidan visas diagram där du kan se hur enheter som är anslutna till ditt program fungerar. En operatör använder den här sidan för att övervaka och undersöka problem med anslutna enheter. Byggaren kan definiera vilka diagram som ska visas på den här sidan. Mer information finns i artikeln om att [skapa anpassad analys för Azure IoT Central-programmet](howto-use-device-sets.md).

## <a name="jobs"></a>Jobb

![Sidan Jobb](media/overview-iot-central-tour/jobs.png)

På sidan jobb kan du köra hanterings åtgärder för flera enheter på dina enheter. Verktyget använder den här sidan för att uppdatera enhetsegenskaper, inställningar och kommandon. Mer information finns i artikeln [Köra ett jobb](howto-run-a-job.md).

## <a name="device-templates"></a>Enhets mallar

![Sidan Enhetsmallar](media/overview-iot-central-tour/templates.png)

Sidan för enhetsmallar är där en byggare skapar och hanterar enhetsmallarna i programmet. En enhets mal len anger enhets egenskaper som:

- Telemetri, tillstånd och händelse mätningar.
- Inställningar och egenskaper.
- Kommandon.
- Regler som baseras på värden för händelser eller telemetri.

Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Löpande dataexport

![Sidan Kontinuerlig dataexport](media/overview-iot-central-tour/export.png)

Sidan för kontinuerlig data export är där en administratör definierar hur data strömmas, t. ex. telemetri, från programmet. Andra tjänster kan lagra exporterade data eller använda dem för analys. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Administration

![Administrationssida](media/overview-iot-central-tour/administration.png)

Sidan administration innehåller länkar till de verktyg som en administratör använder, till exempel definiera användare och roller i programmet och anpassa användar gränssnittet. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).