---
title: Skapa en volym för Azure NetApp Files| Microsoft Docs
description: Beskriver hur du skapar en volym för Azure NetApp-filer.
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
ms.date: 3/17/2019
ms.author: b-juche
ms.openlocfilehash: c2dd8b636223e0484e36eed9fcc616dc6f19cf3a
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877983"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Skapa en volym för Azure NetApp Files

Varje kapacitet pool kan ha högst 500 volymer. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Azure NetApp-filer har stöd för NFS och SMBv3 volymer. 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Skapa en NFS-volym

1.  Klicka på den **volymer** bladet från bladet kapacitet pooler. 

    ![Gå till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Klicka på **+ Lägg till volym** för att skapa en volym.  
    Skapa en volym visas.

3.  I avsnittet Skapa en volym-fönstret klickar du på **skapa** och ange information för följande fält:   
    * **Volymnamn**      
        Ange namnet på den volym du skapar.   

        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Kapacitetspool**  
        Ange kapacitet poolen där du vill att volymen som ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från ditt lokala nätverk via Expressroute.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje virtuellt nätverk kan bara ett undernät delegeras till Azure NetApp-filer.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicka på **protokollet**och välj sedan **NFS** som protokolltypen för volymen.   
    * Ange den **filsökväg** som ska användas för att skapa exportsökvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.

        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt.  

    * Du kan också [konfigurera export-princip för NFS-volymen](azure-netapp-files-configure-export-policy.md)

    ![Ange NFS-protokollet](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Klicka på **granska + skapa** Läs mer om volymen.  Klicka sedan på **skapa** att skapa NFS-volymen.

    Den volym som du har skapat visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="create-an-smb-volume"></a>Skapa en SMB-volym

Azure NetApp-filer stöder SMBv3-volymer. Du behöver skapa Active Directory-anslutningarna innan du lägger till en SMB-volym. 

### <a name="create-an-active-directory-connection"></a>Skapa en Active Directory-anslutning

1. NetApp-konto klickar du på **Active Directory-anslutningarna**, klicka sedan på **ansluta**.  

    ![Active Directory-anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Ange följande information i fönstret ansluta till Active Directory:

    * **Primär DNS**   
        Det här är domain controller IP-adressen för den önskade Active Directory Domain Services för användning med Azure NetApp-filer. 
    * **Sekundär DNS**  
        Det här är domain controller IP-adressen för den sekundära Active Directory Domain Services för användning med Azure NetApp-filer. 
    * **Domain**  
        Det här är domännamnet för din Active Directory Domain Services som du vill ansluta till.
    * **Prefix för SMB-server (datorkontot)**  
        Det här är namnprefix för datorkontot i Active Directory som Azure NetApp filer ska använda för att skapa nya konton.

        Till exempel om namngivningsstandard som din organisation använder för filservrar är NAS-01, NAS-02..., anger NAS-045 och sedan ”NAS” för prefixet. 

        Tjänsten kommer att skapa ytterligare konton i Active Directory efter behov.

    * **Organisationsenhet sökväg**  
        Det här är LDAP-sökväg för organisationsenheten (OU) där datorkonton för SMB-servern kommer att skapas. Det vill säga OU = andra nivån, OU = första nivån. 
    * Autentiseringsuppgifterna, till exempel din **användarnamn** och **lösenord**

    ![Ansluta till Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klicka på **Anslut**.  

    Active Directory-anslutningen som du har skapat visas.

    ![Active Directory-anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Lägg till en SMB-volym

1. Klicka på den **volymer** bladet från bladet kapacitet pooler. 

    ![Gå till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicka på **+ Lägg till volym** för att skapa en volym.  
    Skapa en volym visas.

3. I avsnittet Skapa en volym-fönstret klickar du på **skapa** och ange information för följande fält:   
    * **Volymnamn**      
        Ange namnet på den volym du skapar.   

        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Filsökväg**  
        Ange filsökvägen som ska användas för att skapa exportvägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.   
     
        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt.  

    * **Kapacitetspool**  
        Ange kapacitet poolen där du vill att volymen som ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange från vilket virtuellt Azure-nätverk du vill komma åt volymen.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från ditt lokala nätverk via Expressroute.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje virtuellt nätverk kan bara ett undernät delegeras till Azure NetApp-filer.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicka på **protokollet** och ange följande information:  
    * Välj **SMB** som protokolltypen för volymen. 
    * Välj din **Active Directory** anslutningen från den nedrullningsbara listan.
    * Ange namnet på den delade volymen i **resursnamn**.

    ![Ange SMB-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klicka på **granska + skapa** Läs mer om volymen.  Klicka sedan på **skapa** att skapa SMB-volym.

    Den volym som du har skapat visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurera exportprincipen för en NFS-volym](azure-netapp-files-configure-export-policy.md)
* [Lär dig mer om virtual network-integration för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
