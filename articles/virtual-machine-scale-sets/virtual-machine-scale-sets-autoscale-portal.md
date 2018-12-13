---
title: Virtuella datorer för autoskalning anger i Azure portal | Microsoft Docs
description: Hur du skapar regler för automatisk skalning för VM-skalningsuppsättning som anger i Azure portal
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: zarhoads
ms.openlocfilehash: ceb3f1da742c79d80398b87b9c4880781175690c
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321519"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Skala automatiskt en VM-skalningsuppsättning i Azure portal
När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app.

Den här artikeln visar hur du skapar regler för automatisk skalning i Azure-portalen som övervakar prestandan för de Virtuella datorinstanserna i din skalningsuppsättning. Dessa regler för automatisk skalning öka eller minska antalet Virtuella datorinstanser som svar på dessa prestandamått. Du kan också utföra dessa steg med [Azure PowerShell](tutorial-autoscale-powershell.md) eller [Azure CLI](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Förutsättningar
För att skapa regler för automatisk skalning, behöver du en befintlig virtuell dator skalningsuppsättning. Du kan skapa en skalningsuppsättning med den [Azure-portalen](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md), eller [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för att automatiskt skala ut
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

1. Öppna Azure portal och väljer **resursgrupper** på menyn till vänster på instrumentpanelen.
2. Välj den resursgrupp som innehåller din skalningsuppsättning och välj din skalningsuppsättning i listan över resurser.
3. Välj **skalning** ange fönstret på menyn på vänster sida av skalan. Välj knappen för att **aktivera autoskalning**:

    ![Aktivera automatisk skalning i Azure portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Ange ett namn för dina inställningar, till exempel *Autoskala*, Välj alternativet att **lägga till en regel**.

5. Nu ska vi skapa en regel som ökar antalet Virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen är större än 70% över en 10-minutersperiod. När regeln utlöses, ökar antalet Virtuella datorinstanser med 20%. I skalningsuppsättningar med ett litet antal VM-instanser, du kan ange den **åtgärden** till *öka antal med* och anger sedan *1* eller *2* för den *antal instanser*. VM-instanser i skalningsuppsättningar med ett stort antal instanser av virtuella datorer, en ökning av 10% eller 20% kan vara mer effektivt.

    Ange följande inställningar för din regel:
    
    | Parameter              | Förklaring                                                                                                         | Värde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tidsmängd*     | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Medel        |
    | *Måttnamn*          | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Procent CPU |
    | *Tidsintervallstatistik* | Definierar hur de insamlade mätvärdena i varje tidsintervall ska aggregeras för analys.                             | Medel        |
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
    | *Tröskelvärde*            | Den procentandel som orsakar regeln för automatisk skalning att utlösa en åtgärd.                                                 | 70             |
    | *Varaktighet*             | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs.                                   | 10 minuter     |
    | *Åtgärd*            | Anger om skalningsuppsättningen ska skala upp eller ned när regeln ska tillämpas och i vilka steg                        | Öka procent med |
    | *Instansantal*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                            | 20             |
    | *Väntetid (minuter)*  | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter      |

    I följande exempel visas en regel som skapas i Azure-portalen som matchar de här inställningarna:

    ![Skapa en regel för automatisk skalning för att öka antalet Virtuella datorinstanser](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. För att skapa regeln, Välj **Lägg till**


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för automatisk skalning i
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

1. Välja att **lägga till en regel** igen.
2. Skapa en regel som minskar antalet Virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen faller under 30% över en 10-minutersperiod. När regeln utlöses, minskar antalet Virtuella datorinstanser med 20%.

    Använd samma metod om du precis som med den föregående regeln. Justera följande inställningar för din regel:
    
    | Parameter              | Förklaring                                                                                                          | Värde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                      | Mindre än   |
    | *Tröskelvärde*            | Den procentandel som orsakar regeln för automatisk skalning att utlösa en åtgärd.                                                 | 30             |
    | *Åtgärd*            | Anger om skalningsuppsättningen ska skala upp eller ned när regeln ska tillämpas och i vilka steg                         | Minska procent med |
    | *Instansantal*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                             | 20             |

3. För att skapa regeln, Välj **Lägg till**


## <a name="define-autoscale-instance-limits"></a>Definiera instansgränser för automatisk skalning
Din autoskalningsprofilen måste definiera en lägsta, högsta och standardvärdet för antal VM-instanser. När reglerna för automatisk skalning tillämpas gränserna instans att kontrollera att du inte skala ut utöver det maximala antalet instanser eller skala in utöver minimum för instanser.

1. Ange följande begränsningar för instans:

    | Minimum | Maximal | Standard|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Markera för att tillämpa dina regler för automatisk skalning och instansgränser **spara**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skalningsuppsättning
Om du vill se antalet och status för VM-instanser, **instanser** ange fönstret på menyn på vänster sida av skalan. Statusen anger om den Virtuella datorinstansen är *skapa* eftersom skalningsuppsättningen automatiskt skalas upp eller är *tas bort* eftersom skalningsuppsättningen skalas automatiskt in.

![Visa en lista över skalningsuppsättningar för Virtuella datorinstanser](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatisk skalning baserat på ett schema
I föregående exempel skalas automatiskt en skala in eller ut med grundläggande värdmått, till exempel CPU-användning. Du kan också skapa regler för automatisk skalning baserat på scheman. Reglerna schemabaserade kan du automatiskt skala ut antalet Virtuella datorinstanser före en förväntad ökning i programkrav, till exempel core arbetstid, och sedan automatiskt skala antalet instanser vid en tidpunkt som du räknar mindre begäran, till exempel helgen.

1. Välj **skalning** ange fönstret på menyn på vänster sida av skalan. Välj på Papperskorgen för att ta bort befintliga reglerna för automatisk skalning som skapats i föregående exempel.

    ![Ta bort de befintliga reglerna för automatisk skalning](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Välja att **lägga till en skalningsvillkoret**. Välj pennikonen bredvid Regelnamnet på och ange ett namn som *skala ut under varje arbetsdag*.

    ![Byt namn på regeln för automatisk skalning standard](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Välj alternativknappen till **skala till ett specifikt instansantal**.
4. Om du vill skala upp antalet instanser, ange *10* som instansantalet.
5. Välj **Upprepa specifika dagar** för den **schema** typen.
6. Välj alla gällande arbetsdagar, måndag till fredag.
7. Välj lämplig tidszon och ange en **starttid** av *09:00*.
8. Välja att **Lägg till ett villkor för skala** igen. Upprepa stegen för att skapa ett schema med namnet *skala i på kvällar* som kan skalas till *3* instanser, upprepas varje veckodag och börjar vid *18:00*.
9. Om du vill använda schemabaserad automatisk skalning reglerna, Välj **spara**.

    ![Skapa regler för automatisk skalning som kan skalas enligt ett schema](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Om du vill se hur dina regler för automatisk skalning tillämpas **Körningshistorik** överst på den **skalning** fönster. Diagram och händelser lista visas när utlösaren för automatisk skalning regler och antalet Virtuella datorinstanser i skalningsuppsättningen ökningar och minskningar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder regler för automatisk skalning att skala vågrätt och öka eller minska den *nummer* för VM-instanser i din skalningsuppsättning. Du kan även skala lodrätt om du vill öka eller minska den Virtuella datorinstansen *storlek*. Mer information finns i [vertikal automatisk skalning med VM Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns i [hantera VM-skalningsuppsättningar med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Läs hur du genererar aviseringar när dina autoskalningsregler utlösare i [använda åtgärder för automatisk skalning för att skicka e-post och webhook-aviseringar i Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Du kan också [Använd granskningsloggar för att skicka e-post och webhook-aviseringar i Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
