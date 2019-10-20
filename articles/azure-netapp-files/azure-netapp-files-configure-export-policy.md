---
title: Konfigurera export princip för en NFS-volym med Azure NetApp Files | Microsoft Docs
description: Beskriver hur du konfigurerar export policy för att kontrol lera åtkomsten till en NFS-volym med hjälp av Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d73ac199df03f4d789db0634be1e926afc77e623
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597554"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurera exportprincipen för en NFS-volym

Beskriver hur du konfigurerar en exportpolicy och kontrollerar åtkomst till en Azure NetApp Files-volym. Azure NetApp Files export policy stöder endast NFS-volymer.  Både NFSv3 och NFSv4 stöds. 

## <a name="steps"></a>Steg 

1.  Klicka på bladet **Skapa exportpolicy** från bladet Hantera volym. 

2.  Ange information för följande fält för att skapa en exportpolicyregel:   
    *  **Index**   
        Ange indexnummer för regeln.  
        En exportpolicy kan bestå av upp till fem regler. Reglerna utvärderas enligt ordningen i listan med indexnummer. Regler med lägre indexnummer utvärderas först. Till exempel utvärderas regeln med indexnummer 1 före regeln med indexnummer 2. 

    * **Tillåtna klienter**   
        Ange värdet i något av följande format:  
        * IPv4-adress, till exempel, `10.1.12.24` 
        * IPv4-adress med en nätmask uttryckt som antal bitar, till exempel `10.1.12.10/4`

    * **Åtkomst**  
        Markera en av följande åtkomsttyper:  
        * Ingen åtkomst 
        * Läs- och skriv
        * Skrivskyddad

    ![Exportpolicy](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Nästa steg 
* [Hantera volymer](azure-netapp-files-manage-volumes.md)
* [Montera eller demontera en volym för virtuella datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Hantera ögonblicksbilder](azure-netapp-files-manage-snapshots.md)
