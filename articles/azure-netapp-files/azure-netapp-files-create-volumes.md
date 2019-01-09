---
title: Skapa en volym för Azure NetApp Files| Microsoft Docs
description: Beskriver hur du skapar en volym för Azure NetApp-filer.
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
ms.topic: get-started-article
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 937ff9cf4c099f229df28070be07ba76339704e0
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584011"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Skapa en volym för Azure NetApp Files

En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.  Du kan skapa flera volymer i en kapacitetspool men volymernas totala kapacitetsförbrukning får inte överskrida poolens storlek. 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Steg 
1.  Klicka på bladet **Volymer** via bladet Hantera kapacitetspooler. 
2.  Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Ny volym visas.

3.  I fönstret Ny volym klickar du på **Skapa** och fyller i följande fält:   
    * **Namn**      
        Ange namnet på den volym du skapar.   

        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Filsökväg**  
        Ange filsökvägen som ska användas för att skapa exportvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.   
     
        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt.  

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan även komma åt volymen från ditt lokala nätverk via ExpressRoute.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. Observera att endast undernät kan delegeras till Azure NetApp Files i varje virtuellt nätverk.   
 
        ![Ny volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Klicka på **OK**. 
 
En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="next-steps"></a>Nästa steg  
* [Konfigurera exportprincipen för en volym (valfritt)](azure-netapp-files-configure-export-policy.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

