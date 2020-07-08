---
title: Hantera ögonblicks bilder med Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar ögonblicks bilder för en volym eller återställer från en ögonblicks bild till en ny volym med hjälp av Azure NetApp Files.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: ed13c61646bd2a6672b613964507d291a69a6821
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483609"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Hantera ögonblicksbilder med hjälp av Azure NetApp Files

Du kan använda Azure NetApp Files för att skapa en ögonblicks bild på begäran manuellt för en volym eller återställa från en ögonblicks bild till en ny volym. Azure NetApp Files tjänsten skapar inte automatiskt ögonblicks bilder av volymen.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Skapa en ögonblicks bild på begäran för en volym

Du kan bara skapa ögonblicks bilder på begäran. Ögonblicks bilds principer stöds inte för närvarande.

1.  Klicka på **ögonblicks bilder**från volym bladet.

    ![Navigera till ögonblicks bilder](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klicka på **+ Lägg till ögonblicks bild** för att skapa en ögonblicks bild på begäran för en volym.

    ![Lägg till ögonblicks bild](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  I fönstret ny ögonblicks bild anger du ett namn för den nya ögonblicks bilden som du skapar.   

    ![Ny ögonblicks bild](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klicka på **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Återställa en ögonblicks bild till en ny volym

För närvarande kan du bara återställa en ögonblicks bild till en ny volym. 
1. Gå till bladet **Hantera ögonblicks bilder** från volym bladet för att Visa ögonblicks bild listan. 
2. Välj en ögonblicks bild att återställa.  
3. Högerklicka på namnet på ögonblicks bilden och välj **Återställ till ny volym** på meny alternativet.  

    ![Återställ ögonblicks bild till ny volym](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Ange information för den nya volymen i fönstret ny volym:  
    * **Namn**   
        Ange namnet på den volym du skapar.  
        
        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Fil Sök väg**     
        Ange filsökvägen som ska användas för att skapa exportvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.   
        
        Ett monteringsmål är slutpunkten för NFS-IP-adressen. Det skapas automatiskt.   
        
        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt. 

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.

    *   **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  
        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Du kan endast komma åt Azure NetApp Files från samma VNet eller från ett VNet som finns i samma region som volymen via VNet-peering. Du kan komma åt volymen från ditt lokala nätverk via Express Route. 

    * **Delnät**  
        Ange det undernät som du vill använda för volymen.  
        Under nätet som du anger måste vara delegerat till Azure NetApp Files tjänsten. Du kan skapa ett nytt undernät genom att välja **Skapa nytt** under under näts fältet.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Klicka på **OK**.   
    Den nya volymen som ögonblicks bilden återställs till visas på bladet volymer.

## <a name="next-steps"></a>Nästa steg

[Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
