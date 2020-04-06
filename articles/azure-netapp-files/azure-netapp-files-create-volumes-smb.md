---
title: Skapa en SMB-volym för Azure NetApp-filer | Microsoft-dokument
description: Beskriver hur du skapar en SMB-volym för Azure NetApp-filer.
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: c4e7566eeb28bc5709acd60ced9fcdffb7e8a725
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668004"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Skapa en SMB-volym för Azure NetApp Files

Azure NetApp Files stöder NFS- och SMBv3-volymer. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Den här artikeln visar hur du skapar en SMBv3-volym. Om du vill skapa en NFS-volym läser du [Skapa en NFS-volym för Azure NetApp-filer](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Krav för Active Directory-anslutningar

 Du måste skapa Active Directory-anslutningar innan du skapar en SMB-volym. Kraven för Active Directory-anslutningar är följande: 

* Administratörskontot som du använder måste ha möjlighet att skapa datorkonton i den organisationsenhetssökväg som du anger.  

* Rätt portar måste vara öppna på den tillämpliga WINDOWS Active Directory-servern (AD).  
    De portar som krävs är följande: 

    |     Tjänst           |     Port     |     Protokoll     |
    |-----------------------|--------------|------------------|
    |    AD-webbtjänster    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Ej tillämpligt       |    Echo Svar    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-namn       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32time            |    123       |    UDP           |

* Platstopologin för de riktade Active Directory Domain Services måste följa metodtips, särskilt Azure VNet där Azure NetApp-filer distribueras.  

    Adressutrymmet för det virtuella nätverket där Azure NetApp-filer distribueras måste läggas till på en ny eller befintlig Active Directory-plats (där en domänkontrollant kan nås av Azure NetApp Files). 

* De angivna DNS-servrarna måste kunna nås från det [delegerade undernätet i](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) Azure NetApp Files.  

    Se [Riktlinjer för Azure NetApp Files nätverksplanering](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) för nätverkstopologier som stöds.

    NSG-grupperna (Network Security Groups) och brandväggar måste ha lämpliga regler för att möjliggöra Active Directory- och DNS-trafikbegäranden. 

* Det delegerade undernätet för Azure NetApp-filer måste kunna nå alla ACTIVE Directory Domain Services -domänkontrollanter (ADD) i domänen, inklusive alla lokala domänkontrollanter och fjärrdomänkontrollanter. Annars kan avbrott i tjänsten uppstå.  

    Om du har domänkontrollanter som inte kan nås av det delegerade undernätet för Azure NetApp-filer kan du ange en Active Directory-plats när Active Directory-anslutningen skapas.  Azure NetApp-filer behöver endast kommunicera med domänkontrollanter på den plats där Azure NetApp-filer delegerade undernätsadressutrymmet är.

    Se [Utforma webbplatsens topologi](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) om AD-webbplatser och tjänster. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Se Vanliga frågor om Azure NetApp Files [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) om ytterligare AD-information. 

## <a name="decide-which-domain-services-to-use"></a>Bestäm vilka domäntjänster som ska användas 

Azure NetApp-filer stöder både [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADD) och Azure Active Directory Domain Services (AADDS) för AD-anslutningar.  Innan du skapar en AD-anslutning måste du bestämma om DU vill använda ADD eller AADDS.  

Mer information finns i [Jämför självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Du kan använda ditt önskade [Active Directory-program och -tjänster](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) för Azure NetApp-filer. Det här alternativet aktiverar läsningar och skrivningar till ACTIVE Directory Domain Services (ADDS) domänkontrollanter som är [tillgängliga för Azure NetApp Files](azure-netapp-files-network-topologies.md). Det förhindrar också att tjänsten kommunicerar med domänkontrollanter som inte finns på den angivna webbplatsen För platser och tjänster i Active Directory. 

Om du vill hitta ditt webbplatsnamn när du använder ADD kan du kontakta den administrativa grupp i organisationen som ansvarar för Active Directory Domain Services. Exemplet nedan visar plugin-programmet För Active Directory - platser och tjänster där webbplatsnamnet visas: 

![Active Directory-platser och -tjänster](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

När du konfigurerar en AD-anslutning för Azure NetApp-filer anger du platsnamnet i omfånget för fältet **AD-platsnamn.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Konfiguration och vägledning för Azure Active Directory Domain Services (AADDS) finns i [dokumentationen för Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

Ytterligare AADDS-överväganden gäller för Azure NetApp-filer: 

* Kontrollera att det virtuella nätverket eller undernätet där AADDS distribueras finns i samma Azure-region som Azure NetApp Files-distributionen.
* Om du använder ett annat virtuella nätverk i regionen där Azure NetApp-filer distribueras bör du skapa en peering mellan de två virtuella nätverken.
* Azure NetApp-filer stöder `user` och `resource forest` typer.
* För synkroniseringstyp kan `All` du `Scoped`välja eller .   
    Om du `Scoped`väljer kontrollerar du att rätt Azure AD-grupp har valts för åtkomst till SMB-resurser.  Om du är osäker kan `All` du använda synkroniseringstypen.
* Användning av Enterprise eller Premium SKU krävs. Standard-SKU stöds inte.

När du skapar en Active Directory-anslutning bör du tänka på följande detaljer för AADDS:

* Du hittar information för **primär DNS,** **sekundär DNS**och **AD DNS-domännamn** på AADDS-menyn.  
För DNS-servrar används två IP-adresser för att konfigurera Active Directory-anslutningen. 
* Sökvägen **till organisationsenheten** är `OU=AADDC Computers`.  
Den här inställningen är konfigurerad i **Active Directory-anslutningar** under **NetApp-konto:**

  ![Sökväg till organisationsenhet](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Användarnamnsuppgifter** kan vara alla användare som är medlemmar i Azure AD-gruppen **Azure AD DC-administratörer**.


## <a name="create-an-active-directory-connection"></a>Skapa en Active Directory-anslutning

1. Klicka på **Active Directory-anslutningar**på ditt NetApp-konto och klicka sedan på **Anslut**.  

    ![Active Directory-anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. I fönstret Anslut till Active Directory anger du följande information, baserat på de domäntjänster som du vill använda:  

    Information som är specifik för de domäntjänster du använder finns [i Bestäm vilka domäntjänster som ska användas](#decide-which-domain-services-to-use). 

    * **Primär DNS**  
        Detta är den DNS som krävs för Active Directory-domänanslutningen och SMB-autentiseringsåtgärderna. 
    * **Sekundär DNS**   
        Detta är den sekundära DNS-servern för att säkerställa redundanta namntjänster. 
    * **AD DNS-domännamn**  
        Det här är domännamnet för de Active Directory Domain Services som du vill gå med i.
    * **AD-platsens namn**  
        Det här är det platsnamn som identifieringen av domänkontrollanten begränsas till.
    * **Prefix för SMB-server (datorkonto)**  
        Det här är namngivningsprefixet för datorkontot i Active Directory som Azure NetApp Files ska använda för att skapa nya konton.

        Om till exempel namngivningsstandarden som din organisation använder för filservrar är NAS-01, NAS-02..., NAS-045, anger du "NAS" för prefixet. 

        Tjänsten skapar ytterligare datorkonton i Active Directory efter behov.

    * **Sökväg till organisationsenhet**  
        Det här är LDAP-sökvägen för organisationsenheten (OU) där SMB-serverdatorkonton ska skapas. Det vill än OU=andra nivån, OU=första nivån. 

        Om du använder Azure NetApp-filer med Azure Active Directory `OU=AADDC Computers` Domain Services är sökvägen till organisationsenheten när du konfigurerar Active Directory för ditt NetApp-konto.
        
    * Referenser, inklusive ditt **användarnamn** och **lösenord**

    ![Gå med i Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klicka på **Anslut**.  

    Active Directory-anslutningen som du skapade visas.

    ![Active Directory-anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Du kan redigera fälten användarnamn och lösenord när du har sparat Active Directory-anslutningen. Inga andra värden kan redigeras när anslutningen har sparats. Om du behöver ändra andra värden måste du först ta bort alla distribuerade SMB-volymer och sedan ta bort och återskapa Active Directory-anslutningen.

## <a name="add-an-smb-volume"></a>Lägga till en SMB-volym

1. Klicka på bladet **Volymer** från bladet Kapacitetspooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3. Klicka på **Skapa** i fönstret Skapa en volym och ange information för följande fält:   
    * **Volymnamn**      
        Ange namnet på den volym du skapar.   

        Ett volymnamn måste vara unikt inom varje kapacitetspool. Det måste innehålla minst tre tecken. Du kan använda alla alfanumeriska tecken.   

        Du kan inte `default` använda som volymnamn.

    * **Kapacitet pool**  
        Ange den kapacitetspool där du vill att volymen ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Virtuellt nätverk**  
        Ange det virtuella Azure-nätverk (VNet) som du vill komma åt volymen från.  

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp-filer. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuella nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp-filer.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicka på **Protokoll** och fyll i följande information:  
    * Välj **SMB** som protokolltyp för volymen. 
    * Välj **Active Directory-anslutning** i listrutan.
    * Ange namnet på den delade volymen i **Resursnamn**.

    ![Ange SMB-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klicka på **Granska + Skapa** om du vill granska volyminformationen.  Klicka sedan på **Skapa** för att skapa SMB-volymen.

    Volymen som du skapade visas på sidan Volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vanliga frågor om SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installera en ny Active Directory-skog med Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
