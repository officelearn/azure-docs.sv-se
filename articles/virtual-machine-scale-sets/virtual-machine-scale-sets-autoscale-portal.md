---
title: "Autoskala virtuella skalningsuppsättningarna i Azure portal | Microsoft Docs"
description: "Hur du skapar automatiska regler för den virtuella datorn anger i Azure-portalen"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: e43be53817e7fa65c3d7a95cab9821126ed88831
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Skala automatiskt en virtuell dator skaluppsättningen i Azure-portalen
När du skapar en skaluppsättning för definiera antalet VM-instanser som du vill köra. När din begäran för program ändras, kan du automatiskt öka eller minska antalet VM-instanser. Möjligheten att Autoskala kan du Håll dig uppdaterad med kundernas behov eller svara på ändringar i programmet prestanda under hela livscykeln för din app.

Den här artikeln visar hur du skapar automatiska regler i Azure-portalen som övervaka prestanda för VM-instanser i en skaluppsättning. Reglerna Autoskala öka eller minska antalet VM-instanser som svar på dessa prestandamått. Du kan också utföra dessa steg med [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) eller [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md).


## <a name="prerequisites"></a>Förutsättningar
Du behöver en befintlig virtuell dator för att skapa regler för automatiska skaluppsättning. Du kan skapa en skala som anges med den [Azure-portalen](virtual-machine-scale-sets-create-portal.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md), eller [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för att automatiskt skala ut
Om ditt program begäran ökar, ange ökar belastningen på VM-instanser i nivå. Om den här ökade belastningen är konsekvent, i stället för bara en kort begäran, kan du konfigurera automatiska regler för att öka antalet VM-instanser i skaluppsättning. När dessa VM-instanser som skapas och dina program distribueras börjar skaluppsättning distribuera trafik till dem via belastningsutjämnaren. Du kan styra vilka mått som ska övervakas, t.ex CPU eller disk, hur länge belastningen program måste uppfylla ett visst tröskelvärde och ange hur många VM-instanser som ska läggas till skalan.

1. Öppna Azure portal och välj **resursgrupper** på menyn till vänster på instrumentpanelen.
2. Välj den resursgrupp som innehåller din skaluppsättning, och välj sedan din skaluppsättningen i listan över resurser.
3. Välj **skalning** ange fönster på menyn till vänster för skalan. Klicka om du vill **aktivera Autoskala**:

    ![Aktivera Autoskala i Azure-portalen](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Ange ett namn för dina inställningar, till exempel *Autoskala*, Välj alternativet att **lägga till en regel**.

5. Nu ska vi skapa en regel som ökar antalet VM-instanser i en skala som anges när Genomsnittlig CPU-belastningen är större än 70% under en period om 10 minuter. När regeln utlöser ökar antalet VM-instanser med 20%. I skalningsuppsättningar med ett litet antal VM-instanser som du kan ange den **åtgärden** till *öka antalet av* och sedan ange *1* eller *2* för den *instansen antal*. VM-instanser kan vara mer lämpliga i skalningsuppsättningar med ett stort antal VM-instanser, en ökning av 10% eller 20%.

    Ange följande inställningar för din regel:
    
    | Parameter              | Förklaring                                                                                                         | Värde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Aggregation tid*     | Definierar hur mätvärdena som samlats in ska aggregeras för analys.                                                | Medel        |
    | *Måttnamnet*          | Prestanda-mått för att övervaka och tillämpa skala ange åtgärder på.                                                   | Processorprocentandel |
    | *Tid grain statistik* | Definierar hur mätvärdena som samlats in i varje tidsenhet ska aggregeras för analys.                             | Medel        |
    | *Operatorn*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
    | *Tröskelvärde*            | Den procentandel som regeln Autoskala kan utlösa en åtgärd.                                                 | 70             |
    | *Varaktighet*             | Tidsperiod som övervakas innan värdena mått och tröskelvärdet jämförs.                                   | 10 minuter     |
    | *Åtgärd*            | Anger om skaluppsättning bör skala upp eller ned när regeln gäller och i vilka steg                        | Öka procent med |
    | *Instansantal*       | Procentandelen av VM-instanser som ska ändras när regeln utlöser.                                            | 20             |
    | *Ned (minuter)*  | Hur lång tid ska gå innan regeln tillämpas igen så att automatiska åtgärder har tid att börja gälla. | 5 minuter      |

    I följande exempel visas en regel som skapas i Azure-portalen som matchar de här inställningarna:    

    ![Skapa en regel för Autoskala för att öka antalet VM-instanser](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. För att skapa regeln, Välj **Lägg till**


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för att skala automatiskt i
På en kväll eller helger minska program-begäran. Om det här minskar belastningen är konsekvent under en viss tidsperiod, kan du konfigurera automatiska regler för att minska antalet VM-instanser i skaluppsättning. Den här åtgärden för skala minskar kostnaden för att köra skaluppsättningen eftersom du bara köra antalet instanser som krävs för att uppfylla den aktuella begäran.

1. Välja att **lägga till en regel** igen.
2. Skapa en regel som minskar antalet VM-instanser i en skala som anges när Genomsnittlig CPU-belastningen sedan sjunker under 30% över 10-minutersperiod. När regeln utlöser minskade antalet VM-instanser med 20%.

    Använda samma metod som i föregående regeln. Justera följande inställningar för din regel:
    
    | Parameter              | Förklaring                                                                                                          | Värde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operatorn*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                      | Mindre än   |
    | *Tröskelvärde*            | Den procentandel som regeln Autoskala kan utlösa en åtgärd.                                                 | 30             |
    | *Åtgärd*            | Anger om skaluppsättning bör skala upp eller ned när regeln gäller och i vilka steg                         | Minska procent med |
    | *Instansantal*       | Procentandelen av VM-instanser som ska ändras när regeln utlöser.                                             | 20             |

3. För att skapa regeln, Välj **Lägg till**


## <a name="define-autoscale-instance-limits"></a>Definiera Autoskala beräkningsrollsinstansens begränsningar
Autoskala profilen måste definiera en lägsta, högsta och standardantalet VM-instanser. Autoskala-regler tillämpas Kontrollera gränserna instans när du inte skalar upp efter antalet instanser eller skala i utöver minsta möjliga instanser.

1. Ange följande beräkningsrollsinstansens begränsningar:

    | Minimum | Maximal | Standard|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Om du vill använda dina Autoskala regler och beräkningsrollsinstansens begränsningar, Välj **spara**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skaluppsättning
Om du vill visa antalet och status för VM-instanser, Välj **instanser** ange fönster på menyn till vänster för skalan. Statusen anger om VM-instans är *skapa* eftersom skaluppsättningen automatiskt skalas ut eller är *om du tar bort* som skalan skalas automatiskt i.

![Visa en lista över scale set VM-instanser](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Autoskala enligt ett schema
I föregående exempel skalas automatiskt en skala ställa in eller ut med grundläggande värden, till exempel CPU-användning. Du kan också skapa automatiska regler baserat på scheman. Reglerna schemabaserade kan du skala automatiskt antalet VM-instanser i en förväntad ökning i programmet begäran, till exempel core arbetstid, och sedan automatiskt skala antalet instanser samtidigt som du planerar mindre begäran som det är helgen.

1. Välj **skalning** ange fönster på menyn till vänster för skalan. Om du vill ta bort de automatiska regler som har skapats i föregående exempel, väljer du ikonen Papperskorgen.

    ![Ta bort de befintliga reglerna Autoskala](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Välja att **Lägg till ett villkor för skala**. Välj pennikonen bredvid namnet på regel och ange ett namn som *skala ut under varje arbetsdagen*.

    ![Byt namn på Autoskala standardregel](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Välj att alternativknappen **skala till en specifik instansantalet**.
4. Om du vill skala antalet instanser, ange *10* som instansantal.
5. Välj **Upprepa särskilda dagar** för den **schema** typen.
6. Välj alla gällande arbetsdagar, måndag-fredag.
7. Välj lämplig tidszonen, och anger sedan en **starttid** av *09:00*.
8. Välja att **Lägg till ett villkor för skala** igen. Upprepa processen för att skapa ett schema med namnet *skala på kvällar* som skalas till *3* instanser, upprepas varje veckodag och börjar vid *18:00*.
9. Om du vill använda schemabaserade Autoskala reglerna, Välj **spara**.

    ![Skapa automatiska regler som kan skalas enligt ett schema](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Om du vill se hur Autoskala-regler tillämpas, Välj **kör historik** längst upp i den **skalning** fönster. Diagram och händelser lista visas när Autoskala regler utlösaren och antalet VM-instanser i nivå ökar eller minskar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder automatiska regler för att skala horisontellt och öka eller minska den *nummer* i VM-instanser i nivå. Du kan även skala lodrätt för att öka eller minska den Virtuella datorinstansen *storlek*. Mer information finns i [lodräta Autoskala med Virtual Machine-skalningsuppsättningar](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns [hantera virtuella skalningsuppsättningarna med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Information om hur du genererar aviseringar när din Autoskala regler utlösaren finns [använda automatiska åtgärder för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Du kan också [Använd granskningsloggarna för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
