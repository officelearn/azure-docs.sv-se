---
title: Självstudie om att identifiera problem med enheter i en Azure-baserad fjärrövervakningslösning | Microsoft Docs
description: I den här självstudien får du lära dig att använda regler och åtgärder till att automatiskt identifiera problem med tröskelvärden i lösningen för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 29d4289f5b83e37cddec652b976aeb5aa255ed4c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158885"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Självstudie: Identifiera problem med enheter som är anslutna till din övervakningslösning

I den här självstudien konfigurerar du självstudiekursen använder du lösningsacceleratorn Fjärrövervakning till att identifiera problem med dina anslutna IoT-enheter. Du kan identifiera problem med dina enheter genom att lägga till regler som genererar aviseringar på instrumentpanelen för lösningen.

I självstudiekursen använder vi regler och aviseringar för ett simulerat kylaggregat. Kylaggregatet hanteras av en organisation som heter Contoso och är anslutet till lösningsacceleratorn Fjärrövervakning. Contoso har redan en regel som genererar en kritisk avisering när trycket i kylaggregatet överstiger 298 psi. Som operatör på Contoso vill du identifiera kylaggregat enheter med felaktiga sensorer genom att leta efter plötsliga toppar i tryckvärdena. Det gör du genom att lägga till en regel som genererar en avisering när trycket i kylaggregatet överstiger 150 psi.

Du har också blivit ombedd att skapa en kritisk avisering för ett kylaggregat när den genomsnittliga luftfuktigheten i enheten varit högre än 80 % och enhetens temperatur varit högre än 75 grader fahrenheit under de senaste fem minuterna.

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Visa regler i din lösning
> * Skapa en regel
> * Skapa en regel med flera villkor
> * Redigera en befintlig regel
> * Aktivera och inaktivera regler

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Granska befintliga regler

På sidan **Regler** i lösningsacceleratorn visas en lista med aktuella regler:

[![Sidan Regler](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Om du bara vill visa regler som gäller för kylaggregat kan du använda ett filter. Du kan visa mer information om en regel och redigera den när du väljer den i listan:

[![Visa regelinformation](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

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

## <a name="create-an-advanced-rule"></a>Skapa en avancerad regel

Om du vill skapa en regel med flera villkor som genererar en kritisk varning när den genomsnittliga luftfuktigheten har varit högre än 80 % och medeltemperaturen varit högre än 75 grader fahrenheit under de senaste fem minuterna klickar du på **Ny regel** . Skapa regeln med följande värden:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Regelnamn        | Kritisk luftfuktighet och temperatur för kylaggregat    |
| Beskrivning      | Luftfuktighet och temperatur har kritiska värden |
| Enhetsgrupp     | **Kylaggregatets** enhetsgrupp             |
| Beräkning      | Medel                               |
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

[!INCLUDE [iot-iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen såg du hur du kan använda sidan **Regler** i lösningsacceleratorn Fjärrövervakning till att skapa och hantera regler som utlöser aviseringar i lösningen. Fortsätt till nästa självstudie om du vill lära dig hur du använder lösningsacceleratorn till att hantera och konfigurera dina anslutna enheter.

> [!div class="nextstepaction"]
> [Konfigurera och hantera enheter som är anslutna till din övervakningslösning](iot-accelerators-remote-monitoring-manage.md)