---
title: Konfigurera export princip för NFS-volym – Azure NetApp Files
description: Beskriver hur du konfigurerar export policy för att kontrol lera åtkomsten till en NFS-volym med hjälp av Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 6d990b94210383ba4b30569693f4471f43306ed2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169824"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurera exportprincipen för en NFS-volym

Du kan konfigurera export policy för att kontrol lera åtkomsten till en Azure NetApp Files volym. Azure NetApp Files export policy stöder volymer som använder NFS-protokollet (både NFSv3 och NFSv 4.1) och det dubbla protokollet (NFSv3 och SMB). 

Du kan skapa upp till fem export princip regler.

## <a name="steps"></a>Steg 

1.  På sidan volymer väljer du den volym som du vill konfigurera export principen för och klickar på **Exportera princip**. 

    Du kan också konfigurera export principen när volymen skapas.

2.  Ange information för följande fält för att skapa en exportpolicyregel:   
    *  **Tabbindex**   
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

    * **Rot åtkomst**  
        Ange om `root` kontot har åtkomst till volymen.  Som standard är rot åtkomsten inställd på **på**och `root` kontot har åtkomst till volymen.

        ![Exportpolicy](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Nästa steg 
* [Hantera volymer](azure-netapp-files-manage-volumes.md)
* [Montera eller demontera en volym för virtuella datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Hantera ögonblicksbilder](azure-netapp-files-manage-snapshots.md)
