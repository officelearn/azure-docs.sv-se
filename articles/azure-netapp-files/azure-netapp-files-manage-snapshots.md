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
ms.openlocfilehash: 48cb88b9815ba723d93c18caf63f33b50eea850c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009198"
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
        Ange namnet för den volym som du skapar.  
        
        Namnet måste vara unika inom en resursgrupp. Det måste vara minst 3 tecken.  Det kan använda alla alfanumeriska tecken.

    * **Filsökväg**     
        Ange sökvägen till filen som ska användas för att skapa exportsökvägen för den nya volymen. Exportsökvägen används för att montera och komma åt volymen.   
        
        Ett monteringsmål är slutpunkten för NFS-IP-adress. Det genereras automatiskt.   
        
        Filens sökväg får innehålla bokstäver, siffror och bindestreck (”-”) endast. Det måste vara mellan 16 och 40 tecken. 

    * **Kvot**  
        Ange mängden logisk lagring som allokerats till volymen.  

        Den **tillgänglig kvot** fält visar mängden outnyttjat utrymme i poolen valda kapacitet som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida tillgänglig kvot.

    *   **Virtuellt nätverk**  
        Ange den Azure-nätverk (Vnet) som från vilken du vill komma åt volymen. 
        
        Det virtuella nätverket som du anger måste ha Azure NetApp filer som konfigurerats. Tjänsten Azure NetApp Files kan nås från ett virtuellt nätverk som finns på samma plats som volymen.  

    ![Återställda ny volym](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
    
5. Klicka på **OK**.   
    Den nya volymen som ögonblicksbilden återställs visas i bladet volymer.

