---
title: Skapa en app för kontinuerlig övervakning av patienter med Azure IoT Central | Microsoft Docs
description: Lär dig att bygga ett kontinuerligt program för patient övervakning med hjälp av Azure IoT Central programmallar.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021295"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Självstudie: Distribuera och genom gång av en kontinuerlig app-mall för patient övervakning



Den här självstudien visar dig som Solution Builder och hur du kommer igång genom att distribuera en IoT Central kontinuerlig övervaknings program mal len för patienter. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kan göra härnäst.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Programmall
> * Gå igenom program mal len

## <a name="create-an-application-template"></a>Skapa en Programmall

Gå till [webbplatsen för Azure IoT Central Application Manager](https://apps.azureiotcentral.com/). Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **hälso vård** . 

>[!div class="mx-imgBorder"] 
>![App Manager-hälsovård](media/app-manager-health.png)

Klicka på knappen **skapa app** för att börja skapa ditt program och logga sedan in med ett Microsoft personal-, arbets-eller skol konto. Det tar dig till sidan **nytt program** .

![Skapa program hälso vård](media/app-manager-health-create.png)

![Skapa fakturerings information för program hälso vård](media/app-manager-health-create-billinginfo.png)

Så här skapar du ditt program:

1. Azure IoT Central föreslår automatiskt ett program namn baserat på mallen som du har valt. Du kan acceptera namnet eller ange ett eget vänligt program namn, till exempel **kontinuerlig övervakning av patienter**. Azure IoT Central genererar också ett unikt URL-prefix åt dig baserat på programmets namn. Du är kostnads fri att ändra detta URL-prefix till något mer minnes värt om du vill.

2. Du kan välja om du vill skapa programmet med hjälp av den *kostnads fria* pris planen eller någon av *standard* pris avtalen. Program som du skapar med hjälp av den kostnads fria planen är kostnads fria i sju dagar innan de går ut och ger upp till fem kostnads fria enheter. Du kan när som helst flytta ett program från den kostnads fria planen till en standard pris plan innan den upphör att gälla. Om du väljer den kostnads fria planen måste du ange din kontakt information och välja om du vill få information och tips från Microsoft. Program som du skapar med hjälp av en standard plan stöder upp till två kostnads fria enheter och kräver att du anger din prenumerations information för Azure för fakturering.

3. Klicka på **skapa** längst ned på sidan för att distribuera programmet.

## <a name="walk-through-the-application-template"></a>Gå igenom program mal len

### <a name="dashboards"></a>Instrumentpaneler

När du har distribuerat app-mallen hamnar du först på **lamna instrument panel för patient övervakning**. Lamna sjukvård är ett fiktivt sjukhus system som innehåller två sjukhus: Sparbanken sjukhus och Burkville sjukhus. På instrument panelen för den här operatorn för Sparbanken kommer du att se information och telemetri om enheterna i den här mallen tillsammans med en uppsättning kommandon, jobb och åtgärder som du kan vidta. Från instrument panelen kan du:

* Se telemetri och egenskaper för enheten, till exempel enhetens **batteri nivå** eller dess **anslutnings** status.

* Visa **våningsplanet** och platsen för den smarta viktigare korrigerings enheten.

* **Reetablering** av den smarta viktiga korrigerings filen för en ny patient.

* Se ett exempel på en **instrument panel** för en leverantör som ett sjukhus lag kan se för att spåra sina patienter.

* Ändra enhetens **patient status** för att ange om enheten används för ett pågående eller ett fjärrscenario.

>[!div class="mx-imgBorder"] 
>![Lamna in-patient](media/lamna-in-patient.png)

Du kan också klicka på **gå till instrument panelen för fjär patienter** för att se den andra operatorn för instrument panelen som används för Burkville sjukhus. Den här instrument panelen innehåller en liknande uppsättning åtgärder, telemetri och information. Dessutom kan du se att flera enheter används och att du kan **Uppdatera den inbyggda program varan** på var och en.

>[!div class="mx-imgBorder"] 
>![Lamna-fjärr](media/lamna-remote.png)

På båda instrument panelerna kan du alltid länka tillbaka till den här dokumentationen.

### <a name="device-templates"></a>Enhets mallar

Om du klickar på fliken **enhets mallar** ser du att det finns två olika enhets typer som är en del av mallen:

* **Smart Vital-korrigering**: den här enheten representerar en korrigering som mäter olika typer av viktiga tecken. Den kan användas för att övervaka patienter i och utanför sjukhus. Om du klickar på mallen, ser du att förutom att skicka enhets data, till exempel batteri nivå och enhets temperatur, så skickar korrigeringen även patient hälso data, till exempel andnings frekvensen och blod trycket.

* **Smart Knee-klammer**: den här enheten representerar en Knee-klammer som patienter kan använda vid återställning från en Knee. Om du klickar på den här mallen ser du funktioner som rörelse-och accelerations intervall, förutom enhets data.

>[!div class="mx-imgBorder"] 
>![Enhets mal len Smart Vital patch](media/smart-vitals-device-template.png)

Om du klickar på fliken **enhets grupper** ser du också att dessa enhets mallar automatiskt har enhets grupper skapade.

### <a name="rules"></a>Regler

När du hoppar till fliken regler visas tre regler som finns i program mal len:

* **Klammerparentes temperatur hög**: den här regeln utlöses när enhetens temperatur på den smarta Knee-klammern är större än 95&deg;F över ett 5-minuters fönster. Du kan använda den här regeln för att meddela patient-och sjukvårds teamet, och att fjärrlagra enheten.

* **Identifierad**: den här regeln utlöses om en patient befinner sig. Du kan använda den här regeln för att konfigurera en åtgärd för att distribuera ett drifts team för att hjälpa patienten som har fallit.

* **Korrigerings batteri låg**: den här regeln utlöses när batteri nivån på enheten hamnar under 10%. Du kan använda den här regeln för att utlösa en avisering till patienten att debitera enheten.

>[!div class="mx-imgBorder"] 
>![Hög regel för klammerns temperatur](media/brace-temp-rule.png)

### <a name="devices"></a>Enheter

Klicka på fliken **enheter** och välj sedan en instans av den **smarta Knee-klammern**. Du ser att det finns tre vyer för att utforska information om den specifika enhet som du har valt. Dessa vyer skapas och publiceras när du skapar enhets mal len för enheten, vilket innebär att de kommer att vara konsekventa över alla enheter som du ansluter eller simulerar.

I vyn **instrument panel** får du en översikt över telemetri och egenskaper som kommer från den enhet som är inriktad på operatör.

På fliken **Egenskaper** kan du redigera moln egenskaper och egenskaper för läsa/skriva enhet.

På fliken **kommandon** kan du köra kommandon som har modeller ATS som en del av din enhets mall.

>[!div class="mx-imgBorder"] 
>![Vyer för Knee-klammer](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort programmet genom att gå till **Administration > program inställningar** och klicka på **ta bort**.

>[!div class="mx-imgBorder"] 
>![Ta bort app](media/admin-delete.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar en instrument panel för provider som ansluter till ditt IoT Central-program.

> [!div class="nextstepaction"]
> [Bygg en instrument panel för Provider](howto-health-data-triage.md)