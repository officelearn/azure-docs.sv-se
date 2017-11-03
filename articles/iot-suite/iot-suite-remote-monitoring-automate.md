---
title: "Identifiera enhetsproblem med i fjärranslutna övervakningslösning - Azure | Microsoft Docs"
description: "Den här kursen visar hur du använder regler och åtgärder för att identifiera problem med tröskelvärdet-baserade enheter i den fjärranslutna övervakningslösning automatiskt."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 2f2b221f5739ac370e110d60ed7812ce9ea5e05f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>Identifiera problem med tröskelvärdesbaserad regler

Den här kursen visar funktionerna i regelmotor i fjärranslutna övervakningslösning. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

Contoso har en regel som genererar en kritisk varning när trycket som rapporterats av en **kylaggregat** enhet överskrider 250 PSI. Som operatör kan du vill identifiera **kylaggregat** enheter som kan vara problematiskt sensorer genom att söka efter inledande trycket toppar. För att identifiera dessa enheter kan skapa du en regel för att generera en varning när överstiger 150 PSI.

I den här guiden får du lära dig hur man:

>[!div class="checklist"]
> * Visa regler i din lösning
> * Skapa en ny regel
> * Redigera en befintlig regel
> * Ta bort en regel

## <a name="prerequisites"></a>Krav

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-deploy.md) kursen.

## <a name="view-the-rules-in-your-solution"></a>Visa regler i din lösning

Den **regler & åtgärder** i lösningen visas en lista över alla aktuella regler:

![Sidan regler och åtgärder](media/iot-suite-remote-monitoring-automate/rulesactions.png)

Visa regler som gäller för **kylaggregat** enheter kan använda ett filter:

![Filtrera listan över regler](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

Du kan visa mer information om en regel och redigera den när du väljer den i listan:

![Visa information om regeln](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

Om du vill inaktivera, aktivera eller ta bort en eller flera regler, väljer du flera regler i listan:

![Markera flera regler](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>Skapa en ny regel

Att lägga till en ny regel som genererar en varning när trycket i en **kylaggregat** enhet överskrider 150 PSI, Välj **ny regel**:

![Skapa regel](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

Använd följande värden för att skapa regeln:

| Inställning          | Värde                                 |
| ---------------- | ------------------------------------- |
| Namn             | Kylaggregat varning                       |
| Källa           | **Kylaggregat** enhetsgrupp              |
| Utlösaren fält    | Tryck                              |
| Utlösaren operator | Större än                          |
| Värdet för utlösare    | 150                                   |
| Allvarlighetsgrad   | Varning                               |
| Larm händelsetexten | Kylaggregat trycket har överskridit 150 PSI |

Om du vill spara den nya regeln, Välj **tillämpa**.

Du kan visa när regeln aktiveras på den **regler & åtgärder** sidan eller på den **instrumentpanelen** sidan.

## <a name="edit-an-existing-rule"></a>Redigera en befintlig regel

Om du vill ändra en befintlig regel, markerar du den i listan över regler. I den **regeln detalj** panelen Välj **redigeringsläget**.

![Redigera regel](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>Inaktivera en regel

Om du vill växla tillfälligt inaktivera en regel, kan du inaktivera det i listan över regler. Välj regeln för att inaktivera och välj sedan **inaktivera**. Den **Status** i regeln i listan ändras för att ange regeln har nu inaktiverats. Du kan återaktivera en regel som du tidigare har inaktiverats på samma sätt.

![Inaktivera regeln](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

Du kan aktivera och inaktivera flera regler på samma gång om du väljer flera regler i listan.

## <a name="delete-a-rule"></a>Ta bort en regel

Om du vill ta bort en regel permanent, Välj regeln i listan över regler och sedan **ta bort**.

Du kan ta bort flera regler på samma gång om du väljer flera regler i listan.

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