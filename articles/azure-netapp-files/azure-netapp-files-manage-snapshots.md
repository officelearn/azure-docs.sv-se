---
title: Hantera ögonblicksbilder med hjälp av Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar en ögonblicksbild av på begäran för en volym eller en återställning från en ögonblicksbild till en ny volym med hjälp av Azure NetApp-filer.
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
ms.topic: how-to-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e3ae11adf84e858429cba4643802300f7915a166
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412949"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Hantera ögonblicksbilder med hjälp av Azure NetApp filer
Du kan använda Azure NetApp filer om du vill skapa en ögonblicksbild av på begäran för en volym eller återställa från en ögonblicksbild till en ny volym.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Skapa en ögonblicksbild av på begäran för en volym
Du kan skapa ögonblicksbilder endast på begäran.  Principer för ögonblicksbilden stöds inte för närvarande.  
1.  Hantera volym-bladet klickar du på **ögonblicksbilder**, klicka sedan på **+ Lägg till ögonblicksbild** att skapa en ögonblicksbild av på begäran för en volym.

2.  Ange ett namn för den nya ögonblicksbild som du skapar i fönstret Ny ögonblicksbild.   

3. Klicka på **OK**. 


## <a name="restore-a-snapshot-to-a-new-volume"></a>Återställa en ögonblicksbild till en ny volym
För närvarande kan återställa du en ögonblicksbild endast till en ny volym. 
1. Gå till den **hantera ögonblicksbilder** bladet från bladet volym för att visa listan över ögonblicksbilder. 
2. Välj en ögonblicksbild för att återställa.  
3. Högerklicka på namnet på ögonblicksbilden och välj **Återställ till ny volym** från menyalternativet.  

    ![Återställ ögonblicksbild till ny volym](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Ange information för den nya volymen i fönstret Ny volym:  
    * **Namn**   
        Ange namnet på den volym du skapar.  
        
        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Filsökväg**     
        Ange filsökvägen som ska användas för att skapa exportvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.   
        
        Ett monteringsmål är slutpunkten för NFS-IP-adressen. Det skapas automatiskt.   
        
        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt. 

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.

    *   **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  
        Det virtuella nätverket som du anger måste ha ett undernät som delegerats till Azure NetApp-filer. Tjänsten Azure NetApp Files kan nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volym via Vnet-peering. Du kan också komma åt volymen från ditt lokala nätverk via Expressroute. 

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files-tjänsten. Du kan skapa ett nytt undernät genom att välja **Skapa nytt** under fältet undernät.  
<!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
-->

5. Klicka på **OK**.   
    Den nya volymen som ögonblicksbilden återställs visas i bladet volymer.

