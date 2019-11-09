---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Bekanta dig med viktiga områden i Azure IoT Central UI som du använder för att skapa, hantera och använda din IoT-lösning.
author: lmasieri
ms.author: lmasieri
ms.date: 10/21/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: f8aa467f95bb97c42e726d1392deca53e15df624
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893958"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Ta en rundtur i Azure IoT Central UI (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver Microsoft Azure IoT Central-användargränssnittet. Du kan använda gränssnittet till att skapa, hantera och använda en Azure IoT Central-lösning och dess anslutna enheter.

Som _Solution Builder_använder du Azure IoT Central-användargränssnittet för att definiera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

* ange vilka enhetstyper som kan ansluta till din lösning
* konfigurera regler och åtgärder för dina enheter 
* anpassa användargränssnittet för en _operatör_ som använder din lösning.

Som _operatör_ använder du Azure IoT Central-användargränssnittet till att hantera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

* övervaka dina enheter
* konfigurera dina enheter
* felsöka och åtgärda problem med dina enheter.
* Etablera nya enheter.

## <a name="iot-central-homepage"></a>IoT Central start sida

Sidan [IoT Central start](https://aka.ms/iotcentral-get-started) sida är den plats där du kan lära dig mer om de senaste nyheterna och funktionerna som finns på IoT Central, skapa nya program och se och starta ditt befintliga program.

> [!div class="mx-imgBorder"]
> ![IoT Central start sida](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Skapa ett program

I avsnittet build kan du bläddra i listan över bransch relevanta IoT Central mallar som hjälper dig att komma igång snabbt eller börja från början med en anpassad app-mall.  
> [!div class="mx-imgBorder"]
> Sidan ![IoT Central build](media/overview-iot-central-tour/iot-central-build-pnp.png)

Mer information finns i snabb starten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md) .

### <a name="launch-your-application"></a>Starta ditt program

Du kan starta ditt IoT Central-program genom att gå till URL: en som du eller Solution Builder väljer när du skapar en app. Du kan också se en lista över alla program som du har åtkomst till i [IoT Central App Manager](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![IoT Central App Manager](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigera i ditt program

När du är i IoT-programmet använder du den vänstra rutan för att få åtkomst till de olika områdena. Du kan expandera eller komprimera navigerings fältet genom att välja den tredimensionella ikonen överst i navigerings fältet:

> [!NOTE]
> De objekt som visas i navigerings fältet beror på din användar roll. Lär dig mer om att [Hantera användare och roller](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![vänstra rutan](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Instrument** panelen visar din program instrument panel. Som *Solution Builder*kan du anpassa den globala instrument panelen för dina operatörer. Beroende på deras användar roll kan operatörer även skapa egna personliga instrument paneler.
     
     Med **enheter** kan du hantera anslutna enheter – verkliga och simulerade enheter.

     Med **enhets grupper** kan du Visa och skapa logiska samlingar med enheter som anges i en fråga. Du kan spara den här frågan och använda enhets grupper via programmet för att utföra Mass åtgärder.

     Med **regler** kan du skapa och redigera regler för att övervaka dina enheter. Reglerna utvärderas utifrån enhetens telemetri och utlöser anpassningsbara åtgärder.

     Med **Analytics** kan du skapa anpassade vyer ovanpå enhets data för att få insikter från ditt program.

     Med **jobb** kan du hantera dina enheter i stor skala genom att köra Mass åtgärder.

     Med **enhets mallar** kan du skapa och hantera egenskaperna för de enheter som ansluter till ditt program.

     Med **data export** kan du konfigurera en kontinuerlig export till externa tjänster, till exempel lagring och köer.

     **Administration** är där du kan hantera programmets inställningar, anpassning, fakturering, användare och roller.

     **IoT Central** låter *Administratörer* gå tillbaka till IoT Centrals App Manager.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Sök, hjälp, tema och support

Den översta menyn visas på varje sida:

> [!div class="mx-imgBorder"]
> ![verktygsfältet](media/overview-iot-central-tour/toolbar-pnp.png)

* Ange **ett** sökvärde om du vill söka efter enhets mallar och enheter.
* Om du vill ändra språket eller temat för användar gränssnittet väljer du ikonen **Inställningar** . Läs mer om hur [du hanterar dina program inställningar](../core/howto-manage-preferences.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
* Om du vill logga ut från programmet väljer du **konto** ikonen.
* Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. I ett utvärderings program inkluderar support resurserna åtkomst till [Live Chat](../core/howto-show-hide-chat.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

> [!NOTE]
> Alternativet att välja mellan ljust och mörkt teman är inte tillgängligt om administratören har konfigurerat ett anpassat tema för programmet.

> [!div class="mx-imgBorder"]
> ![välja ett tema för användar gränssnittet](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Instrumentpanel
> [!div class="mx-imgBorder"]
> ![Instrumentpanel](media/overview-iot-central-tour/dashboard-pnp.png)

* Instrument panelen är den första sidan som visas när du loggar in på ditt Azure IoT Central-program. Som *Solution Builder*kan du skapa och anpassa flera globala program instrument paneler för andra användare. Lär dig mer om att [lägga till paneler på din instrument panel](../core/howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)

* Som *operatör*, om din användar roll tillåter det, kan du skapa personliga instrument paneler för att övervaka vad du bryr dig om. Mer information finns i artikeln [Skapa Azure IoT Central personliga instrument paneler](../core/howto-create-personal-dashboards.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) instruktioner.

### <a name="devices"></a>Enheter

> [!div class="mx-imgBorder"]
> Sidan ![enheter](media/overview-iot-central-tour/devices-pnp.png)

På utforskningssidan visas de _enheter_ som finns i ditt Azure IoT Central-program grupperade efter _enhetsmall_. 

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program.
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program.

Mer information finns i [övervaka dina enheter](./quick-monitor-devices.md) snabb start. 

### <a name="device-groups"></a>Enhets grupper

> [!div class="mx-imgBorder"]
> Sidan ![enhets grupper](media/overview-iot-central-tour/device-groups-pnp.png)

Enhets grupp är en samling relaterade enheter. En *Solution Builder* definierar en fråga för att identifiera de enheter som ingår i en enhets grupp. Du kan använda enhets grupper för att utföra Mass åtgärder i ditt program. Läs mer i [använda enhets grupper i Azure IoT Central program](tutorial-use-device-groups.md) -artikeln.

### <a name="rules"></a>Regler
> [!div class="mx-imgBorder"]
> Sidan ![regler](media/overview-iot-central-tour/rules-pnp.png)

På sidan regler kan du definiera regler baserat på enheternas telemetri, tillstånd eller händelser. När en regel utlöses kan den utlösa en eller flera åtgärder, till exempel att skicka ett e-postmeddelande, meddela ett externt system via webhook-aviseringar osv. Mer information finns i själv studie kursen [Konfigurera regler](tutorial-create-telemetry-rules.md) . 

### <a name="analytics"></a>Analys

> [!div class="mx-imgBorder"]
> ![Analytics-sida](media/overview-iot-central-tour/analytics-pnp.png)

Med Analytics kan du skapa anpassade vyer ovanpå enhets data för att få insikter från ditt program. Mer information finns i artikeln [skapa analyser för ditt Azure IoT Central-program](howto-create-analytics.md) .

### <a name="jobs"></a>Jobb

> [!div class="mx-imgBorder"]
> Sidan ![jobb](media/overview-iot-central-tour/jobs-pnp.png)

På sidan jobb kan du köra hanterings åtgärder för flera enheter på dina enheter. Du kan uppdatera enhets egenskaper, inställningar och köra kommandon mot enhets grupper. Mer information finns i artikeln [Köra ett jobb](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

### <a name="device-templates"></a>Enhets mallar

> [!div class="mx-imgBorder"]
> Sidan ![Device templates](media/overview-iot-central-tour/templates-pnp.png)

Sidan för enhetsmallar är där en byggare skapar och hanterar enhetsmallarna i programmet. En enhets mal len anger enhets egenskaper som:

* Telemetri, tillstånd och händelse mätningar
* Egenskaper
* Kommandon
* Vyer

*Solution Builder* kan också skapa formulär och instrument paneler för operatörer som ska användas för att hantera enheter.

Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](howto-set-up-template.md). 

### <a name="data-export"></a>Data export
> [!div class="mx-imgBorder"]
> ![data export sida](media/overview-iot-central-tour/export-pnp.png)

Med data export kan du konfigurera data strömmar, till exempel telemetri, från programmet till externa system. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration
> [!div class="mx-imgBorder"]
> ![administrations sida](media/overview-iot-central-tour/administration-pnp.png)

På sidan Administration kan du konfigurera och anpassa ditt IoT Central-program. Här kan du ändra ditt program namn, URL, de, hantera användare och roller, skapa API-token och exportera ditt program. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
