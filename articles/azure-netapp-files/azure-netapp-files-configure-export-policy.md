---
title: Konfigurera exportpolicy för en Azure NetApp Files-volym| Microsoft Docs
description: Beskriver hur du konfigurerar en exportpolicy och kontrollerar åtkomst till en Azure NetApp Files-volym
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
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010964"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>Konfigurera exportprincipen för en volym (valfritt)

Beskriver hur du konfigurerar en exportpolicy och kontrollerar åtkomst till en Azure NetApp Files-volym. 

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

    * **Protokoll**   
        Ange vilket protokoll som ska användas för exportpolicyn.   
        Azure NetApp Files stöder för närvarande endast NFSv3.

    ![Exportpolicy](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Nästa steg 
* [Hantera volymer](azure-netapp-files-manage-volumes.md)
* [Montera eller demontera en volym för virtuella datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Hantera ögonblicksbilder](azure-netapp-files-manage-snapshots.md)
