---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Bekanta dig med de viktigaste områdena i Azure IoT Central UI som du använder för att skapa, hantera och använda din IoT-lösning.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b905b1e86810b25c4c94072d6cd414b993e2a883
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77426190"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ta en rundtur i Azure IoT Central-användargränssnittet



Den här artikeln beskriver Microsoft Azure IoT Central-användargränssnittet. Du kan använda gränssnittet till att skapa, hantera och använda en Azure IoT Central-lösning och dess anslutna enheter.

Som _lösningsbyggare_använder du Azure IoT Central UI för att definiera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

* ange vilka enhetstyper som kan ansluta till din lösning
* konfigurera regler och åtgärder för dina enheter 
* anpassa användargränssnittet för en _operatör_ som använder din lösning.

Som _operatör_ använder du Azure IoT Central-användargränssnittet till att hantera din Azure IoT Central-lösning. Du kan använda användargränssnittet till att:

* övervaka dina enheter
* konfigurera dina enheter
* felsöka och åtgärda problem med dina enheter.
* Etablera nya enheter.

## <a name="iot-central-homepage"></a>IoT Central hemsida

[IoT Central hemsida](https://aka.ms/iotcentral-get-started) sida är den plats där du kan lära dig mer om de senaste nyheterna och funktionerna som finns på IoT Central, skapa nya program, och se och starta din befintliga ansökan.

> [!div class="mx-imgBorder"]
> ![IoT Central hemsida](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Skapa ett program

I avsnittet Skapa kan du bläddra i listan över branschrelevanta IoT Central-mallar som hjälper dig att komma igång snabbt, eller börja om från början med hjälp av en anpassad appmall.  
> [!div class="mx-imgBorder"]
> ![IoT Central byggsida](media/overview-iot-central-tour/iot-central-build-pnp.png)

Mer information finns i snabbstarten [Skapa ett Azure IoT Central-program.](quick-deploy-iot-central.md)

### <a name="launch-your-application"></a>Starta programmet

Du kan starta ditt IoT Central-program genom att gå till den url som du eller din lösningsbyggare väljer när appen skapas. Du kan också se en lista över alla program som du har åtkomst till i [IoT Central app manager](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![IoT Central apphanterare](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigera i ditt program

När du är inne i ditt IoT-program använder du den vänstra rutan för att komma åt de olika områdena. Du kan expandera eller komprimera den vänstra rutan genom att markera den trekantade ikonen ovanpå fönstret:

> [!NOTE]
> Vilka objekt som visas i den vänstra rutan beror på din användarroll. Läs mer om [hur du hanterar användare och roller](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![vänster ruta](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Instrumentpanelen** visar instrumentpanelen. Som *lösningsbyggare*kan du anpassa den globala instrumentpanelen för dina operatörer. Beroende på deras användarroll kan operatörerna också skapa sina egna personliga instrumentpaneler.
     
     **Med enheter** kan du hantera dina anslutna enheter – verkliga och simulerade.

     **Med enhetsgrupper** kan du visa och skapa logiska samlingar av enheter som anges av en fråga. Du kan spara den här frågan och använda enhetsgrupper via programmet för att utföra massåtgärder.

     **Med regler** kan du skapa och redigera regler för att övervaka dina enheter. Regler utvärderas baserat på enhetstelemetri och utlösa anpassningsbara åtgärder.

     **Med Analytics** kan du skapa anpassade vyer ovanpå enhetsdata för att härleda insikter från ditt program.

     **Med jobb** kan du hantera dina enheter i stor skala genom att köra massåtgärder.

     **Enhetsmallar** är där du skapar och hanterar egenskaperna hos de enheter som ansluter till ditt program.

     **Med dataexport** kan du konfigurera en kontinuerlig export till externa tjänster – till exempel lagring och köer.

     **Administration** är där du kan hantera programmets inställningar, anpassning, fakturering, användare och roller.

     **IoT Central** låter *administratörer* hoppa tillbaka till IoT Centrals apphanterare.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Sök, hjälp, tema och support

Den översta menyn visas på varje sida:

> [!div class="mx-imgBorder"]
> ![Verktygsfält](media/overview-iot-central-tour/toolbar-pnp.png)

* Om du vill söka efter enhetsmallar och enheter anger du ett **sökvärde.**
* Om du vill ändra användargränssnittets språk eller tema väljer du ikonen **Inställningar.** Läs mer om hur du [hanterar dina programinställningar](howto-manage-preferences.md)
* Om du vill logga ut **Account** från programmet väljer du kontoikonen.
* Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. I ett program om den kostnadsfria prisplanen inkluderar supportresurserna åtkomst till [chatt](howto-show-hide-chat.md).

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

> [!NOTE]
> Alternativet att välja mellan ljusa och mörka teman är inte tillgängligt om administratören har konfigurerat ett anpassat tema för programmet.

> [!div class="mx-imgBorder"]
> ![Välja ett tema för användargränssnittet](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Instrumentpanel
> [!div class="mx-imgBorder"]
> ![Instrumentpanel](media/overview-iot-central-tour/dashboard-pnp.png)

* Instrumentpanelen är den första sidan du ser när du loggar in på ditt Azure IoT Central-program. Som *lösningsbyggare*kan du skapa och anpassa flera globala programinstrumentpaneler för andra användare. Läs mer om [att lägga till paneler på instrumentpanelen](howto-add-tiles-to-your-dashboard.md)

* Som *operatör*kan du skapa personliga instrumentpaneler för att övervaka vad du bryr dig om om om din användarroll. Mer information finns i instruktioner för hur du vill att skapa [Azure IoT Central-instrumentpaneler.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Enheter

> [!div class="mx-imgBorder"]
> ![Sidan Enheter](media/overview-iot-central-tour/devices-pnp.png)

På utforskningssidan visas de _enheter_ som finns i ditt Azure IoT Central-program grupperade efter _enhetsmall_. 

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program.
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program.

Mer information finns i snabbstarten För bildskärmen på [dina enheter.](./quick-monitor-devices.md) 

### <a name="device-groups"></a>Enhetsgrupper

> [!div class="mx-imgBorder"]
> ![Sidan Enhetsgrupper](media/overview-iot-central-tour/device-groups-pnp.png)

Enhetsgruppen är en samling relaterade enheter. En *lösningsbyggare* definierar en fråga för att identifiera de enheter som ingår i en enhetsgrupp. Du använder enhetsgrupper för att utföra massåtgärder i programmet. Mer information finns i artikel [använda enhetsgrupper i din Azure IoT Central-programartikel.](tutorial-use-device-groups.md)

### <a name="rules"></a>Regler
> [!div class="mx-imgBorder"]
> ![Sidan Regler](media/overview-iot-central-tour/rules-pnp.png)

På regelsidan kan du definiera regler baserat på enheters telemetri, tillstånd eller händelser. När en regel utlöser kan den utlösa en eller flera åtgärder - till exempel skicka ett e-postmeddelande, meddela ett externt system via webhook-varningar etc. Mer information finns i självstudien [Konfigurera regler.](tutorial-create-telemetry-rules.md) 

### <a name="analytics"></a>Analys

> [!div class="mx-imgBorder"]
> ![Analyssidan](media/overview-iot-central-tour/analytics-pnp.png)

Med analysen kan du skapa anpassade vyer ovanpå enhetsdata för att härleda insikter från ditt program. Mer information finns i artikeln [Skapa analyser för ditt Azure IoT Central-program.](howto-create-analytics.md)

### <a name="jobs"></a>Jobb

> [!div class="mx-imgBorder"]
> ![Sidan Jobb](media/overview-iot-central-tour/jobs-pnp.png)

På jobbsidan kan du köra massenhetshanteringsåtgärder på dina enheter. Du kan uppdatera enhetsegenskaper, inställningar och köra kommandon mot enhetsgrupper. Mer information finns i artikeln [Köra ett jobb](howto-run-a-job.md).

### <a name="device-templates"></a>Enhetsmallar

> [!div class="mx-imgBorder"]
> ![Sidan Enhetsmallar](media/overview-iot-central-tour/templates-pnp.png)

Sidan för enhetsmallar är där en byggare skapar och hanterar enhetsmallarna i programmet. En enhetsmall anger enhetsegenskaper som:

* Telemetri-, tillstånds- och händelsemätningar
* Egenskaper
* Kommandon
* Vyer

*Lösningsverktyget* kan också skapa formulär och instrumentpaneler som operatörer kan använda för att hantera enheter.

Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](howto-set-up-template.md). 

### <a name="data-export"></a>Dataexport
> [!div class="mx-imgBorder"]
> ![Sidan Dataexport](media/overview-iot-central-tour/export-pnp.png)

Med dataexport kan du ställa in dataströmmar, till exempel telemetri, från programmet till externa system. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration
> [!div class="mx-imgBorder"]
> ![Sidan Administration](media/overview-iot-central-tour/administration-pnp.png)

På administrationssidan kan du konfigurera och anpassa ditt IoT Central-program. Här kan du ändra ditt programnamn, URL, teman, hantera användare och roller, skapa API-token och exportera ditt program. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
