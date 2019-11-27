---
title: Kvot för låg prioritet | Microsoft Docs
description: Kvot begär Anden med låg prioritet
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 49ac478330cf73dff050a3edcc15933692fa6448
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535176"
---
# <a name="low-priority-quota-limit-increase-for-all-vm-series"></a>Kvot för låg prioritet: begränsa ökningen för alla VM-serier

Virtuella datorer med låg prioritet har en annan modell av Azure-användning, handel med lägre kostnad för att låta Azure ta bort en virtuell dator efter behov för distributioner med betala per användning eller reserverade VM-instanser. Läs mer om virtuella datorer med låg prioritet [här](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority).

Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer. Använd standard kvot **för virtuella datorer som du betalar per användning och reserverade VM-instanser** . **Virtuella datorer med låg prioritet** använder låg prioritets kvot. 

För **kvot typen med låg prioritet** tillämpas kvoterna för vCPU i alla tillgängliga VM-serier som en enda regional gräns.

När en ny virtuell dator med låg prioritet ska distribueras måste summan av den nya och befintliga virtuella processorer-användningen för alla virtuella datorer med låg prioritet inte överskrida den godkända kvot gränsen för låg prioritets vCPU. Om kvoten för låg prioritet överskrids, tillåts inte distributionen av den virtuella datorn med låg prioritet. Du kan begära en ökning av kvot gränsen för virtuella processorer med låg prioritet från Azure Portal. 

Läs mer om standard kvoter för vCPU på sidan för virtuella datorer vCPU kvoter och sidan för Azure-prenumeration och tjänst begränsningar. Läs mer om hur du ökar den regionala vCPU-gränsen för standard kvot på den här [sidan](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Du kan nu begära en ökning av **kvot gränser med låg prioritet för alla VM-serier** via **Hjälp + Support** -bladet eller **användnings** -och kvot bladet i portalen.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Begär låg prioritets gräns för kvot ökning för alla VM-serier per prenumeration med hjälp + support-bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal.

Du kan också **begära kvot för flera regioner** genom ett enda support ärende. Mer information finns i steg 10 nedan. 


1. Välj **Hjälp + Support**från https://portal.azure.com.

   ![Hjälp + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

     ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. I list rutan ärende typ väljer du **tjänst-och prenumerations gränser (kvoter)** .

   ![Listruta för problem typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

   ![Välj prenumerations nyheter](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** i list rutan för **kvot typ** . 

   ![Välj typ av kvot](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. I **problem information**anger du ytterligare information som hjälper dig att bearbeta din begäran genom att klicka på **Ange information**.

   ![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  Välj **distributions modell** på panelen **kvot information*** och välj en **plats**.

![Ange information](./media/resource-manager-core-quotas-request/3-7.png)

8. För den valda platsen väljer du **Ange** värde som **"låg prioritet"** . Du kan begära både standard-och låg prioritets kvot typer från ett enda support ärende via stöd för flera markeringar i fältet **typ** . Läs mer om att **öka standard kvoten per VM-serien** på den här [sidan](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Ange information](./media/resource-manager-core-quotas-request/3-8.png)

9.  Ange den nya gräns som du vill använda för prenumerationen. 
 
 ![Ange information](./media/resource-manager-core-quotas-request/3-9.png)

10. Om du vill begära kvot för mer än en plats kan du kontrol lera en annan plats i list rutan och välja lämplig VM-typ. Du kan sedan ange de nya gränser som du vill använda.

![Ange information](./media/resource-manager-core-quotas-request/3-10.png)

11. När du har angett önskad kvot klickar du på **Spara och fortsätter** med att fortsätta med att skapa support förfrågan i panelen kvot information.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Begär låg prioritets gräns för kvot ökning för alla VM-serier per prenumeration med användnings-och kvot blad

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal.

Du kan också **begära kvot för flera regioner** genom ett enda support ärende. Mer information finns i steg 9 nedan. 

1.  Välj **prenumerationer**från https://portal.azure.com.

 ![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Välj den prenumeration som behöver en ökad kvot.

 ![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Välj **användning + kvoter**.

 ![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  I det övre högra hörnet väljer du **begär ökning.**

   ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** som offert typ.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  Välj distributions modell på panelen **kvot information** och välj en plats.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  För den valda platsen väljer du **Ange** värde som **"låg prioritet".** Du kan begära både standard-och låg prioritets kvot typer från ett enda support ärende via stöd för flera markeringar i fältet **typ** . Läs mer om att **öka standard kvoten per VM-serien** på den här [sidan](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Ange den nya gräns som du vill använda för prenumerationen.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Om du vill begära kvot för mer än en plats kan du kontrol lera en annan **plats** i list rutan och välja lämplig VM-typ. Du kan sedan ange de nya gränser som du vill använda.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. När du har angett önskad kvot klickar du på **Spara och fortsätter** med att fortsätta med att skapa support förfrågan i panelen kvot information.

