---
title: "Automatisk skala en tjänst i molnet i den klassiska portalen | Microsoft Docs"
description: "(klassisk) Lär dig hur du använder den klassiska portalen för att konfigurera regler för Automatisk skala för en cloud service-webbroll eller worker-rollen i Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 90d55bbac6e113d6add848ace67cf0749e26342b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>Så här konfigurerar du automatisk skalning för en molntjänst i den klassiska portalen
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-scale-portal.md)
> * [Klassisk Azure-portal](cloud-services-how-to-scale.md)

Du kan konfigurera automatiska inställningar för din webbroll eller worker-rollen på sidan skala i den klassiska Azure-portalen. Du kan också konfigurera manuell skalning i stället för regelbaserade automatisk skalning.

> [!NOTE]
> Den här artikeln fokuserar på Molntjänsten webb- och arbetsroller roller. När du skapar en virtuell dator (klassisk) direkt finns det i en molntjänst. En del av den här informationen gäller för dessa typer av virtuella datorer. Skala en tillgänglighetsuppsättning för virtuella datorer stängs bara dem och inaktivera skala regler som du konfigurerar. Läs mer om virtuella datorer och tillgänglighetsuppsättningar [hantera tillgängligheten för virtuella datorer](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Innan du konfigurerar skalning för ditt program bör du tänka på följande information:

* Skalning påverkas av kärnor användning.

    Större rollinstanser använda flera kärnor. Du kan skala ett program bara inom ramen för kärnor för prenumerationen. Anta till exempel har en begränsning på 20 kärnor för prenumerationen. Om du kör ett program med två medelstora molntjänster (totalt 4 kärnor) kan du bara skala upp andra distributioner av molntjänster i din prenumeration av återstående 16 kärnor. Mer information om storlekar finns [Molntjänststorlekar](cloud-services-sizes-specs.md).

* Du måste skapa en kö och koppla den till en roll innan du kan skala en applikation baserat på ett tröskelvärde för meddelandet. Mer information finns i [hur du använder tjänsten Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Du kan skala resurser som är länkade till Molntjänsten. Mer information om hur du länkar resurser finns [så här: länka en resurs till en molntjänst](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

* Om du vill aktivera hög tillgänglighet för ditt program bör du kontrollera att den har distribuerats med två eller flera rollinstanser. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="schedule-scaling"></a>Schemalägga skalning
Som standard följer inte alla roller för ett visst schema. Därför kan dessa inställningar ändras gäller för alla tider och alla dagar året. Om du vill kan konfigurera du manuell eller automatisk skalning av något av följande lägen:

* Veckodagar
* Helger
* Vecka kvällar
* Vecka mornings
* Specifika datum
* Specifika datumintervall

Schema-inställningen har konfigurerats i den [klassiska Azure-portalen](https://manage.windowsazure.com/) på den  
**Molntjänster** > **\[Molntjänsten\]** > **skala**  >   **\[Produktion eller mellanlagring\]**  sidan.

Klicka på den **ställer in schema gånger** knappen för varje roll som du vill ändra.

![Molntjänsten automatisk skalning enligt ett schema][scale_schedules]

## <a name="manual-scale"></a>Manuell skala
På den **skala** kan du kan manuellt öka eller minska antalet instanser som körs i en molntjänst. Den här inställningen konfigureras för varje schema som du har skapat eller hela tiden om du inte har skapat ett schema.

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/), klickar du på **molntjänster**, och klicka sedan på namnet på Molntjänsten att öppna instrumentpanelen.
   
   > [!TIP]
   > Om du inte hittar din molntjänst kan du behöva ändra från **produktion** till **mellanlagring** eller vice versa.

2. Klicka på **skala**.
3. Välj det schema som du vill ändra skalningsalternativ för. *Inte schemalagda tider* är standardvärdet om du har inga scheman som definierats.
4. Hitta de **skala efter mått** avsnittet och väljer **NONE**. Den här inställningen är standard för alla roller.
5. Varje roll i Molntjänsten har en skjutreglaget för att ändra antalet instanser som ska användas.
   
    ![Skala manuellt en rolltjänst för molnet][manual_scale]
   
    Om du behöver fler instanser kan du behöva ändra den [molnet storleken för den virtuella datorn](cloud-services-sizes-specs.md).
6. Klicka på **Spara**.  
   Rollinstanser läggs till eller tas bort baserat på dina val.

> [!TIP]
> När du ser ![][tip_icon] flytta musen till den och du kan få hjälp om en specifik inställning har.

## <a name="automatic-scale---cpu"></a>Automatisk skalning - processor
Det här läget skalas om den genomsnittliga procentandelen av CPU-användningen går över eller under angivna tröskelvärden. Rollinstanser skapas eller tas bort när detta sker.

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/), klickar du på **molntjänster**, och klicka sedan på namnet på Molntjänsten att öppna instrumentpanelen.
   
   > [!TIP]
   > Om du inte hittar din molntjänst kan du behöva ändra från **produktion** till **mellanlagring** eller vice versa.

2. Klicka på **skala**.
3. Välj det schema som du vill ändra skalningsalternativ för. *Inte schemalagda tider* är standardvärdet om du har inga scheman som definierats.
4. Hitta de **skala efter mått** avsnittet och väljer **CPU**.
5. Du kan nu konfigurera en lägsta och högsta antal instanser av roller, mål CPU-användning (för att utlösa en skala upp) och hur många instanser som du vill skala upp eller ned genom.

![Skala en rolltjänst i molnet genom att cpu-belastning][cpu_scale]

> [!TIP]
> När du ser ![][tip_icon] flytta musen till den och du kan få hjälp om en specifik inställning har.

## <a name="automatic-scale---queue"></a>Automatisk skalning - kön
Det här läget skalas automatiskt om antalet meddelanden i en kö går över eller under ett angivet tröskelvärde. Rollinstanser skapas eller tas bort när detta sker.

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/), klickar du på **molntjänster**, och klicka sedan på namnet på Molntjänsten att öppna instrumentpanelen.
   
   > [!TIP]
   > Om du inte hittar din molntjänst kan du behöva ändra från **produktion** till **mellanlagring** eller vice versa.

2. Klicka på **skala**.
3. Hitta de **skala efter mått** avsnittet och väljer **KÖN**.
4. Du kan nu konfigurera en lägsta och högsta antal roller instanser, kö- och antal meddelanden i kö ska behandlas för varje instans och hur många instanser att skala upp eller ned genom.

![Skala en rolltjänst för molntjänster genom en meddelandekö][queue_scale]

> [!TIP]
> När du ser ![][tip_icon] flytta musen till den och du kan få hjälp om en specifik inställning har.

## <a name="scale-linked-resources"></a>Skala länkade resurserna
Ofta när du skalar en roll, är det bra att skala databasen som programmet använder också. Om du länka databasen till Molntjänsten, kan du komma åt skalning inställningarna för den här resursen genom att klicka på länken.

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/), klickar du på **molntjänster**, och klicka sedan på namnet på Molntjänsten att öppna instrumentpanelen.
   
   > [!TIP]
   > Om du inte hittar din molntjänst kan du behöva ändra från **produktion** till **mellanlagring** eller vice versa.

2. Klicka på **skala**.
3. Hitta de **länkade resurser** avsnittet och klicka på **hantera skala för den här databasen**.
   
   > [!NOTE]
   > Om du inte ser en **länkade resurser** avsnittet du förmodligen inte har några länkade resurser.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
