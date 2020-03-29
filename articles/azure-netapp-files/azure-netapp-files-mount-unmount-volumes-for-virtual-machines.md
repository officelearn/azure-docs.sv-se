---
title: Mount Azure NetApp-filvolymer för virtuella datorer
description: Lär dig hur du monterar eller avmonterar en volym för virtuella Windows-datorer eller virtuella Linux-datorer i Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551547"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montera eller demontera en volym för virtuella Windows- eller Linux-datorer 

Du kan montera eller avmontera en volym för virtuella Windows- eller Linux-datorer efter behov.  Monteringsinstruktionerna för virtuella Linux-datorer är tillgängliga på Azure NetApp-filer.  

1. Klicka på bladet **Volymer** och välj sedan den volym som du vill montera. 
2. Klicka på **Montera instruktioner** från den valda volymen och följ sedan instruktionerna för att montera volymen. 

    ![Montera instruktioner NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Montera instruktioner SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Om du använder NFSv4.1 använder du följande kommando för att montera filsystemet:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Om du vill att en NFS-volym ska monteras automatiskt när en Virtuell `/etc/fstab` Azure-dator startas eller startas om lägger du till en post i filen på värden. 

    Exempelvis:  `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`är IP-adressen för Azure NetApp Files-volymen som finns i volymegenskaperna.
    * `$FILEPATH`är exportsökvägen för Azure NetApp Files-volymen.
    * `$MOUNTPOINT`är den katalog som skapats på Linux-värden som används för att montera NFS-exporten.

4. Om du vill montera volymen i Windows med NFS:

    a. Montera volymen på en Unix eller Linux VM först.  
    b. Kör `chmod 777` ett `chmod 775` eller ett kommando mot volymen.  
    c. Montera volymen via NFS-klienten i Windows.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera NFSv4.1-standarddomän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Vanliga frågor och frågor om NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
