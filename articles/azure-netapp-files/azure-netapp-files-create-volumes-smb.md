---
title: Skapa en SMB-volym för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar en SMB-volym för Azure NetApp Files.
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
ms.openlocfilehash: 94fc4906478e44365d03e9c8eeadd7cb1946a43a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300540"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Skapa en SMB-volym för Azure NetApp Files

Azure NetApp Files stöder NFS-och SMBv3-volymer. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Den här artikeln visar hur du skapar en SMBv3-volym. Om du vill skapa en NFS-volym, se [skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Krav för Active Directory anslutningar

 Du måste skapa Active Directory anslutningar innan du skapar en SMB-volym. Kraven för Active Directory anslutningar är följande: 

* Det administratörs konto som du använder måste kunna skapa dator konton i den organisationsenhet (OU) som du ska ange.  

* Rätt portar måste vara öppna på den aktuella Windows Active Directory-servern (AD).  
    De portar som krävs är följande: 

    |     Tjänst           |     Port     |     Protokoll     |
    |-----------------------|--------------|------------------|
    |    AD-webbtjänster    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Saknas       |    Eko svar    |
    |    Paket           |    464       |    TCP           |
    |    Paket           |    464       |    UDP           |
    |    Paket           |    88        |    TCP           |
    |    Paket           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-namn       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    Säkert LDAP        |    636       |    TCP           |
    |    Säkert LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

* Platstopologi för mål Active Directory Domain Services måste följa bästa praxis, särskilt det virtuella Azure-VNet där Azure NetApp Files distribueras.  

    Adress utrymmet för det virtuella nätverk där Azure NetApp Files distribueras måste läggas till på en ny eller befintlig Active Directory plats (där en domänkontrollant kan kontaktas av Azure NetApp Files finns). 

* De angivna DNS-servrarna måste gå att komma åt från det [delegerade under nätet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) för Azure NetApp Files.  

    Se [rikt linjer för Azure NetApp Files nätverks planering](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) för nätverkstopologier som stöds.

    Nätverks säkerhets grupper (NSG: er) och brand väggar måste ha rätt konfigurerade regler för att tillåta Active Directory-och DNS-trafik begär Anden. 

* Det Azure NetApp Files delegerade under nätet måste kunna komma åt alla Active Directory Domain Services (lägger till) domänkontrollanter i domänen, inklusive alla lokala domänkontrollanter och fjärranslutna domänkontrollanter. I annat fall kan tjänst avbrott uppstå.  

    Om du har domänkontrollanter som inte kan kommas åt via Azure NetApp Files delegerade under nätet kan du skicka en support förfrågan för Azure för att ändra omfånget från **Global** (standard) till **plats**.  Azure NetApp Files behöver endast kommunicera med domänkontrollanter på platsen där Azure NetApp Files-adress utrymmet för det delegerade under nätet finns.

    Se [utforma platstopologi](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) om AD-platser och-tjänster. 

## <a name="create-an-active-directory-connection"></a>Skapa en Active Directory anslutning

1. Klicka på **Active Directory anslutningar**på ditt NetApp-konto och klicka sedan på **Anslut**.  

    ![Active Directory anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. I fönstret Anslut Active Directory anger du följande information:

    * **Primär DNS**  
        Detta är den DNS som krävs för åtgärderna för att Active Directory domän anslutning och SMB-autentisering. 
    * **Secondary DNS**   
        Det här är den sekundära DNS-servern för att säkerställa redundanta namn tjänster. 
    * **Domän**  
        Detta är domän namnet för din Active Directory Domain Services som du vill ansluta till.
    * **Prefix för SMB-server (dator konto)**  
        Detta är namngivnings prefixet för dator kontot i Active Directory som Azure NetApp Files används för att skapa nya konton.

        Exempel: om namngivnings standarden som din organisation använder för fil servrar är NAS-01, NAS-02..., NAS-045, anger du "NAS" för prefixet. 

        Tjänsten kommer att skapa ytterligare dator konton i Active Directory efter behov.

    * **Sökväg till organisationsenhet**  
        Det här är LDAP-sökvägen för organisationsenheten (OU) där SMB-serverns dator konton kommer att skapas. Det vill säga OU = den andra nivån, OU = första nivån. 

        Om du använder Azure NetApp Files med Azure Active Directory Domain Services, är sökvägen till organisationsenheten `OU=AADDC Computers` när du konfigurerar Active Directory för ditt NetApp-konto.
        
    * Autentiseringsuppgifter, inklusive ditt **användar namn** och **lösen ord**

    ![Anslut Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klicka på **Anslut**.  

    Active Directory anslutningen som du skapade visas.

    ![Active Directory anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Du kan redigera fälten användar namn och lösen ord när du har sparat Active Directory anslutningen. Inga andra värden kan redige ras efter att anslutningen har sparats. Om du behöver ändra andra värden måste du först ta bort alla distribuerade SMB-volymer och sedan ta bort och återskapa Active Directory anslutningen.

## <a name="add-an-smb-volume"></a>Lägg till en SMB-volym

1. Klicka på bladet **volymer** på bladet kapacitets pooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3. I fönstret Skapa en volym klickar du på **skapa** och anger information för följande fält:   
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
        Ange det virtuella Azure-nätverk (VNet) som du vill ha åtkomst till volymen från.  

        Det virtuella nätverk du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Filess tjänsten kan endast nås från samma VNet eller från ett VNet som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicka på **protokoll** och Slutför följande information:  
    * Välj **SMB** som protokoll typ för volymen. 
    * Välj din **Active Directory** anslutning i list rutan.
    * Ange namnet på den delade volymen i **resurs namn**.

    ![Ange SMB-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Granska volym informationen genom att klicka på **Granska + skapa** .  Klicka sedan på **skapa** för att skapa SMB-volymen.

    Volymen som du skapade visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vanliga frågor och svar om SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installera en ny Active Directory skog med hjälp av Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
