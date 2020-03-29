---
title: Konfigurera exportprincip för NFS-volym - Azure NetApp-filer
description: Beskriver hur du konfigurerar exportprincip för att styra åtkomsten till en NFS-volym med Hjälp av Azure NetApp-filer
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551566"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurera exportprincipen för en NFS-volym

Beskriver hur du konfigurerar en exportpolicy och kontrollerar åtkomst till en Azure NetApp Files-volym. Azure NetApp Files exportprincip stöder endast NFS-volymer.  Både NFSv3 och NFSv4 stöds. 

## <a name="steps"></a>Steg 

1.  Klicka på **Exportera princip** från navigeringsfönstret för Azure NetApp Files. 

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
        * Skrivskydd

    ![Exportpolicy](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Nästa steg 
* [Hantera volymer](azure-netapp-files-manage-volumes.md)
* [Montera eller demontera en volym för virtuella datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Hantera ögonblicksbilder](azure-netapp-files-manage-snapshots.md)
