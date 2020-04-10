---
title: Skalning av virtuella datorskaleuppsättningar automatiskt i Azure-portalen
description: Skapa regler för automatisk skalning för skalningsuppsättningar för virtuella datorer i Azure-portalen
author: ju-shim
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 1915b144aec5a5447504c70d18dbf420d255a08e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010298"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Skala en skala för en virtuell dator automatiskt i Azure-portalen
När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app.

Den här artikeln visar hur du skapar regler för automatisk skalning i Azure-portalen som övervakar prestanda för VM-instanser i din skalningsuppsättning. Dessa regler för automatisk skalning ökar eller minskar antalet VM-instanser som svar på dessa prestandamått. Du kan också slutföra dessa steg med [Azure PowerShell](tutorial-autoscale-powershell.md) eller [Azure CLI](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Krav
Om du vill skapa regler för automatisk skalning behöver du en befintlig skaluppsättning för virtuella datorer. Du kan skapa en skalningsuppsättning med [Azure-portalen,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)eller [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för att automatiskt skala ut
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

1. Öppna Azure-portalen och välj **Resursgrupper** på menyn till vänster på instrumentpanelen.
2. Välj den resursgrupp som innehåller skalningsuppsättningen och välj sedan skalningsuppsättningen i listan över resurser.
3. Välj **Skala** på menyn till vänster i skalningsfönstret. Välj knappen för att **aktivera automatisk skalning:**

    ![Aktivera automatisk skalning i Azure-portalen](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Ange ett namn för dina inställningar, till exempel *automatisk skalning,* och välj sedan alternativet **Lägg till en regel**.

5. Nu ska vi skapa en regel som ökar antalet VM-instanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen är större än 70 % under en 10-minutersperiod. När regeln utlöses ökas antalet VM-instanser med 20 %. I skalningsuppsättningar med ett litet antal VM-instanser kan du ställa in **åtgärden** till *Öka antalet* med och ange sedan *1* eller *2* för *instansantalet*. I skalningsuppsättningar med ett stort antal VM-instanser kan en ökning med 10 % eller 20 % VM-instanser vara lämpligare.

    Ange följande inställningar för regeln:
    
    | Parameter              | Förklaring                                                                                                         | Värde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tid aggregering*     | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Medel        |
    | *Måttnamn*          | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Procent CPU |
    | *Tidsintervallstatistik* | Definierar hur de insamlade måtten i varje tidskorn ska aggregeras för analys.                             | Medel        |
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
    | *Tröskel*            | Den procentandel som gör att regeln för automatisk skalning utlöser en åtgärd.                                                 | 70             |
    | *Varaktighet*             | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs.                                   | 10 minuter     |
    | *Drift*            | Definierar om skalningsuppsättningen ska skalas upp eller ned när regeln gäller och efter vilket steg                        | Öka procenten med |
    | *Antal instanser*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                            | 20             |
    | *Väntetid (minuter)*  | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter      |

    Följande exempel visar en regel som skapats i Azure-portalen som matchar dessa inställningar:

    ![Skapa en regel för automatisk skalning för att öka antalet VM-instanser](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Om du vill skapa regeln väljer du **Lägg till**


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för att automatiskt skala i
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

1. Välj att **lägga till en regel** igen.
2. Skapa en regel som minskar antalet VM-instanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen sedan sjunker under 30 % under en 10-minutersperiod. När regeln utlöses minskas antalet VM-instanser med 20 %.

    Använd samma metod som med föregående regel. Justera följande inställningar för regeln:
    
    | Parameter              | Förklaring                                                                                                          | Värde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                      | Mindre än   |
    | *Tröskel*            | Den procentandel som gör att regeln för automatisk skalning utlöser en åtgärd.                                                 | 30             |
    | *Drift*            | Definierar om skalningsuppsättningen ska skalas upp eller ned när regeln gäller och efter vilket steg                         | Minska procent med |
    | *Antal instanser*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                             | 20             |

3. Om du vill skapa regeln väljer du **Lägg till**


## <a name="define-autoscale-instance-limits"></a>Definiera instansgränser för automatisk skalning
Din profil för automatisk skalning måste definiera ett minsta, högsta och standardantal vm-instanser. När reglerna för automatisk skalning tillämpas ser dessa instansgränser till att du inte skalar ut utöver det maximala antalet instanser eller skalar in utöver minimiinstanserna.

1. Ange följande förekomstgränser:

    | Minimum | Maximal | Default|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Om du vill tillämpa regler och instansgränser för automatisk skalning väljer du **Spara**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skalningsuppsättning
Om du vill visa antalet och statusen för VM-instanser väljer du **Instanser** på menyn till vänster i skalningsfönstret. Statusen anger om VM-instansen *skapar* som skalningsuppsättningen skalas automatiskt ut, eller *tar bort* som skalan automatiskt skalas in.

![Visa en lista över skalningsuppsättning VM-instanser](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatisk skalning baserat på ett schema
De tidigare exemplen skalade automatiskt en skala som anges i eller ut med grundläggande värdmått som CPU-användning. Du kan också skapa regler för automatisk skalning baserat på scheman. Med de här schemabaserade reglerna kan du automatiskt skala ut antalet VM-instanser före en förväntad ökning av programbehovet, till exempel grundläggande arbetstid, och sedan automatiskt skala antalet instanser vid en tidpunkt som du förutser mindre efterfrågan, till exempel helgen.

1. Välj **Skala** på menyn till vänster i skalningsfönstret. Om du vill ta bort de befintliga reglerna för automatisk skalning som skapats i föregående exempel väljer du papperskorgen.

    ![Ta bort befintliga regler för automatisk skalning](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Välj att **lägga till ett skalningsvillkor**. Välj pennikonen bredvid regelnamnet och ange ett namn som *Skala ut under varje arbetsdag*.

    ![Byta namn på standardregeln för automatisk skalning](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Välj alternativknappen **för Att skala till ett visst instansantal**.
4. Om du vill skala upp antalet instanser anger du *10* som instansantal.
5. Välj **Upprepa specifika dagar** för typen **Schema.**
6. Välj alla arbetsdagar, måndag till fredag.
7. Välj lämplig tidszon och ange sedan en **starttid** *på 09:00*.
8. Välj att **lägga till ett skalningsvillkor** igen. Upprepa processen för att skapa ett schema med namnet *Skala in under kvällen* som skalas till *3* instanser, upprepas varje veckodag och börjar klockan *18:00*.
9. Om du vill tillämpa dina schemabaserade regler för automatisk skalning väljer du **Spara**.

    ![Skapa regler för automatisk skalning som skalas enligt ett schema](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Om du vill se hur reglerna för automatisk skalning tillämpas väljer du **Körhistorik** överst i **skalningsfönstret.** Diagram- och händelselistan visar när reglerna för automatisk skalning utlöser och antalet VM-instanser i skalningsuppsättningen ökar eller minskar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du använder regler för automatisk skalning för att skala vågrätt och öka eller minska *antalet* VM-instanser i skalningsuppsättningen. Du kan också skala lodrätt för att öka eller minska *vm-instansens storlek*. Mer information finns i [Lodrät automatisk skalning med uppsättningar med virtuell datorskala](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar vm-instanser finns i [Hantera skalningsuppsättningar för virtuella datorer med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Mer information om hur du genererar aviseringar när reglerna för automatisk skalning utlöses finns i [Använda åtgärder för automatisk skalning för att skicka e-post- och webhook-aviseringar i Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Du kan också [använda granskningsloggar för att skicka e-post- och webhook-aviseringar i Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
