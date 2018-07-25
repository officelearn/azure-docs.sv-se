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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011100"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Skapa en volym för Azure NetApp Files

En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.  Du kan skapa flera volymer i en kapacitetspool men volymernas totala kapacitetsförbrukning får inte överskrida poolens storlek. 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.  

[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>Steg 
1.  Klicka på bladet **Volymer** via bladet Hantera kapacitetspooler. 
2.  Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Ny volym visas.

3.  I fönstret Ny volym klickar du på **Skapa** och fyller i följande fält:   
    * **Namn**      
        Ange namnet på den volym du skapar.   
        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst 3 tecken.  Du kan använda alla alfanumeriska tecken.

    * **Filsökväg**  
        Ange filsökvägen som ska användas för att skapa exportvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.   
        Ett monteringsmål är slutpunkten för NFS-IP-adressen. Det skapas automatiskt.    
        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt.  

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  
        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen. Det virtuella nätverk du anger måste vara konfigurerat för Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås via ett virtuellt nätverk som finns på samma plats som volymen.   

    ![Ny volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Klicka på **OK**. 
 
En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="next-steps"></a>Nästa steg  
[Konfigurera exportprincipen för en volym (valfritt)](azure-netapp-files-configure-export-policy.md)

