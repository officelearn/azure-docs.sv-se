---
title: Konfigurera princip för export för en NFS-volymen med hjälp av Azure NetApp filer | Microsoft Docs
description: Beskriver hur du konfigurerar export för att styra åtkomsten till en NFS-volymen med hjälp av Azure NetApp filer
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086195"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurera exportprincipen för en NFS-volym

Beskriver hur du konfigurerar en exportpolicy och kontrollerar åtkomst till en Azure NetApp Files-volym. Exportera principen stöds endast för NFS-volymer. 

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
        Azure NetApp Files exportera principen stöder för närvarande endast NFSv3.

    ![Exportpolicy](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Nästa steg 
* [Hantera volymer](azure-netapp-files-manage-volumes.md)
* [Montera eller demontera en volym för virtuella datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Hantera ögonblicksbilder](azure-netapp-files-manage-snapshots.md)
