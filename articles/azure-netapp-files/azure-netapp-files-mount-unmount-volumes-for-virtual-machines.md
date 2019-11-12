---
title: Montera eller demontera en Azure NetApp Files volym för virtuella Windows-eller Linux-datorer | Microsoft Docs
description: Beskriver hur du monterar eller demonterar en volym för virtuella datorer eller virtuella Linux-datorer.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 49c5fc4a9d4fed2176baf220e3fcc8f887757a36
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906221"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montera eller demontera en volym för virtuella Windows- eller Linux-datorer 

Du kan montera eller demontera en volym för virtuella Windows-eller Linux-datorer om det behövs.  Monterings anvisningarna för virtuella Linux-datorer finns på Azure NetApp Files.  

1. Klicka på bladet **volymer** och välj sedan den volym som du vill montera. 
2. Klicka på **monterings instruktioner** från den valda volymen och följ sedan anvisningarna för att montera volymen. 

    ![Monterings instruktioner NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Monterings instruktioner SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Om du använder NFSv 4.1 använder du följande kommando för att montera fil systemet:  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

Se [Konfigurera nfsv 4.1 standard domän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md).