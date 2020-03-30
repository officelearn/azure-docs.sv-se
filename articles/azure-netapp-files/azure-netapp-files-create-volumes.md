---
title: Skapa en NFS-volym för Azure NetApp-filer | Microsoft-dokument
description: Beskriver hur du skapar en NFS-volym för Azure NetApp-filer.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274091"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Skapa en NFS-volym för Azure NetApp Files

Azure NetApp Files stöder NFS (NFSv3- och NFSv4.1) och SMBv3-volymer. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Den här artikeln visar hur du skapar en NFS-volym. Om du vill skapa en SMB-volym läser du [Skapa en SMB-volym för Azure NetApp-filer](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Överväganden 

* Bestämma vilken NFS-version som ska användas  
  NFSv3 kan hantera en mängd olika användningsfall och distribueras ofta i de flesta företagsprogram. Du bör validera vilken version (NFSv3 eller NFSv4.1) ditt program kräver och skapa din volym med rätt version. Om du till exempel använder [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)rekommenderas fillåsning med NFSv4.1 via NFSv3. 

* Säkerhet  
  Stöd för UNIX-läge bitar (läsa, skriva och köra) är tillgänglig för NFSv3 och NFSv4.1. Åtkomst på rotnivå krävs på NFS-klienten för att montera NFS-volymer.

* Lokalt användar-/grupp- och LDAP-stöd för NFSv4.1  
  För närvarande stöder NFSv4.1 endast root-åtkomst till volymer. Se [Konfigurera NFSv4.1-standarddomän för Azure NetApp-filer](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Regelverk

* Du bör se till att du använder rätt monteringsinstruktioner för volymen.  Se [Montera eller avmontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* NFS-klienten ska finnas i samma virtuella nätverk eller peer-virtuella nätverk som Azure NetApp Files-volymen. Anslutning utanför det virtuella nätverket stöds. Det kommer dock att införa ytterligare latens och minska den totala prestanda.

* Du bör se till att NFS-klienten är uppdaterad och kör de senaste uppdateringarna för operativsystemet.

## <a name="create-an-nfs-volume"></a>Skapa en NFS-volym

1.  Klicka på bladet **Volymer** från bladet Kapacitetspooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3.  Klicka på **Skapa** i fönstret Skapa en volym och ange information för följande fält:   
    * **Volymnamn**      
        Ange namnet på den volym du skapar.   

        Ett volymnamn måste vara unikt inom varje kapacitetspool. Det måste innehålla minst tre tecken. Du kan använda alla alfanumeriska tecken.   

        Du kan `default` inte använda som volymnamn.

    * **Kapacitet pool**  
        Ange den kapacitetspool där du vill att volymen ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje Vnät kan endast ett undernät delegeras till Azure NetApp-filer.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicka på **Protokoll**och slutför sedan följande åtgärder:  
    * Välj **NFS** som protokolltyp för volymen.   
    * Ange den **filsökväg** som ska användas för att skapa exportsökvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.

        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt. 

        Filsökvägen måste vara unik inom varje prenumeration och varje region. 

    * Välj NFS-versionen **(NFSv3** eller **NFSv4.1)** för volymen.  
    * Du kan också [konfigurera exportprincip för NFS-volymen](azure-netapp-files-configure-export-policy.md).

    ![Ange NFS-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Klicka på **Granska + Skapa** om du vill granska volyminformationen.  Klicka sedan på **Skapa** för att skapa NFS-volymen.

    Volymen som du skapade visas på sidan Volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.


## <a name="next-steps"></a>Nästa steg  

* [Konfigurera NFSv4.1-standarddomän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurera exportprincipen för en NFS-volym](azure-netapp-files-configure-export-policy.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
