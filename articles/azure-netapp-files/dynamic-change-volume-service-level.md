---
title: Ändra service nivån för en volym dynamiskt för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du ändrar Service nivån för en volym dynamiskt.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579087"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Ändra tjänstnivå för en volym dynamiskt

> [!IMPORTANT] 
> * Den offentliga förhands gransknings registreringen för den här funktionen är stoppad tills vidare meddelande. 
> * Det finns för närvarande inte stöd för att dynamiskt ändra service nivån för en målvolym för replikering.

Du kan ändra service nivån för en befintlig volym genom att flytta volymen till en annan kapacitets pool som använder den [tjänste nivå](azure-netapp-files-service-levels.md) som du vill använda för volymen. Den här ändringar på plats på service nivå för volymen kräver inte att du migrerar data. Det påverkar inte heller åtkomsten till volymen.  

Med den här funktionen kan du uppfylla dina arbets belastnings behov på begäran.  Du kan ändra en befintlig volym för att använda en högre service nivå för bättre prestanda eller för att använda en lägre service nivå för kostnads optimering. Om volymen till exempel för närvarande finns i en kapacitetsutnyttjande som använder *standard* Service nivån och du vill att volymen ska använda Service nivån *Premium* , kan du flytta volymen dynamiskt till en pool som använder *Premium* Service-nivån.  

Den kapacitets grupp som du vill flytta volymen till måste redan finnas. Kapacitets gruppen kan innehålla andra volymer.  Om du vill flytta volymen till en helt ny kapacitets uppsättning måste du skapa en pool för [kapacitet](azure-netapp-files-set-up-capacity-pool.md) innan du flyttar volymen.  

## <a name="considerations"></a>Överväganden

* När volymen har flyttats till en annan kapacitets pool kommer du inte längre att ha åtkomst till de tidigare volym aktivitets loggarna och volym måtten. Volymen börjar med nya aktivitets loggar och mät värden under den nya kapacitets gruppen.

* Om du flyttar en *volym till en* kapacitets uppsättning med en högre service nivå (till exempel genom att flytta från *standard* till *Premium* eller *Ultra* Service Level) måste du vänta minst sju dagar innan du kan flytta volymen till en kapacitets pool med en lägre service nivå (till exempel genom att flytta från *Ultra* till *Premium* eller *standard* ).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Flytta en volym till en annan kapacitets grupp

1.  På sidan volymer högerklickar du på den volym vars service nivå du vill ändra. Välj **ändra pool**.

    ![Högerklicka på volym](../media/azure-netapp-files/right-click-volume.png)

2. I fönstret Ändra pool väljer du den kapacitets grupp som du vill flytta volymen till. 

    ![Ändra pool](../media/azure-netapp-files/change-pool.png)

3.  Klicka på **OK**.


## <a name="next-steps"></a>Nästa steg  

* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
