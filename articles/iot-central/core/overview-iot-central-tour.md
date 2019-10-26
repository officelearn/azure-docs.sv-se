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
ms.openlocfilehash: 6e375e809308da8be0723fb2fcbdf718e9fb5479
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957771"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ta en rundtur i Azure IoT Central-användargränssnittet

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

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

## <a name="use-the-left-pane"></a>Använd den vänstra rutan

Använd den vänstra rutan för att få åtkomst till olika delar av programmet. Du kan expandera eller komprimera navigerings fältet genom att välja **<** eller **>** :

:::row:::
  :::column span="":::
      ![vänster fönster](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     **Instrument** panelen visar din program instrument panel. Som ett verktyg kan du anpassa instrument panelen för dina operatörer. Användare kan också skapa egna instrument paneler.
    
     **Device Explorer** listar de simulerade och riktiga enheter som är kopplade till varje enhets mall i programmet. Som operatör kan du använda **Device Explorer** till att hantera dina anslutna enheter.
    
     Med **enhets uppsättningar** kan du Visa och skapa enhets uppsättningar. Som operatör kan du skapa enhetsuppsättningar som en logisk samling av enheter som anges av en fråga.
    
     **Analytics** visar analys som härletts från enhets telemetri för enheter och enhets uppsättningar. Som operatör kan du skapa anpassade vyer ovanpå enhetsdata för att kunna härleda insikter från ditt program.
    
     **Jobb** möjliggör hantering av Mass enheter genom att du har skapat och kört jobb för att utföra uppdateringar i stor skala.
    
     **Enhets mallar** visar de verktyg som en Builder använder för att skapa och hantera mallar för enheter.
    
     Med **kontinuerliga data export** kan en administratör konfigurera en kontinuerlig export till andra Azure-tjänster, till exempel lagring och köer.
    
     **Administration** visar de program administrations sidor där en administratör kan hantera program inställningar, användare och roller.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Sök, hjälp och support

Den översta menyn visas på varje sida:

![Verktygsfält](media/overview-iot-central-tour/toolbar.png)

- Ange **ett** sökvärde om du vill söka efter enhets mallar och enheter.
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

* Som operatör kan du skapa anpassade instrument paneler och växla mellan dem och standard instrument panelen. Mer information finns i artikeln [Skapa Azure IoT Central personliga instrument paneler](howto-create-personal-dashboards.md) instruktioner.

## <a name="device-explorer"></a>Enhetsutforskaren

![Sidan Explorer](media/overview-iot-central-tour/explorer.png)

På utforskningssidan visas de _enheter_ som finns i ditt Azure IoT Central-program grupperade efter _enhetsmall_.

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program. Läs mer i informationen om att [definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type.md).
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program. Läs mer i informationen om att [lägga till en ny enhet i Azure IoT Central-programmet](tutorial-add-device.md).

## <a name="device-sets"></a>Enhetsuppsättningar

![Sidan med enhetsuppsättningar](media/overview-iot-central-tour/devicesets.png)

På sidan med _enhetsuppsättningar_ visas de enhetsuppsättningar som har skapats av byggaren. En enhetsuppsättning är en samling relaterade enheter. En byggare definierar en fråga för att identifiera de enheter som ingår i en enhetsuppsättning. Du använder enhetsuppsättningar när du anpassar analyser i ditt program. Mer information finns i artikeln om att [använda enhetsuppsättningar i Azure IoT Central-programmet](howto-use-device-sets.md).

## <a name="analytics"></a>Analyser

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

Sidan för kontinuerlig data export är där en administratör definierar hur data strömmas, t. ex. telemetri, från programmet. Andra tjänster kan lagra exporterade data eller använda dem för analys. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](howto-export-data-blob-storage.md).

## <a name="administration"></a>Administration

![Sidan Administration](media/overview-iot-central-tour/administration.png)

Sidan administration innehåller länkar till de verktyg som en administratör använder, till exempel definiera användare och roller i programmet och anpassa användar gränssnittet. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
