---
title: Identifiera problem med enheter i en Azure-baserad fjärrövervakningslösning | Microsoft Docs
description: I den här självstudien får du lära dig att använda regler och åtgärder till att automatiskt identifiera problem med tröskelvärden i lösningen för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098489"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Självstudie: Identifiera problem med enheter som är anslutna till din övervakningslösning

I den här självstudien konfigurerar du självstudiekursen använder du lösningsacceleratorn Fjärrövervakning till att identifiera problem med dina anslutna IoT-enheter. Du kan identifiera problem med dina enheter genom att lägga till regler som genererar aviseringar på instrumentpanelen för lösningen.

I självstudiekursen använder vi regler och aviseringar för ett simulerat kylaggregat. Kylaggregatet hanteras av en organisation som heter Contoso och är anslutet till lösningsacceleratorn Fjärrövervakning. Contoso har redan en regel som genererar en kritisk avisering när trycket i kylaggregatet överstiger 298 psi. Som operatör på Contoso vill du identifiera kylaggregat enheter med felaktiga sensorer genom att leta efter plötsliga toppar i tryckvärdena. Det gör du genom att lägga till en regel som genererar en avisering när trycket i kylaggregatet överstiger 150 psi.

Du har också blivit ombedd att skapa en kritisk avisering för ett kylaggregat när den genomsnittliga luftfuktigheten i enheten varit högre än 80 % och enhetens temperatur varit högre än 75 grader fahrenheit under de senaste fem minuterna.

I den här kursen får du:

>[!div class="checklist"]
> * Visa regler i din lösning
> * Skapa en regel
> * Skapa en regel med flera villkor
> * Redigera en befintlig regel
> * Aktivera och inaktivera regler

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här självstudien behöver du en distribuerad instans av lösningsacceleratorn Fjärrövervakning i Azure-prenumerationen.

Om du inte har distribuerat lösningsacceleratorn Fjärrövervakning ännu bör du genomföra snabbstarten [Deploy a cloud-based remote monitoring solution](quickstart-remote-monitoring-deploy.md) (Distribuera en molnbaserad fjärrövervakningslösning).

## <a name="view-the-existing-rules"></a>Visa befintliga regler

På sidan **Regler** i lösningsacceleratorn visas en lista med aktuella regler:

[![Sidan Regler](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Om du bara vill visa regler som gäller för kylaggregat kan du använda ett filter:

[![Filtrera listan med regler](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

Du kan visa mer information om en regel och redigera den när du väljer den i listan:

[![Visa regelinformation](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

Om du vill inaktivera eller aktivera en eller flera regler markerar du dem i listan:

[![Markera flera regler](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Skapa en regel

Om du vill skapa en regel som genererar en varning när trycket i ett kylaggregat överstiger 150 psi klickar du på **Ny regel**. Skapa regeln med följande värden:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Regelnamn        | Kylaggregatsvarning                       |
| Beskrivning      | Trycket i kylaggregatet har överstigit 150 psi |
| Enhetsgrupp     | **Kylaggregatets** enhetsgrupp             |
| Beräkning      | Omedelbar                               |
| Villkor 1 fält| tryck                              |
| Villkor 1 operator | Större än                      |
| Villkor 1 värde    | 150                               |
| Allvarlighetsgrad  | Varning                               |

[![Skapa varningsregel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Om du vill spara den nya regeln klickar du på **Tillämpa**.

Du kan se när regeln utlöses på sidan **Regler** eller på **instrumentpanelen**:

[![Utlöst varningsregel](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>Skapa en regel med flera villkor

Om du vill skapa en regel med flera villkor som genererar en kritisk varning när den genomsnittliga luftfuktigheten har varit högre än 80 % och medeltemperaturen varit högre än 75 grader fahrenheit under de senaste fem minuterna klickar du på **Ny regel** . Skapa regeln med följande värden:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Regelnamn        | Kritisk luftfuktighet och temperatur för kylaggregat    |
| Beskrivning      | Luftfuktighet och temperatur har kritiska värden |
| Enhetsgrupp     | **Kylaggregatets** enhetsgrupp             |
| Beräkning      | Genomsnitt                               |
| Tidsperiod      | 5                                     |
| Villkor 1 fält| luftfuktighet                              |
| Villkor 1 operator | Större än                      |
| Villkor 1 värde    | 80                                |
| Allvarlighetsgrad  | Kritisk                              |

[![Skapa regel med flera villkor – del ett](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Lägg till det andra villkoret genom att klicka på ”+ Lägg till villkor”. Använd följande värden för det nya villkoret:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Villkor 2 fält| temperatur                           |
| Villkor 2 operator | Större än                      |
| Villkor 2 värde    | 75                                |

[![Skapa regel med flera villkor – del två](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Om du vill spara den nya regeln klickar du på **Tillämpa**.

Du kan se när regeln utlöses på sidan **Regler** eller på **instrumentpanelen**:

[![Utlöst regel med flera villkor](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Redigera en befintlig regel

Om du vill ändra en befintlig regel markerar du den i listan med regler och klickar på **Redigera**:

[![Redigera regel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Inaktivera en regel

Om du tillfälligt vill inaktivera en regel kan du göra det i listan med regler. Välj regeln du vill inaktivera och välj sedan **Inaktivera**. **Status** för regeln i listan ändras så att du ser att regeln nu är inaktiverad. Du kan aktivera regeln igen på samma sätt.

[![Inaktivera regel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Du kan aktivera och inaktivera flera regler på samma gång genom att markera flera regler i listan.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till nästa självstudie lämnar du den distribuerade lösningsacceleratorn Fjärrövervakning. Du kan minska kostnaden för att köra lösningsacceleratorn när du inte använder den genom att stoppa de simulerade enheterna på inställningspanelen:

[![Pausa telemetri](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

Du kan starta om de simulerade enheterna när du är redo att starta nästa självstudie.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Ta bort lösningen](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen såg du hur du kan använda sidan **Regler** i lösningsacceleratorn Fjärrövervakning till att skapa och hantera regler som utlöser aviseringar i lösningen. Fortsätt till nästa självstudie om du vill lära dig hur du använder lösningsacceleratorn till att hantera och konfigurera dina anslutna enheter.

> [!div class="nextstepaction"]
> [Konfigurera och hantera enheter som är anslutna till din övervakningslösning](iot-accelerators-remote-monitoring-manage.md)