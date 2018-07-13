---
title: Distribuera en molnbaserad lösning för IoT-fjärrövervakning på Azure | Microsoft Docs
description: I den här snabbstarten distribuerar du Azure IoT-lösningsacceleratorn Fjärrövervakning och loggar in för att använda lösningsinstrumentpanelen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/07/2018
ms.author: dobett
ms.openlocfilehash: e3eff46299ecfbfe39b57bc2cf5ed4a655a6d7f1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088139"
---
# <a name="quickstart-deploy-a-cloud-based-remote-monitoring-solution"></a>Snabbstart: Distribuera en molnbaserad fjärrövervakningslösning

Den här snabbstarten visar hur du distribuerar Azure IoT-lösningsacceleratorn Fjärrövervakning som en molnbaserad fjärrövervakningslösning för dina IoT-enheter. När du har distribuerat lösningsacceleratorn använder du sidan **Instrumentpanel** för lösningen för att visualisera simulerade enheter på en karta, och sidan **Underhåll** svarar på en tryckvarning från ett simulerat kylaggregat.

Standarddistributionen konfigurerar Fjärrövervakning-lösningsacceleratorn för ett företag med namnet Contoso. Contoso hanterar olika enheter, till exempel kylaggregat, som distribuerats i olika fysiska miljöer. Ett kylaggregat skickar telemetri om temperatur, luftfuktighet och tryck till Fjärrövervakning-lösningsacceleratorn.

## <a name="prerequisites"></a>Krav

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-the-solution"></a>Distribuera lösningen

När du distribuerar lösningsacceleratorn till Azure-prenumerationen måste du ange några konfigurationsalternativ.

Logga in på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) med dina Azure-kontouppgifter.

Klicka på **Testa nu** på panelen **Fjärrövervakning**.

![Välj Fjärrövervakning](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

På sidan **Skapa Fjärrövervakning-lösning** väljer du en **Basic**-distribution. Om du distribuerar lösningsacceleratorn för att lära dig hur den fungerar eller för att köra en demonstration väljer du alternativet **Basic** för att minska kostnaderna.

Välj **.NET** som språk. Java- och .NET-implementeringar har identiska funktioner.

Ange ett unikt **lösningsnamn** för din Fjärrövervakning-lösningsaccelerator.

Välj den **prenumeration** och **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. Du måste vara [global administratör eller användare](iot-accelerators-permissions.md) i prenumerationen.

Klicka på **Skapa lösning** för att inleda distributionen. Processen tar minst fem minuter att köra:

![Information om Fjärrövervakning-lösningen](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in på lösningen

När distributionen till din Azure-prenumeration är klar kan du logga in på instrumentpanelen för Fjärrövervakning-lösningsacceleratorn.

På sidan **Etablerade lösningar** klickar du på Fjärrövervakning-lösningsacceleratorn:

![Välj en ny lösning](./media/quickstart-remote-monitoring-deploy/choosenew.png)

Du kan visa information om Fjärrövervakning-lösningsacceleratorn på panelen som visas. Välj **Lösningens instrumentpanel** för att visa Fjärrövervakning-lösningsacceleratorn:

![Lösningens panel](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Klicka på **Acceptera** för att acceptera begäran om behörigheter som Fjärrövervakning-acceleratorn visar i webbläsaren:

[![Instrumentpanel för lösningen](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Visa dina enheter

Lösningens instrumentpanel visar följande information om Contosos enheter:

* **Enhetsstatistik** visar sammanfattningsinformation om aviseringar och totalt antal enheter. I standarddistributionen har Contoso 10 simulerade enheter av olika typ.

* **Enhetsplatser** visar var enheterna finns fysiskt. Färgen på nålen visar när det finns aviseringar från enheten.

* **Aviseringar** visar information om aviseringar från enheterna.

* **Telemetri** visar telemetri från enheterna. Du kan visa olika telemetriströmmar genom att klicka på telemetrityperna högst upp.

* **Analys** visar kombinerad information om aviseringarna från enheterna.

## <a name="respond-to-an-alert"></a>Svara på en avisering

Som operatör på Contoso kan du övervaka dina enheter via lösningens instrumentpanel. Panelen **Enhetsstatistik** visar att det har varit ett antal kritiska aviseringar och panelen **Aviseringar** visar att de flesta kommer från kylaggregatet. För Contosos kylaggregat visar ett inre tryck på över 250 PSI att enheten inte fungerar som den ska.

### <a name="identify-the-issue"></a>Identifiera problemet

På sidan **Instrumentpanel**, på panelen **Aviseringar** ser du att aviseringen **Chiller pressure too high** (Trycket i kylaggregatet är för högt). Kylaggregatet har en röd nål på kartan (du kanske måste panorera och zooma på kartan):

[![Instrumentpanelen visar tryckvarning och enhet på kartan](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

På panelen **Aviseringar** klickar du på **...** i kolumnen **Utforska** bredvid regeln **Chiller pressure too high** (Trycket i kylaggregatet är för högt). Den här åtgärden tar dig till sidan **Underhåll** där du kan visa informationen för regeln som utlöste aviseringen.

Underhållssidan för **Chiller pressure too high** (Trycket i kylaggregatet är för högt) visar information om regeln som utlöste aviseringarna. På sidan listas även när aviseringarna förekom och vilken enhet som utlöste dem:

[![Underhållssidan visar lista över aviseringar som har utlösts](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Nu har du identifierat problemet som utlöste aviseringen och på vilken enhet som problemet finns. Som operatör är nästa steg att bekräfta aviseringen och åtgärda problemet.

### <a name="fix-the-issue"></a>Åtgärda problemet

Visa för andra operatörer att du arbetar med aviseringen genom att välja den och ändra **aviseringens status** till **Bekräftad**:

[![Välj och bekräfta aviseringen](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

Värdet i statuskolumnen ändras till **Bekräftad**.

Åtgärda kylaggregat genom att rulla ned till **Relaterad information**, välja kylaggregatet i listan **Enheter med aviseringar** och välj sedan **Jobb**:

[![Välj en enhet och schemalägg en åtgärd](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

På panelen **Jobb** väljer du **Kör metod**, sedan metoden **EmergencyValveRelease**, lägger till jobbnamnet **ChillerPressureRelease** och klickar på **Använd**. Inställningarna skapar ett jobb som körs direkt.

Om du vill se jobbstatusen går du tillbaka till sidan **Underhåll** och visar listan över jobb i vyn **Jobb**. Du kan behöva vänta i några sekunder innan du ser att jobbet har körts för att släppa ventiltrycket på kylaggregatet:

[![Statusen för jobben i jobbvyn](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>Kontrollera att trycket är normalt igen

Om du vill visa trycktelemetrin för kylaggregatet går du till sidan **Instrumentpanel**, väljer **Pressure** (Tryck) i telemetripanelen och bekräftar att trycket för **chiller-02.0** är normalt igen:

[![Trycket är normalt igen](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Stäng incidenten genom att gå till sidan **Underhåll**, välj aviseringen och sätt statusen till **Stängd**:

[![Välj och stäng aviseringen](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

Värdet i statuskolumnen ändras till **Stängd**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till självstudierna låter du Fjärrövervakning-lösningsacceleratorn vara distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat Fjärrövervakning-lösningsacceleratorn och utfört en övervakningsuppgift med hjälp av de simulerade enheterna i Contoso-standarddistributionen.

Information om hur du uppdaterar den inbyggda programvara i dina anslutna enheter och organiserar dina tillgångar i lösningsacceleratorn finns i nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Självstudier: Övervaka dina IoT-enheter](iot-accelerators-remote-monitoring-monitor.md)