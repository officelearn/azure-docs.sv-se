---
title: Prova en molnbaserad IoT-fjärrövervakningslösning – Azure | Microsoft Docs
description: I den här snabbstarten distribuerar du Azure IoT-lösningsacceleratorn Fjärrövervakning och loggar in för att använda lösningsinstrumentpanelen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/25/2019
ms.author: dobett
ms.openlocfilehash: 32e2d3f9e8bbd63944188355774558ca5ea7bd9d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "60890168"
---
# <a name="quickstart-try-a-cloud-based-remote-monitoring-solution"></a>Snabbstart: Testa en molnbaserad fjärrövervakningslösning

Den här snabbstarten visar hur du distribuerar Azure IoT-acceleratorn Fjärrövervakningslösning. I den här molnbaserade lösningen använder du **instrumentpanelssidan** till att visualisera simulerade enheter på en karta så svarar sidan **Underhåll** på en tryckvarning för ett simulerat kylaggregat. Du kan använda den här lösningsacceleratorn som utgångspunkt för en egen implementering eller som utbildningsverktyg.

I den initiala distributionen konfigureras lösningsacceleratorn för ett företag med namnet Contoso. Som operatör på Contoso hanterar du olika enheter, till exempel kylaggregat, som distribuerats i olika fysiska miljöer. Ett kylaggregat skickar telemetri om temperatur, luftfuktighet och tryck till acceleratorn för fjärrövervakningslösningen.

Den här snabb starten distribuerar en **grundläggande** version av Solution Accelerator för test-och demonstrations syfte som minimerar kostnaderna. Mer information om de olika versioner som du kan distribuera finns i [Basic-och standard-distributioner](iot-accelerators-remote-monitoring-deploy-cli.md#basic-and-standard-deployments).

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="deploy-the-solution"></a>Distribuera lösningen

När du distribuerar lösningsacceleratorn till Azure-prenumerationen måste du ange några konfigurationsalternativ.

Logga in på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) med dina Azure-kontouppgifter.

Klicka på panelen **Fjärrövervakning**. Klicka på **Testa nu** på sidan **Fjärrövervakning**:

