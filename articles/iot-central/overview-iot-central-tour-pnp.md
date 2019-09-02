---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Som byggare bekantar du dig med de viktiga områden i Azure IoT Central-användargränssnittet som du använder när du skapar en IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4a0c9d16474ddf032ff88382bc240713bc734ff8
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211899"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Ta en rundtur i Azure IoT Central UI (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver Microsoft Azure IoT Central-användargränssnittet. Du kan använda gränssnittet till att skapa, hantera och använda en Azure IoT Central-lösning och dess anslutna enheter.

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

* ange vilka enhetstyper som kan ansluta till din lösning
* konfigurera regler och åtgärder för dina enheter
* anpassa användargränssnittet för en _operatör_ som använder din lösning.

Som _operatör_ använder du Azure IoT Central-användargränssnittet till att hantera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

* övervaka dina enheter
* konfigurera dina enheter
* felsöka och åtgärda problem med dina enheter.
* Etablera nya enheter.

## <a name="use-the-left-navigation-menu"></a>Använda den vänstra navigeringsmenyn

Använd den vänstra navigerings menyn för att få åtkomst till olika delar av programmet. Du kan expandera eller komprimera navigerings fältet genom att välja **<** eller **>** :

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::

      **Dashboard** displays your application dashboard. As a builder, you can customize the dashboard for your operators. Users can also create their own  dashboards.
    
      **Devices** lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.
    
      **Device groups** lets you view and create device groups. As an operator, you can create device groups as a logical collections of devices specified by a query.

      **Rules** lets you edit rules that fire based on device telemetry and trigger customizable actions.
    
      **Analytics** shows analytics derived from device telemetry for devices and device groups. As an operator, you can create custom views on top of device data to derive insights from your application.
    
      **Jobs** enables bulk device management by having you create and run jobs to update your devices at scale.
    
      **Device templates** shows the tools a builder uses to create and manage device templates.
    
      **Data export** enables an administrator to configure a continuous export to other Azure services such as storage and queues.
    
      **Administration** shows the application administration pages where an administrator can manage application settings, users, and roles.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Sök, hjälp och support

Den översta menyn visas på varje sida:

![Verktygsfält](media/overview-iot-central-tour-pnp/toolbar.png)

* Ange ett sökvärde om du vill söka efter enhets mallar och enheter.
* Om du vill ändra språket eller temat för användar gränssnittet väljer du ikonen **Inställningar** .
* Om du vill logga ut från programmet väljer du **konto** ikonen.
* Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. I ett utvärderings program inkluderar support resurserna åtkomst till [Live Chat](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

![Välja ett tema för användargränssnittet](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> Alternativet att välja mellan ljust och mörkt teman är inte tillgängligt om administratören har konfigurerat ett anpassat tema för programmet.

## <a name="dashboard"></a>Instrumentpanel

![Instrumentpanel](media/overview-iot-central-tour-pnp/homepage.png)

* Instrument panelen är den första sidan som visas när du loggar in på ditt Azure IoT Central-program. Som ett verktyg kan du anpassa program instrument panelen för andra användare genom att lägga till paneler. Mer information finns i själv studie kursen [Konfigurera en enhets mall](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

* Som operatör kan du skapa anpassade instrument paneler och växla mellan dem och standard instrument panelen. Mer information finns i artikeln [skapa och hantera personliga instrument paneler](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) instruktioner.

## <a name="devices"></a>Enheter

![Sidan enheter](media/overview-iot-central-tour-pnp/explorer.png)

På utforskningssidan visas de _enheter_ som finns i ditt Azure IoT Central-program grupperade efter _enhetsmall_.

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program. Läs mer i informationen om att [definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program. Läs mer i informationen om att [lägga till en ny enhet i Azure IoT Central-programmet](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-groups"></a>Enhets grupper

![Sidan enhets grupper](media/overview-iot-central-tour-pnp/devicesets.png)

Sidan _enhets grupper_ visar enhets grupper som skapats av verktyget. En enhets grupp är en samling relaterade enheter. Ett verktyg definierar en fråga för att identifiera de enheter som ingår i en enhets grupp. Du använder enhets grupper när du anpassar analysen i ditt program. Läs mer i [använda enhets grupper i Azure IoT Central program](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) -artikeln.

## <a name="rules"></a>Regler

![Sidan regler](media/overview-iot-central-tour-pnp/rules.png)

På sidan regler kan du definiera regler baserat på telemetri, enhets tillstånd eller enhets händelser. När en regel utlöses kan den utlösa en åtgärd, till exempel att skicka ett e-postmeddelande till en operatör. Verktyget använder den här sidan för att skapa och hantera regler. Mer information finns i själv studie kursen [Konfigurera regler och åtgärder för dina enheter i Azure IoT Central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="analytics"></a>Analytics

![Analyssidan](media/overview-iot-central-tour-pnp/analytics.png)

På analyssidan visas diagram där du kan se hur enheter som är anslutna till ditt program fungerar. En operatör använder den här sidan för att övervaka och undersöka problem med anslutna enheter. Byggaren kan definiera vilka diagram som ska visas på den här sidan. Mer information finns i artikeln om att [skapa anpassad analys för Azure IoT Central-programmet](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="jobs"></a>Jobb

![Sidan Jobb](media/overview-iot-central-tour-pnp/jobs.png)

På sidan jobb kan du köra hanterings åtgärder för flera enheter på dina enheter. Verktyget använder den här sidan för att uppdatera enhetsegenskaper, inställningar och kommandon. Mer information finns i artikeln [Köra ett jobb](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-templates"></a>Enhets mallar

![Sidan enhets mallar](media/overview-iot-central-tour-pnp/templates.png)

Sidan för enhetsmallar är där en byggare skapar och hanterar enhetsmallarna i programmet. En enhets mal len anger enhets egenskaper som:

* Telemetri, tillstånd och händelse mätningar.
* Egenskaperna.
* Kommandon.

Verktyget kan också skapa formulär och instrument paneler för operatörer som ska användas för att hantera enheter.

Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-export"></a>Data export

![Sidan data export](media/overview-iot-central-tour-pnp/export.png)

Sidan data export är där en administratör definierar hur data strömmas, t. ex. telemetri, från programmet. Andra tjänster kan lagra exporterade data eller använda dem för analys. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="administration"></a>Administration

![Administrationssida](media/overview-iot-central-tour-pnp/administration.png)

Sidan administration innehåller länkar till de verktyg som en administratör använder, till exempel definiera användare och roller i programmet och anpassa användar gränssnittet. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
