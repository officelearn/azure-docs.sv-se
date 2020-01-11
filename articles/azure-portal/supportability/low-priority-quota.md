---
title: Kvot för virtuell dator på plats | Microsoft Docs
description: Öka kvot gränserna genom att göra kvot begär Anden
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897304"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Punkt kvot: öka gränserna för alla VM-serier

Virtuella datorer (VM) tillhandahåller en annan modell för Azure-användning. De låter dig säga lägre kostnader i Exchange för att låta Azure ta bort virtuella datorer efter behov för distributioner med betala per användning eller reserverad VM-instans. Mer information om virtuella datorer finns i [Azure-virtuella datorer för skalnings uppsättningar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:
* *Virtuella datorer med betala per* användning och *reserverade VM-instanser* omfattas av en *standard vCPU-kvot*.
* *Virtuella datorer för virtuella datorer* omfattas av en *vCPU kvot*. 

För *kvot typen vCPU* används Resource Manager vCPU kvoter i alla tillgängliga VM-serier som en enda regional gräns.

När du distribuerar en ny virtuell dator, får den totala nya och befintliga vCPU-användningen för alla VM-instanser för virtuella datorer inte överskrida den godkända kvot gränsen för vCPU. Om kvoten överskrids tillåts inte distributionen av virtuella datorer. 

Den här artikeln beskriver hur du begär en ökning av kvot gränsen för vCPU med hjälp av Azure Portal. 

Mer information om standard kvoter för vCPU finns i [vCPU-kvoter för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [begränsningar för Azure-prenumerationer och tjänster](https://aka.ms/quotalimits). 

Mer information om hur du ökar vCPU-gränsen per region finns i [standard kvot: regional vCPU Limit ökning](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Begär en kvot gräns ökning från fönstret Hjälp + Support 

Om du vill begära en gräns för plats kvots ökning för alla VM-serier från **Hjälp + Support** -fönstret, gör du följande:

> [!NOTE]
> Du kan också begära en ökad kvot gräns för flera regioner genom ett enda support ärende. Mer information finns i steg 8. 

1. I den vänstra rutan i [Azure Portal](https://portal.azure.com)väljer du **Hjälp + Support**.

   ![Länken "Help + support"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. I **Hjälp + Support** -fönstret väljer du **ny supportbegäran**. 

    ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. I list rutan **typ av ärende** väljer du begränsningar för **tjänsten och prenumerationen (kvoter)** .

   ![List rutan utfärdande typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. I list rutan **prenumeration** väljer du den prenumeration vars kvot du vill öka.

   ![List rutan "prenumeration"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. I list rutan **kvot typ** väljer du **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar**. 

   ![List rutan "kvot typ"](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj fliken **information** och välj **Ange information**under **problem information**och ange ytterligare information som kan hjälpa dig att bearbeta din begäran.

   ![Länken "Tillhandahåll information"](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. I fönstret **kvot information** längst upp till höger gör du följande:

   ![Fönstret "kvot information"](./media/resource-manager-core-quotas-request/3-7.png)

   a. Välj lämplig modell i list rutan **distributions modell** .

   b. Välj en plats i list rutan **platser** . För den valda platsen, under **typer**, i rutan **Välj typ** , anger du **dekor**. 
   
   ![Fliken "ny support förfrågan"](./media/resource-manager-core-quotas-request/3-8.png)

    Under **typer**kan du begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar. 
    
    Mer information finns i [standard kvot: serien per VM-serien vCPU Limit ökning](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Ange den nya kvot gränsen som du vill använda för den här prenumerationen. 
 
   ![Text rutan "ny vCPU gräns"](./media/resource-manager-core-quotas-request/3-9.png)

1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i list rutan och väljer sedan en lämplig VM-typ. Du kan sedan ange en gräns som gäller för den ytterligare platsen.

   ![Ytterligare platser i fönstret "kvot information"](./media/resource-manager-core-quotas-request/3-10.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Begär en kvot gräns ökning från fönstret prenumerationer

Gör så här för att begära en gräns för plats kvots ökning för alla VM-serier från fönstret **prenumerationer** :

> [!NOTE]
> Du kan också begära en ökad kvot gräns för flera regioner genom ett enda support ärende. Mer information finns i steg 7. 

1. I den vänstra rutan i [Azure Portal](https://portal.azure.com)väljer du **prenumerationer**.

   ![Länken "prenumerationer"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Fönstret prenumerationer](./media/resource-manager-core-quotas-request/select-subscription.png)

1. I den vänstra rutan på sidan **\<prenumerations namn >** väljer du **användning + kvoter**.

   ![Länken "användning + kvoter"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. Välj **begär ökning**längst upp till höger.

   ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

1. I list rutan **kvot typ** väljer du **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar**.

   ![List rutan "kvot typ"](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. I fönstret **kvot information** längst upp till höger gör du följande:

   ![Fönstret "kvot information"](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. Välj lämplig modell i list rutan **distributions modell** .

   b. Välj en plats i list rutan **platser** . 
   
   c. För den valda platsen, under **typer**, i rutan **Välj typ** , anger du **dekor**.

   ![Fönstret "kvot information"](./media/resource-manager-core-quotas-request/3-2-7.png)

   Mer information finns i [standard kvot: serien per VM-serien vCPU Limit ökning](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Ange den nya kvot gränsen som du vill använda för den här prenumerationen.

   ![Text rutan "ny vCPU gräns"](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i list rutan och väljer sedan en lämplig VM-typ. Du kan sedan ange en gräns som gäller för den ytterligare platsen.

   ![Ytterligare platser i fönstret "kvot information"](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Välj **Spara och fortsätt** att skapa support förfrågan.


