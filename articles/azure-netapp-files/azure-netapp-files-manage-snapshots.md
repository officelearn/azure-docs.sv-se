---
title: Hantera ögonblicksbilder med hjälp av Azure NetApp-filer | Microsoft-dokument
description: Beskriver hur du skapar ögonblicksbilder för en volym eller återställer från en ögonblicksbild till en ny volym med hjälp av Azure NetApp Files.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267907"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Hantera ögonblicksbilder med hjälp av Azure NetApp-filer

Du kan använda Azure NetApp-filer för att manuellt skapa en ögonblicksbild på begäran för en volym eller återställa från en ögonblicksbild till en ny volym. Azure NetApp Files-tjänsten skapar inte automatiskt volymögonblicksbilder.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Skapa en ögonblicksbild på begäran för en volym

Du kan bara skapa ögonblicksbilder på begäran. Principer för ögonblicksbilder stöds för närvarande inte.

1.  Klicka på **Ögonblicksbilder**i bladet Volym .

    ![Navigera till ögonblicksbilder](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klicka på **+ Lägg till ögonblicksbild** om du vill skapa en ögonblicksbild på begäran för en volym.

    ![Lägg till ögonblicksbild](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  I fönstret Ny ögonblicksbild anger du ett namn på den nya ögonblicksbild som du skapar.   

    ![Ny ögonblicksbild](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klicka på **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Återställa en ögonblicksbild till en ny volym

För närvarande kan du återställa en ögonblicksbild endast till en ny volym. 
1. Gå till bladet **Hantera ögonblicksbilder** från volymbladet för att visa ögonblicksbildlistan. 
2. Välj en ögonblicksbild som ska återställas.  
3. Högerklicka på ögonblicksbildnamnet och välj **Återställ till ny volym** på menyalternativet.  

    ![Återställa ögonblicksbild till ny volym](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Ange information om den nya volymen i fönstret Ny volym:  
    * **Namn**   
        Ange namnet på den volym du skapar.  
        
        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Sökväg för filer**     
        Ange filsökvägen som ska användas för att skapa exportvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.   
        
        Ett monteringsmål är slutpunkten för NFS-IP-adressen. Det skapas automatiskt.   
        
        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt. 

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.

    *   **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  
        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Du kan bara komma åt Azure NetApp-filer från samma virtuella nätverk eller från ett virtuella nätverk som finns i samma region som volymen via Vnet-peering. Du kan komma åt volymen från det lokala nätverket via Express Route. 

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Undernätet som du anger måste delegeras till Azure NetApp Files-tjänsten. Du kan skapa ett nytt undernät genom att välja **Skapa nytt** under fältet Undernät.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Klicka på **OK**.   
    Den nya volymen som ögonblicksbilden återställs till visas i bladet Volymer.

## <a name="next-steps"></a>Nästa steg

[Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
