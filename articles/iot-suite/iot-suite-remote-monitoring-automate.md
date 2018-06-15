---
title: Identifiera enhetsproblem med i fjärranslutna övervakningslösning - Azure | Microsoft Docs
description: Den här kursen visar hur du använder regler och åtgärder för att identifiera problem med tröskelvärdet-baserade enheter i den fjärranslutna övervakningslösning automatiskt.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 5acf35ed19a5b6baa2885fd58cfb7fbbe1ac3cd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774660"
---
# <a name="detect-issues-using-threshold-based-rules"></a>Identifiera problem med tröskelvärdesbaserad regler

Den här kursen visar funktionerna i regelmotor i fjärranslutna övervakningslösning. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

Contoso har en regel som genererar en kritisk varning när trycket som rapporterats av en **kylaggregat** enhet överskrider 250 PSI. Som operatör kan du vill identifiera **kylaggregat** enheter som kan vara problematiskt sensorer genom att söka efter inledande trycket toppar. För att identifiera dessa enheter kan skapa du en regel för att generera en varning när överstiger 150 PSI.

Du också har bett att en kritisk varning måste vara utlöses när den genomsnittliga fuktigheten den **kylaggregat** enheten under de senaste 5 minuterna är större än 80% och temperaturen för den **kylaggregat** enhet i den senaste 5 minuter är större än 75 grader fahrenheit.

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Visa regler i din lösning
> * Skapa en ny regel
> * Skapa en ny regel med flera villkor
> * Redigera en befintlig regel
> * Ta bort en regel

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning solution accelerator](iot-suite-remote-monitoring-deploy.md) kursen.

## <a name="view-the-rules-in-your-solution"></a>Visa regler i din lösning

Den **regler** i lösningen visas en lista över alla aktuella regler:

![Sidan regler och åtgärder](media/iot-suite-remote-monitoring-automate/rulesactions_v2.png)

Visa regler som gäller för **kylaggregat** enheter kan använda ett filter:

![Filtrera listan över regler](media/iot-suite-remote-monitoring-automate/rulesactionsfilter_v2.png)

Du kan visa mer information om en regel och redigera den när du väljer den i listan:

![Visa information om regeln](media/iot-suite-remote-monitoring-automate/rulesactionsdetail_v2.png)

Om du vill inaktivera, aktivera eller ta bort en eller flera regler, väljer du flera regler i listan:

![Markera flera regler](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Skapa en ny regel

Att lägga till en ny regel som genererar en varning när trycket i en **kylaggregat** enhet överskrider 150 PSI, Välj **ny regel**:

![Skapa regel](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Använd följande värden för att skapa regeln:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Regelnamn        | Kylaggregat varning                       |
| Beskrivning      | Kylaggregat trycket har överskridit 150 PSI |
| Enhetsgrupp     | **Chillers** enhetsgrupp             |
| Beräkningen      | Snabbmeddelanden                               |
| Tillstånd 1-fält| tryck                              |
| Tillstånd 1-operator | Större än                      |
| Villkorsvärdet 1    | 150                               |
| Serverity nivå  | Varning                               |

Om du vill spara den nya regeln, Välj **tillämpa**.

Du kan visa när regeln aktiveras på den **regler** sidan eller på den **instrumentpanelen** sidan.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Skapa en ny regel med flera villkor

Att skapa en ny regel med flera villkor som genererar ett allvarligt Varna när den genomsnittliga fuktigheten den **kylaggregat** enheten under de senaste 5 minuterna är större än 80% och temperaturen för den **kylaggregat** enheten under de senaste 5 minuterna är större än 75 grader fahrenheit, Välj **ny regel**:

![Skapa mult regel](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Använd följande värden för att skapa regeln:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Regelnamn        | Kylaggregat fuktighet och temp kritiska    |
| Beskrivning      | Fuktighet och temperatur är avgörande |
| Enhetsgrupp     | **Chillers** enhetsgrupp             |
| Beräkningen      | Medel                               |
| Tidsperiod      | 5                                     |
| Tillstånd 1-fält| fuktighet                              |
| Tillstånd 1-operator | Större än                      |
| Villkorsvärdet 1    | 80                               |
| Serverity nivå  | Kritiska                              |

Om du vill lägga till det andra villkoret, klicka på ”+ Lägg till villkor”.

![Skapa villkor 2](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Använd följande värden på nytt villkor:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Tillstånd 2-fält| Temperatur                           |
| Tillstånd 2-operatorn | Större än                      |
| Villkorsvärdet 2    | 75                                |

Om du vill spara den nya regeln, Välj **tillämpa**.

Du kan visa när regeln aktiveras på den **regler** sidan eller på den **instrumentpanelen** sidan.

## <a name="edit-an-existing-rule"></a>Redigera en befintlig regel

Om du vill ändra en befintlig regel, markerar du den i listan över regler.

![Redigera regel](media/iot-suite-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen visades hur du vill:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Visa regler i din lösning
> * Skapa en ny regel
> * Redigera en befintlig regel
> * Ta bort en regel

Nu när du har lärt dig hur du identifierar problem med tröskelvärdesbaserad regler, föreslagna nästa steg är att lära dig hur du:

* [Hantera och konfigurera dina enheter](./iot-suite-remote-monitoring-manage.md).
* [Felsök och åtgärda enhetsproblem](./iot-suite-remote-monitoring-maintain.md).
* [Testa din lösning med simulerade enheter](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->