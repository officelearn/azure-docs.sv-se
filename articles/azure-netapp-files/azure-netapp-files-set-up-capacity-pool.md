---
title: Konfigurera en kapacitetspool för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du ställer in en kapacitetspool så att du kan skapa volymer inom den.
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
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: b-juche
ms.openlocfilehash: b21db3e842898e8ce11b560714888b946373300e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80616438"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool

När du konfigurerar en kapacitetspool kan du skapa volymer inom den.  

## <a name="before-you-begin"></a>Innan du börjar 

Du måste redan ha skapat ett NetApp-konto.   

[Skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Steg 

1. Gå till hanteringsbladet för NetApp-kontot. I navigeringsfönstret klickar du på **Kapacitetspooler**.  
    
    ![Gå till kapacitetspoolen](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Klicka på **+ Lägg till pooler** när du vill skapa en ny kapacitetspool.   
    Fönstret Ny kapacitetspool visas.

3. Ange följande information för den nya kapacitetspoolen:  
   * **Namn**  
     Ange ett namn för kapacitetspoolen.  
     Namnet på kapacitetspoolen måste vara unikt för varje NetApp-konto.

   * **Service nivå**   
     Det här fältet visar målprestanda för kapacitetspoolen.  
     Ange service nivå för kapacitets gruppen: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)eller [**standard**](azure-netapp-files-service-levels.md#Standard).

   * **Ändra**     
     Ange storleken på den kapacitetspool som du köper.        
     Den minsta kapacitetspoolstorleken är 4 TiB. Du kan skapa poolstorlekar som är multiplar av 4 TiB.   
      
     ![Ny kapacitetspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg 

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- Priserna för olika tjänstnivåer finns på [prissättningssidan för Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
