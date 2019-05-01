---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Som byggare bekantar du dig med de viktiga områden i Azure IoT Central-användargränssnittet som du använder när du skapar en IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 654528ed6137ee62ea22d552d36fda5ac5331adc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688653"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ta en rundtur i Azure IoT Central-användargränssnittet

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

Använd den vänstra navigeringsmenyn för att komma åt de olika områdena i programmet. Du kan expandera eller komprimera navigeringsfältet genom att välja **<** eller **>**:

| Meny | Beskrivning |
| ---- | ----------- |
| ![Vänster navigeringsmeny](media/overview-iot-central-tour/navigationbar.png) | <ul><li>Den **instrumentpanelen** knappen visar instrumentpanelen för dina program. Du kan anpassa instrumentpanelen för dina operatörer som ett verktyg. Användarna kan också skapa egna instrumentpaneler.</li><li>Knappen **Device Explorer** (Enhetsutforskare) visar simulerade och verkliga enheter som är associerade med varje enhetsmall i programmet. Som operatör kan du använda **Device Explorer** till att hantera dina anslutna enheter.</li><li>Med knappen **Device Sets** (Enhetsuppsättningar) kan du visa och skapa enhetsuppsättningar. Som operatör kan du skapa enhetsuppsättningar som en logisk samling av enheter som anges av en fråga.</li><li>Knappen **Analytics** (Analys) visar analys som härletts från enhetstelemetrin för enheter och enhetsuppsättningar. Som operatör kan du skapa anpassade vyer ovanpå enhetsdata för att kunna härleda insikter från ditt program.</li><li>Med knappen **Jobb** kan du ägna dig åt massenhetshantering genom att skapa och köra jobb och utföra uppdateringar i skala.</li><li>Knappen **Enhetsmallar** visar de verktyg som byggare använder för att skapa och hantera enhetsmallar.</li><li>Knappen **Kontinuerlig dataexport** gör att en administratör kan konfigurera en löpande export till andra Azure-tjänster såsom lagring och köer.</li><li>Knappen **Administration** visar programmets administrationssidor där administratören kan hantera programinställningar, användare och roller.</li></ul> |

## <a name="search-help-and-support"></a>Sök, hjälp och support

Den översta menyn visas på varje sida:

![Verktygsfält](media/overview-iot-central-tour/toolbar.png)

- Om du vill söka efter enhet mallar och enheter, ange en **Search** värde.
- Om du vill ändra språk i Användargränssnittet eller tema, Välj den **inställningar** ikon.
- Om du vill logga ut från programmet, Välj den **konto** ikon.
- Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. I en utvärderingsprogram Supportresurser omfattar tillgång till [live-chatt](howto-show-hide-chat.md).

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

![Välja ett tema för användargränssnittet](media/overview-iot-central-tour/themes.png)

## <a name="dashboard"></a>Instrumentpanel

![Instrumentpanel](media/overview-iot-central-tour/homepage.png)

Instrumentpanelen är den första sidan som visas när du loggar in på ditt Azure IoT Central program. Du kan anpassa instrumentpanelen för program för andra användare genom att lägga till paneler som ett verktyg. Mer information finns i självstudien om att [anpassa operatörsvyn i Azure IoT Central](tutorial-customize-operator.md). Användarna kan också [skapa sina egna personliga instrumentpaneler](howto-personalize-dashboard.md).

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

På sidan Jobb kan du utföra aktiviteter för massenhetshantering för dina enheter. Verktyget använder den här sidan för att uppdatera enhetsegenskaper, inställningar och kommandon. Mer information finns i artikeln [Köra ett jobb](howto-run-a-job.md).

## <a name="device-templates"></a>Enhetsmallar

![Sidan Enhetsmallar](media/overview-iot-central-tour/templates.png)

Sidan för enhetsmallar är där en byggare skapar och hanterar enhetsmallarna i programmet. Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Kontinuerlig dataexport

![Sidan Kontinuerlig dataexport](media/overview-iot-central-tour/export.png)

Sidan för kontinuerlig dataexport är där en administratör definierar hur data, till exempel telemetri, ska exporteras från programmet. Andra tjänster kan lagra exporterade data eller använda dem för analys. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Administration

![Sidan Administration](media/overview-iot-central-tour/administration.png)

Administrationssidan innehåller länkar till de verktyg som en administratör använder till att exempelvis definiera användare och roller i programmet. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).