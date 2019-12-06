---
title: Kvot för punkt | Microsoft Docs
description: Begär Anden om kvot begär Anden
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850575"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Kvot för punkt: begränsa ökningen för alla VM-serier

Virtuella datorer för virtuella datorer ger en annan modell av Azure-användning, en lägre kostnad för att låta Azure ta bort en virtuell dator efter behov för distributioner med betala per användning eller reserverad VM-instans. Läs mer om de virtuella datorerna på plats [här](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer. Använd standard kvot **för virtuella datorer som du betalar per användning och reserverade VM-instanser** . **Virtuella datorer** som använder punkt kvoter. 

För en **kvot** typ tillämpas Resource Manager-vCPU kvoter i alla tillgängliga VM-serier som en enda regional gräns.

När en ny virtuell dator ska distribueras måste summan av nya och befintliga virtuella processorer-användningar för alla VM-instanser för virtuella datorer inte överskrida den godkända kvot gränsen för vCPU. Om kvoten överskrids tillåts inte distributionen av virtuella datorer. Du kan begära en ökning av virtuella processorer kvot gräns från Azure Portal. 

Läs mer om standard kvoter för vCPU på sidan för virtuella datorer vCPU kvoter och sidan för Azure-prenumeration och tjänst begränsningar. Läs mer om hur du ökar den regionala vCPU-gränsen för standard kvot på den här [sidan](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Du kan nu begära en ökning av **kvot gränser för alla VM-serier** via **Hjälp + Support** -bladet eller **användnings** -och kvot bladet i portalen.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Begäran om kvot gräns för begär ande punkt för alla VM-serier per prenumeration via hjälp + support-bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal.

Du kan också **begära kvot för flera regioner** genom ett enda support ärende. Mer information finns i steg 10 nedan. 


1. Välj **Hjälp + Support**från https://portal.azure.com.

   ![Hjälp + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
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

8. För den valda platsen väljer du **Ange** värde som **"dekor"** . Du kan begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar i fältet **typ** . Läs mer om att **öka standard kvoten per VM-serien** på den här [sidan](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Ange information](./media/resource-manager-core-quotas-request/3-8.png)

9.  Ange den nya gräns som du vill använda för prenumerationen. 
 
 ![Ange information](./media/resource-manager-core-quotas-request/3-9.png)

10. Om du vill begära kvot för mer än en plats kan du kontrol lera en annan plats i list rutan och välja lämplig VM-typ. Du kan sedan ange de nya gränser som du vill använda.

![Ange information](./media/resource-manager-core-quotas-request/3-10.png)

11. När du har angett önskad kvot klickar du på **Spara och fortsätter** med att fortsätta med att skapa support förfrågan i panelen kvot information.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Begäran om kvot gräns för begär ande punkt för alla VM-serier per prenumeration med användnings-och kvot blad

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal.

Du kan också **begära kvot för flera regioner** genom ett enda support ärende. Mer information finns i steg 9 nedan. 

1.  Välj **prenumerationer**från https://portal.azure.com.

 ![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Välj den prenumeration som behöver en ökad kvot.

 ![Välj prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Välj **användning + kvoter**.

 ![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  I det övre högra hörnet väljer du **begär ökning.**

   ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** som offert typ.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  Välj distributions modell på panelen **kvot information** och välj en plats.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  För den valda platsen väljer du **Ange** värde som **"dekor".** Du kan begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar i fältet **typ** . Läs mer om att **öka standard kvoten per VM-serien** på den här [sidan](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Ange den nya gräns som du vill använda för prenumerationen.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Om du vill begära kvot för mer än en plats kan du kontrol lera en annan **plats** i list rutan och välja lämplig VM-typ. Du kan sedan ange de nya gränser som du vill använda.

  ![Fyll i formulär](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. När du har angett önskad kvot klickar du på **Spara och fortsätter** med att fortsätta med att skapa support förfrågan i panelen kvot information.


