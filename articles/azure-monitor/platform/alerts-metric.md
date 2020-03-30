---
title: Skapa, visa och hantera måttaviseringar med Azure Monitor
description: Lär dig hur du använder Azure Portal eller CLI för att skapa, visa och hantera måttaviseringsregler.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369394"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Skapa, visa och hantera måttaviseringar med Azure Monitor

Måttaviseringar i Azure Monitor är ett sätt att få ett meddelande när ett av dina mått överskrider ett tröskelvärde. Du kan använda måttaviseringar för en mängd olika flerdimensionella plattformsmått, anpassade mått samt vanliga och anpassade Application Insights-mått. I den här artikeln beskriver vi hur du skapar, visar och hanterar måttaviseringsregler via Azure Portal och Azure CLI. Du kan också skapa måttaviseringsregler med Azure Resource Manager-mallar, som beskrivs i [en separat artikel](alerts-metric-create-templates.md).

Du kan läsa mer om hur måttaviseringar fungerar från [måttaviseringar översikt](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Skapa med Azure Portal

I följande procedur beskrivs hur du skapar en måttaviseringsregel i Azure Portal:

1. I [Azure Portal](https://portal.azure.com)klickar du på **Övervaka**. Monitor-bladet konsoliderar alla dina övervakningsinställningar och data i en vy.

2. Klicka på Aviseringar och klicka sedan på **+ Ny varningsregel**. **Alerts**

    > [!TIP]
    > De flesta resursblad har också **aviseringar** i sin resursmeny under **Övervakning,** du kan skapa aviseringar därifrån också.

3. Klicka på **Välj mål**i kontextfönstret som läses in och välj en målresurs som du vill avisera på. Använd listrutan **Prenumeration** **och Resurstyp** för att hitta den resurs som du vill övervaka. Du kan också använda sökfältet för att hitta din resurs.

4. Om den valda resursen har mått som du kan skapa aviseringar på, **Innehåller tillgängliga signaler** längst ned till höger mått. Du kan visa den fullständiga listan över resurstyper som stöds för måttaviseringar i den här [artikeln](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. När du har valt en målresurs klickar du på **Lägg till villkor**.

6. Du kommer att se en lista över signaler som stöds för resursen, välj det mått som du vill skapa en avisering på.

7. Du kommer att se ett diagram för måttet för de senaste sex timmarna. Använd listrutan **Diagramperiod** för att välja för att se längre historik för måttet.

8. Om måttet har dimensioner visas en dimensionstabell. Välj ett eller flera värden per dimension.
    - De visade dimensionsvärdena baseras på måttdata från de senaste tre dagarna.
    - Om dimensionsvärdet du letar efter inte visas klickar du på "+" för att lägga till ett anpassat värde.
    - Du kan också **välja \* ** för någon av dimensionerna. **Select \* ** skalar markeringen dynamiskt till alla aktuella och framtida värden för en dimension.

    Måttaviseringsregeln utvärderar villkoret för alla kombinationer av valda värden. [Läs mer om hur aviseringar om flerdimensionella mått fungerar](alerts-metric-overview.md).

9. Välj **tröskeltyp,** **Operator**operator och **aggregeringstyp**. Detta avgör logiken som måttaviseringsregeln utvärderar.
    - Om du använder ett **statiskt** tröskelvärde fortsätter du att definiera ett **tröskelvärde**. Måttdiagrammet kan hjälpa till att avgöra vad som kan vara en rimlig tröskel.
    - Om du använder ett **dynamiskt** tröskelvärde fortsätter du att definiera **tröskelkänsligheten**. Måttdiagrammet visar de beräknade tröskelvärdena baserat på de senaste uppgifterna. [Läs mer om villkorstyp och känslighetsalternativ för dynamiska tröskelvärden](alerts-dynamic-thresholds.md).

10. Du kan också förfina villkoret genom att justera **aggregeringsgranularitet** och **utvärderingsfrekvens**. 

11. Klicka på **Klar**.

12. Du kan också lägga till ytterligare villkor om du vill övervaka en komplex varningsregel. För närvarande kan användare ha varningsregler med villkor för dynamiska tröskelvärden som ett enda kriterium.

13. Fyll i **aviseringsinformation** som **varningsregelnamn,** **Beskrivning**och **allvarlighetsgrad**.

14. Lägg till en åtgärdsgrupp i aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

15. Klicka på **Klar** om du vill spara måttaviseringsregeln.

> [!NOTE]
> Måttaviseringsregler som skapas via portalen skapas i samma resursgrupp som målresursen.

## <a name="view-and-manage-with-azure-portal"></a>Visa och hantera med Azure-portal

Du kan visa och hantera måttaviseringsregler med bladet Hantera regler under Aviseringar. Proceduren nedan visar hur du visar dina måttvarningsregler och redigerar en av dem.

1. I Azure-portalen navigerar du till **Övervaka**

2. Klicka på **Aviseringar** och **hantera regler**

3. I bladet **Hantera regler** kan du visa alla dina varningsregler för prenumerationer. Du kan ytterligare filtrera reglerna med hjälp av **resursgrupp,** **resurstyp**och **resurs**. Om du bara vill se måttaviseringar väljer du **Signaltyp** som mått.

    > [!TIP]
    > I bladet **Hantera regler** kan du välja flera varningsregler och aktivera/inaktivera dem. Detta kan vara användbart när vissa målresurser måste sättas under underhåll

4. Klicka på namnet på den måttaviseringsregel som du vill redigera

5. Klicka på de **varningsvillkor** som du vill redigera i redigeringsregeln. Du kan ändra mått, tröskelvillkor och andra fält efter behov

    > [!NOTE]
    > Du kan inte redigera **målresursen** och **aviseringsregelnamnet** när måttaviseringen har skapats.

6. Klicka på **Klar** om du vill spara ändringarna.

## <a name="with-azure-cli"></a>Med Azure CLI

I föregående avsnitt beskrivs hur du skapar, visar och hanterar måttaviseringsregler med Azure-portalen. I det här avsnittet beskrivs hur du gör samma sak med Azure [CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) Snabbaste sättet att börja använda Azure CLI är via [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). För den här artikeln kommer vi att använda Cloud Shell.

1. Gå till Azure portal, klicka på **Cloud Shell**.

2. Vid prompten kan du använda ``--help`` kommandon med alternativ för att lära dig mer om kommandot och hur du använder det. Följande kommando visar till exempel listan över kommandon som är tillgängliga för att skapa, visa och hantera måttaviseringar

    ```azurecli
    az monitor metrics alert --help
    ```

3. Du kan skapa en enkel måttvarningsregel som övervakar om genomsnittlig procentuell processor på en virtuell dator är större än 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Du kan visa alla måttaviseringar i en resursgrupp med följande kommando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Du kan se information om en viss måttaviseringsregel med hjälp av namnet eller resurs-ID:t för regeln.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Du kan inaktivera en måttvarningsregel med följande kommando.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Du kan ta bort en måttvarningsregel med följande kommando.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Nästa steg

- [Skapa måttaviseringar med Azure Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md).
- [Förstå hur måttaviseringar fungerar](alerts-metric-overview.md).
- [Förstå hur måttaviseringar med villkor för dynamiska tröskelvärden fungerar](alerts-dynamic-thresholds.md).
- [Förstå webbkrokschemat för måttaviseringar](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

