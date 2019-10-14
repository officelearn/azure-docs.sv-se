---
title: Skapa en NFS-volym för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar en NFS-volym för Azure NetApp Files.
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
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 1a479b4928631f27d5453d462a59fe7fed09a88c
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302761"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Skapa en NFS-volym för Azure NetApp Files

Azure NetApp Files stöder NFS (NFSv3 och NFSv 4.1) och SMBv3-volymer. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Den här artikeln visar hur du skapar en NFS-volym. Om du vill skapa en SMB-volym, se [skapa en SMB-volym för Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Överväganden 

> [!IMPORTANT] 
> Åtkomst till NFSv 4.1-funktionen kräver vit listning.  Skicka en begäran till <anffeedback@microsoft.com> om du vill begära vit listning. 

* Bestämma vilken NFS-version som ska användas  
  NFSv3 kan hantera en mängd olika användnings fall och distribueras ofta i de flesta företags program. Du bör validera vilken version (NFSv3 eller NFSv 4.1) som programmet behöver och skapa din volym med lämplig version. Om du till exempel använder [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)rekommenderas fil låsning med nfsv 4.1 över NFSv3. 

* Säkerhet  
  Stöd för UNIX läges bitar (läsa, skriva och köra) är tillgängligt för NFSv3 och NFSv 4.1. Åtkomst till rot nivå krävs på NFS-klienten för att montera NFS-volymer.

* Lokal användare/grupp och LDAP-stöd för NFSv 4.1  
  För närvarande stöder NFSv 4.1 endast rot åtkomst till volymer. 

## <a name="best-practice"></a>Regelverk

* Du bör se till att du använder rätt monterings instruktioner för volymen.  Se [montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* NFS-klienten ska finnas i samma VNet eller peer-VNet som Azure NetApp Files volym. Det finns stöd för att ansluta utanför VNet. den kommer dock att införa ytterligare svars tid och minska den övergripande prestandan.

* Du bör se till att NFS-klienten är uppdaterad och köra de senaste uppdateringarna för operativ systemet.

## <a name="create-an-nfs-volume"></a>Skapa en NFS-volym

1.  Klicka på bladet **volymer** på bladet kapacitets pooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3.  I fönstret Skapa en volym klickar du på **skapa** och anger information för följande fält:   
    * **Volym namn**      
        Ange namnet på den volym du skapar.   

        Ett volym namn måste vara unikt inom varje pool för kapacitet. Det måste innehålla minst tre tecken. Du kan använda alla alfanumeriska tecken.   

        Du kan inte använda `default` som volym namn.

    * **Pool för kapacitet**  
        Ange den pool där du vill att volymen ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicka på **protokoll**och utför följande åtgärder:  
    * Välj **NFS** som protokoll typ för volymen.   
    * Ange den **fil Sök väg** som ska användas för att skapa export Sök vägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.

        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt. 

        Fil Sök vägen måste vara unik inom varje prenumeration och varje region. 

    * Välj NFS-version (**NFSv3** eller **nfsv 4.1**) för volymen.  
    * Du kan också [Konfigurera export princip för NFS-volymen](azure-netapp-files-configure-export-policy.md).

    ![Ange NFS-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Granska volym informationen genom att klicka på **Granska + skapa** .  Klicka sedan på **skapa** för att skapa NFS-volymen.

    Volymen som du skapade visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.


## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurera exportprincipen för en NFS-volym](azure-netapp-files-configure-export-policy.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
