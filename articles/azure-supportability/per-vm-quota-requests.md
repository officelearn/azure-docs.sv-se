---
title: Begär en ökning av vCPU kvot gränser per Azure VM-serien | Microsoft Docs
description: Den här artikeln beskriver hur du begär kvot gräns ökningar per VM-vCPU.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750190"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standard kvot: öka gränserna efter VM-serien

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:
* *Virtuella datorer med betala per* användning och *reserverade VM-instanser* omfattas av en *standard vCPU-kvot*.
* *Virtuella datorer för virtuella datorer* omfattas av en *vCPU kvot*. 

Standard kvoten för vCPU för betala per användning och reserverade VM-instanser tillämpas på två nivåer för varje prenumeration i varje region:
* Den första nivån är den *totala regionala virtuella processorer-gränsen* (i alla VM-serier).
* Den andra nivån är *virtuella processorer-gränsen per VM-serien* (till exempel dv3-serien virtuella processorer). 

När du distribuerar en ny virtuell dator, får den totala nya och befintliga vCPU-användningen för VM-serien inte överskrida den godkända vCPU-kvoten för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga virtuella processorer som distribueras i alla VM-serien inte överstiga den totala godkända regionala vCPU-kvoten för prenumerationen. Om någon av dessa kvoter överskrids tillåts inte VM-distributionen.

Du kan begära en ökning av vCPU kvot gräns för VM-serien med hjälp av Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala vCPU-gränsen med samma belopp. 

Mer information om standard kvoter för vCPU finns i [vCPU-kvoter för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [begränsningar för Azure-prenumerationer och tjänster](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Mer information om hur du ökar vCPU-gränsen per region för standard kvot finns i [standard kvot: öka gränserna efter region](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Mer information om hur du ökar vCPU-gränser för virtuella datorer finns i [punkt kvot: öka gränserna för alla VM-serier](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Du kan begära en ökning av standard kvot gränser för vCPU per VM-serien på två sätt, enligt beskrivningen i nästa avsnitt.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Begär en standard kvot ökning från rutan "hjälp + support"

Om du vill begära en standard vCPU för kvot ökning per VM-serien från **Hjälp + Support** -fönstret, gör du följande: 

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

1. På fliken **information** under **problem information**väljer du **Ange information**och anger sedan ytterligare information som kan hjälpa dig att bearbeta din begäran.

   ![Länken "Tillhandahåll information"](./media/resource-manager-core-quotas-request/provide-details.png)

1. I fönstret **kvot information** längst upp till höger gör du följande:

   ![Fönstret "kvot information"](./media/resource-manager-core-quotas-request/1-7.png)

   a. Välj lämplig modell i list rutan **distributions modell** .

   b. Välj en plats i list rutan **platser** . Ange **standard**under **typer**i rutan **Välj typ** för den valda platsen.

   ![Fönstret kvot information – kvot typer](./media/resource-manager-core-quotas-request/1-8.png)

   Under **typer**kan du begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar.
   
   Mer information om hur du ökar kvot gränserna finns i [Azure-virtuella datorer för skalnings uppsättningar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   c. Under List rutan **standard** väljer du den SKU-serie vars kvoter du vill öka.

   ![Fönstret med kvot information – SKU-serien](./media/resource-manager-core-quotas-request/1-9.png)

   d. Ange de nya kvot gränser som du vill använda för den här prenumerationen. Om du vill ta bort en SKU från listan avmarkerar du kryss rutan bredvid SKU eller väljer **borttagnings** ikonen (X). 

   ![Text rutan "ny vCPU gräns"](./media/resource-manager-core-quotas-request/1-10.png)

1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i list rutan och väljer sedan en lämplig VM-typ. Du kan sedan ange en gräns som gäller för den ytterligare platsen.

   ![Ytterligare platser i fönstret "kvot information"](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Välj **Spara och fortsätt** att skapa support förfrågan.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Begär en standard kvot ökning från fönstret prenumerationer

Om du vill begära en standard vCPU för kvot ökning per VM-serien från fönstret **prenumerationer** gör du följande:

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

   ![Fönstret "kvot information"](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. Välj lämplig modell i list rutan **distributions modell** .

   b. Välj en plats i list rutan **platser** . 
   
   c. För den valda platsen, under **typer**, väljer du **Välj en typ**och markerar sedan kryss rutan **standard** .

   ![Kryss rutan "standard"](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   Under **typer**kan du begära både standard-och låg prioritets kvot typer från ett enda support ärende via stöd för flera markeringar.
   
   Mer information om hur du ökar kvot gränserna finns i [Azure-virtuella datorer för skalnings uppsättningar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Under List rutan **standard** väljer du den SKU-serie vars kvoter du vill öka.

   ![Fönstret med kvot information – SKU-serien](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Ange de nya kvot gränser som du vill använda för den här prenumerationen. Om du vill ta bort en SKU från listan avmarkerar du kryss rutan bredvid SKU: n eller väljer **ta bort** (X). 

   ![Text rutan "ny vCPU gräns"](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i list rutan och väljer sedan en lämplig VM-typ. 

   I det här steget förinstalleras den SKU-serie som du har valt för tidigare platser. Ange de kvot gränser som du vill använda för ytterligare serier.
   
   ![Ytterligare platser i fönstret "kvot information"](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Välj **Spara och fortsätt** att skapa support förfrågan.
