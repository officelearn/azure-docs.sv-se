---
title: Självstudie om att konfigurera enheter i en fjärrövervakningslösning – Azure | Microsoft Docs
description: Den här självstudien visar dig hur du konfigurerar enheter som är anslutna till Fjärrövervakning-lösningsacceleratorn.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/15/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a2dc5653b7579d9c5db4667d84e6e5b5082abaab
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448082"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Självstudier: Konfigurera enheter som är anslutna till din övervakningslösning

I den här självstudiekursen använder du lösningsacceleratorn Fjärrövervakning för att konfigurera och hantera dina anslutna IoT-enheter. Du lägger till en ny enhet till lösningsacceleratorn och konfigurerar enheten.

Contoso har beställt nya maskiner och utökat en av sina anläggningar. Medan du väntar på att de nya maskinerna ska levereras vill du köra en simulering för att testa lösningens beteende. För att köra simuleringen lägger du till en ny simulerad motor i Fjärrövervakning-lösningsacceleratorn och testar att den simulerade enheten svarar korrekt på konfigurationsuppdateringar. I den här självstudien används simulerade enheter men en enhetsutvecklare kan implementera direktmetoder på en [riktig enhet ansluten till lösningsacceleratorn Fjärrövervakning](iot-accelerators-connecting-devices.md).

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Etablera en simulerad enhet.
> * Testa en simulerad enhet.
> * Konfigurera om en enhet.
> * Organisera dina enheter.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Navigera till sidan **Enheter** i lösningen och klicka sedan på **+ Ny enhet**:

[![Etablera en simulerad enhet](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

I panelen **Ny enhet** väljer du **Simulerad**, lämnar antalet enheter som ska etableras på **1**, väljer enhetsmodellen **Faulty Engine** (Felaktig motor) och väljer sedan **Använd** för att skapa den simulerade enheten:

[![Etablera en simulerad motor](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testa den simulerade enheten

För att testa att den simulerade motorenheten skickar telemetridata och rapporterar egenskapsvärden väljer du den i listan med enheter på sidan **Enheter**. Direktinformation om motorn visas i panelen **Enhetsinformation**:

[![Visa den nya simulerade enheten](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

I **Enhetsinformation** kontrollerar du att den nya enheten skickar telemetridata. Om du vill visa telemetriströmmen för vibrationer från enheten klickar du på **Vibration**:

[![Välja en telemetriström att visa](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Panelen **Enhetsinformation** visar annan information om enheten som taggvärden, metoder som stöds och egenskaperna som rapporteras av enheten.

Om du vill visa detaljerad diagnostik rullar du nedåt i panelen **Enhetsinformation** för att visa avsnittet **Diagnostik**.

## <a name="reconfigure-a-device"></a>Konfigurera om en enhet

Du kan testa att du kan uppdatera motorns konfigurationsegenskaper genom att välja den i enhetslistan på sidan **Enheter**. Klicka sedan på **Jobb** och välj **Egenskaper**. Jobbpanelen visar de egenskapsvärden som kan uppdateras för den valda enheten:

[![Konfigurera om en enhet](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Du kan uppdatera motorns plats genom att ange jobbnamnet **UpdateEngineLocation** (Uppdatera motorns plats), anger longitud **-122.15**, anger platsen **Factory 2** (Fabrik 2), anger latitud **47.62** och klickar på **Använd**:

[![Uppdatera en enhets egenskapsvärde](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Du kan spåra jobbets status genom att klicka på **Visa jobbstatus**:

[![Uppdatera en enhets egenskapsvärde](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

När jobbet har slutförts går du till sidan **Instrumentpanel**. Motorn visas på kartan på den nya platsen:

[![Visa motorns plats](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organisera dina enheter

För att göra det enklare som operatör att organisera och hantera dina enheter taggar du dem med ett teamnamn. Contoso har två olika team för fältserviceaktiviteter:

* Teamet Smart Vehicle hanterar lastbilar och prototyper.
* Teamet Smart Building hanterar kylaggregat, hissar och motorer.

Du kan visa alla dina enheter genom att gå till sidan **Enheter** och välja filtret **Alla enheter**:

[![Visa alla enheter](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Lägga till taggar

Välj alla enheter av typen **Trucks** (Lastbilar) och **Prototyping** (Prototyper). Klicka sedan på **Jobb**.

I panelen **Jobs** (Jobb) väljer du **Tagg**. Ange jobbnamnet till **AddConnectedVehicleTag** (Lägg till ansluten fordonstagg) och lägg sedan till en texttagg med namnet **FieldService** (Fältservice) med värdet **ConnectedVehicle** (Anslutet fordon). Klicka sedan på **Använd**:

[![Lägga till tagg för prototyper och lastbilar](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

På enhetssidan väljer du alla enheter av typen **Chiller** (Kylaggregat), **Elevator** (Hiss) och **Motor**. Klicka sedan på **Jobb**.

I panelen **Jobs** (Jobb) väljer du **Tagg**. Ange jobbnamnet till **AddSmartBuildingTag** (Lägg till Smart Building-tagg) och lägg sedan till en texttagg med namnet **FieldService** (Fältservice) med värdet **SmartBuilding** (Smart byggnad). Klicka sedan på **Använd**:

[![Lägga till tagg för kylaggregat, hissar och motorer](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Skapa filter

Nu kan du använda taggvärdena till att skapa filter. På sidan **Enheter** klickar du på **Hantera enhetsgrupper**:

[![Hantera enhetsgrupper](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Skapa ett textfilter som använder taggnamnet **FieldService** (Fältservice) och värdet **SmartBuilding** i villkoret. Spara filtret som **Smart Building**:

[![Skapa ett Smart Building-filter](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Skapa ett textfilter som använder taggnamnet **FieldService** (Fältservice) och värdet **ConnectedVehicle** (Anslutet fordon) i villkoret. Spara filtret som **Connected Vehicle** (Anslutet fordon).

[![Skapa filter för anslutna fordon](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Nu kan Contoso-operatören fråga efter enheter baserat på driftsteamet:

[![Skapa filter för anslutna fordon](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen visades hur du konfigurerar och hanterar enheter anslutna till lösningsacceleratorn Fjärrövervakning. Fortsätt till nästa självstudie om du vill lära dig att använda lösningsacceleratorn till att utföra en rotorsaksanalys för en oväntad avisering.

> [!div class="nextstepaction"]
> [Utföra en rotorsaksanalys för en avisering](iot-accelerators-remote-monitoring-root-cause-analysis.md)
