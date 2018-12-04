---
title: Datamodellering i Azure Time Series Insights | Microsoft Docs
description: Förstå datamodellering i Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 926d1a35cb10d50ddeacbed5476e2dcf14d0999d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52857050"
---
# <a name="data-modeling-in-azure-time-series-insights"></a>Datamodellering i Azure Time Series Insights

Det här dokumentet beskriver hur du arbetar med **Time Series modeller** följer Azure Time Series Insights (förhandsversion). Den beskriver flera vanliga datascenarier.

Läs den [Azure TSI (förhandsversion) Explorer](./time-series-insights-update-explorer.md) artikeln om du vill veta mer om att navigera den privata förhandsgranskningen.

## <a name="types"></a>Typer

### <a name="how-to-create-a-single-type"></a>Så här skapar du en enda typ

1. Börja med rubriken till panelen TSM modellen väljare och välj typer på menyn. Dölj sedan panelen för att fokusera på TSM typer:

    ![portal_one][1]

1. Klicka på **Lägg till**.
1. Ange all information som rör typer och klicka på **skapa**. Då ska skapa typer i miljön:

    ![portal_two][2]

### <a name="how-to-bulk-upload-one-or-more-types"></a>Massregistrera ladda upp en eller flera typer

1. Klicka på **ladda upp JSON**.
1. Välj den fil som innehåller typen nyttolasten.
1. Klicka på **ladda upp**

    ![portal_three][3]

### <a name="how-to-edit-a-single-type"></a>Så här redigerar du en enda typ

* Välj typ och klicka på **redigera** knappen. Gör nödvändiga ändringar och klicka på **spara**:

    ![portal_four][4]

### <a name="how-to-delete-a-type"></a>Hur du tar bort en typ

* Välj typ och klicka på **ta bort** knappen. Om inga instanser är kopplade till typerna, kommer att tas bort:

    ![portal_five][5]

## <a name="hierarchies"></a>Hierarkier

### <a name="how-to-create-a-single-hierarchy"></a>Så här skapar du en enkel hierarki

1. Börja med rubriken till panelen TSM modellen väljare och välj hierarkier på menyn. Dölj sedan panelen för att fokusera på TSM typer:

    ![portal_six][6]

1. Klicka på **Lägg till**

    ![portal_seven][7]

1. Klicka på **Lägg till nivå** i den högra rutan:

    ![portal_eight][8]

1. Ange information för hierarkin och klicka på **skapa**:

    ![portal_nine][9]

### <a name="how-to-bulk-upload-one-or-more-hierarchies"></a>Massregistrera ladda upp en eller flera hierarkier

1. Klicka på **ladda upp JSON**.
1. Välj den fil som innehåller nyttolasten i hierarkin.
1. Klicka på **överför**:

    ![portal_ten][10]

### <a name="how-to-edit-a-single-hierarchy"></a>Så här redigerar du en enkel hierarki

* Välj hierarkin och klicka på den **redigera** knappen. Gör nödvändiga ändringar och klicka på **spara**:

    ![portal_eleven][11]

### <a name="how-to-delete-a-hierarchy"></a>Hur du tar bort en hierarki

* Välj hierarkin och klicka på **ta bort** knappen. Om inga instanser är kopplade till hierarkin, kommer du att ta bort den.

    ![portal_twelve][12]

## <a name="instances"></a>Instanser

### <a name="how-to-create-a-single-instance"></a>Så här skapar du en enda instans

1. Börja med rubriken till panelen TSM modellen väljare och Välj instanser på menyn. Dölj sedan panelen för att fokusera på TSM typer:

    ![portal_thirteen][13]

1. Klicka på **lägga till**:

    ![portal_fourteen][14]

1. Ange instansinformation, Välj typ och hierarki associationen och klicka på **skapa**.

### <a name="how-to-bulk-upload-one-or-more-instances"></a>Massregistrera ladda upp en eller flera instanser

1. Klicka på **ladda upp JSON**.
1. Välj den fil som innehåller nyttolasten instanser:

    ![portal_fifteen][15]

1. Klicka på **överför**.

### <a name="how-to-edit-a-single-instance"></a>Så här redigerar du en enda instans

* Välj instansen och klicka på den **redigera** knappen. Gör nödvändiga ändringar och klicka på **spara**:

    ![portal_sixteen][16]

### <a name="how-to-delete-an-instance"></a>Hur du tar bort en instans

* Välj instansen och klicka på **ta bort** knappen. Om inga händelser är kopplade till instanserna, kommer du att ta bort den.

## <a name="next-steps"></a>Nästa steg

Läs [Time Series modeller](./time-series-insights-update-tsm.md) för mer information om **Time Series modeller**.

Visa Azure TSI (förhandsversion) Explorer [artikeln](./time-series-insights-update-explorer.md) mer information om förhandsversionen.

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png