![Välj Fjärrövervakning](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

Välj **C# mikrotjänster** som **distributions alternativ**. Java-och C#-implementeringarna har samma funktioner.

Ange ett unikt **lösningsnamn** för acceleratorn för fjärrövervakningslösningen. I den här snabbstarten kallar vi den för **contoso-rm**.

Välj den **prenumeration** och **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. För den här snabbstarten använder vi **USA, östra**.
Du kan välja **Visual Studio Enterprise**, men du måste vara en [global administratör eller användare](iot-accelerators-permissions.md) för att göra det.

Starta distributionen genom att trycka på **Skapa**. Processen tar minst fem minuter att köra:

![Information om fjärrövervakningslösningen](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in till lösningen

När distributionen till Azure-prenumerationen är klar visas en grön bockmarkering och **Redo** på lösningspanelen. Du kan nu logga in på instrumentpanelen för acceleratorn Fjärrövervakningslösning.

På sidan **Etablerade lösningar** klickar du på acceleratorn för fjärrövervakningslösningen:

![Välj en ny lösning](./media/quickstart-remote-monitoring-deploy/choosenew.png)

Du kan visa information om acceleratorn för fjärrövervakningslösningen på panelen som visas. Välj **Go to your solution accelerator** (Gå till din lösningsaccelerator) för att visa acceleratorn Fjärrövervakningslösning:

![Lösningens panel](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Klicka på **Acceptera** för att acceptera begäran om behörigheter som acceleratorn för fjärrövervakningslösningen visar i webbläsaren:

[![Instrumentpanel för lösningen](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Visa dina enheter

På lösningens instrumentpanel visas följande information om Contosos simulerade enheter:

* Panelen **Enhetsstatistik** visar sammanfattningsinformation om aviseringar och totalt antal enheter. I standarddistributionen har Contoso 10 simulerade enheter av olika typ.

* Panelen **Enhetsplatser** visar var enheterna finns fysiskt. Färgen på nålen visar när det finns aviseringar från enheten.

* Panelen **Aviseringar** visar information om aviseringar från enheterna.

* Panelen **Telemetri** visar telemetridata från enheterna. Du kan visa olika telemetriströmmar genom att klicka på telemetrityperna högst upp.

* Panelen **Analys** visar kombinerad information om aviseringarna från enheterna.

## <a name="respond-to-an-alert"></a>Svara på en avisering

Som operatör på Contoso kan du övervaka dina enheter via lösningens instrumentpanel. Panelen **Enhetsstatistik** visar att det har varit ett antal kritiska aviseringar och panelen **Aviseringar** visar att de flesta kommer från kylaggregatet. För Contosos kylaggregat visar ett inre tryck på över 250 PSI att enheten inte fungerar som den ska.

### <a name="identify-the-issue"></a>Identifiera problemet

På sidan **Instrumentpanel**, på panelen **Aviseringar** ser du att aviseringen **Chiller pressure too high** (Trycket i kylaggregatet är för högt). Kylaggregatet har en röd nål på kartan (du kanske måste panorera och zooma på kartan):

[![Instrument panelen visar tryck avisering och enhet på kartan](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

På panelen **Aviseringar** klickar du på **...** i kolumnen **Utforska** bredvid regeln **Chiller pressure too high** (Trycket i kylaggregatet är för högt). Den här åtgärden tar dig till sidan **Underhåll** där du kan visa informationen för regeln som utlöste aviseringen.

Underhållssidan för **Chiller pressure too high** (Trycket i kylaggregatet är för högt) visar information om regeln som utlöste aviseringarna. På sidan listas även när aviseringarna förekom och vilken enhet som utlöste dem:

[![Underhålls sidan visar en lista över aviseringar som har utlösts](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Nu har du identifierat problemet som utlöste aviseringen och på vilken enhet som problemet finns. Som operatör är nästa steg att bekräfta aviseringen och åtgärda problemet.

### <a name="fix-the-issue"></a>Åtgärda problemet

Visa för andra operatörer att du nu arbetar med aviseringen genom att välja den och ändra **aviseringens status** till **Bekräftad**:

[![Välj och bekräfta aviseringen](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

Värdet i statuskolumnen ändras till **Bekräftad**.

Åtgärda kylaggregat genom att rulla ned till **Relaterad information**, välja kylaggregatet i listan **Enheter med aviseringar** och välj sedan **Jobb**:

[![Välj enheten och Schemalägg en åtgärd](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

På panelen **Jobb** väljer du **Kör metod** och sedan metoden **EmergencyValveRelease**. Lägg till Jobbnamnet **ChillerPressureRelease** och klicka på **Använd**. Inställningarna skapar ett jobb åt dig som körs direkt.

Om du vill se jobbstatusen går du tillbaka till sidan **Underhåll** och visar listan över jobb i vyn **Jobb**. Du kan behöva vänta några sekunder innan du kan se jobbet har körts:

[![Status för jobben i jobbvy](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>Kontrollera att trycket är normalt igen

Om du vill visa trycktelemetrin för kylaggregatet går du till sidan **Instrumentpanel**, väljer **Pressure** (Tryck) i telemetripanelen och bekräftar att trycket för **chiller-02.0** är normalt igen:

[![Tryck tillbaka till normal](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Stäng incidenten genom att gå till sidan **Underhåll**, välj aviseringen och sätt statusen till **Stängd**:

[![Markera och Stäng aviseringen](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

Värdet i statuskolumnen ändras till **Stängd**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till självstudierna låter du acceleratorn för fjärrövervakningslösningen vara distribuerad.

Om du inte längre behöver Solution Accelerator tar du bort den från sidan [etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att markera den och sedan klicka på **ta bort lösning**:

![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution.png)


![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution-page.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat acceleratorn Fjärrövervakningslösning och utfört en övervakningsuppgift med hjälp av de simulerade enheterna i Contoso-standarddistributionen.

Fortsätt till nästa självstudie om du vill lära dig mer om lösningsaccelerator som använder simulerade enheter.

> [!div class="nextstepaction"]
> [Självstudier: Övervaka dina IoT-enheter](iot-accelerators-remote-monitoring-monitor.md)