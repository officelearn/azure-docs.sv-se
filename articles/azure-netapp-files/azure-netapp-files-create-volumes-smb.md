---
title: Skapa en SMB-volym för Azure NetApp Files | Microsoft Docs
description: Den här artikeln visar hur du skapar en SMBv3-volym i Azure NetApp Files. Läs mer om kraven för Active Directory anslutningar och domän tjänster.
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
ms.topic: how-to
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: e85a78582c0f7aac188198ad91f9ac91ddf62961
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950382"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Skapa en SMB-volym för Azure NetApp Files

Azure NetApp Files stöder skapande av volymer med NFS (NFSv3 och NFSv 4.1), SMBv3 eller Dual Protocol (NFSv3 och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Den här artikeln visar hur du skapar en SMBv3-volym.

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Krav för Active Directory anslutningar

 Du måste skapa Active Directory anslutningar innan du skapar en SMB-volym. Kraven för Active Directory anslutningar är följande: 

* Det administratörs konto som du använder måste ha möjlighet att skapa dator konton i den organisationsenhet (OU) som du ska ange.  

* Rätt portar måste vara öppna på den aktuella Windows Active Directory-servern (AD).  
    De portar som krävs är följande: 

    |     Tjänst           |     Port     |     Protokoll     |
    |-----------------------|--------------|------------------|
    |    AD-webbtjänster    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Ej tillämpligt       |    Eko svar    |
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
    |    W32Time            |    123       |    UDP           |

* Platstopologi för mål Active Directory Domain Services måste följa rikt linjerna, i synnerhet det virtuella Azure-VNet där Azure NetApp Files distribueras.  

    Adress utrymmet för det virtuella nätverk där Azure NetApp Files distribueras måste läggas till på en ny eller befintlig Active Directory plats (där en domänkontrollant som kan kontaktas av Azure NetApp Files är). 

* De angivna DNS-servrarna måste gå att komma åt från det [delegerade under nätet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) för Azure NetApp Files.  

    Se [rikt linjer för Azure NetApp Files nätverks planering](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) för nätverkstopologier som stöds.

    Nätverks säkerhets grupper (NSG: er) och brand väggar måste ha rätt konfigurerade regler för att tillåta Active Directory-och DNS-trafik begär Anden. 

* Det Azure NetApp Files delegerade under nätet måste kunna komma åt alla Active Directory Domain Services (lägger till) domänkontrollanter i domänen, inklusive alla lokala domänkontrollanter och fjärranslutna domänkontrollanter. I annat fall kan tjänst avbrott uppstå.  

    Om du har domänkontrollanter som inte kan kontaktas av Azure NetApp Files delegerade under nätet kan du ange en Active Directory plats när du skapar Active Directory anslutningen.  Azure NetApp Files behöver endast kommunicera med domänkontrollanter på den plats där Azure NetApp Files-adress utrymmet för under nätet är.

    Se [utforma platstopologi](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) om AD-platser och-tjänster. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Se Azure NetApp Files [FAQ (FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) ) om ytterligare AD-information. 

## <a name="decide-which-domain-services-to-use"></a>Bestäm vilka domän tjänster som ska användas 

Azure NetApp Files stöder både [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (lägger till) och Azure Active Directory Domain Services (AADDS) för AD-anslutningar.  Innan du skapar en AD-anslutning måste du bestämma om du vill använda tillägg eller AADDS.  

Mer information finns i [jämföra självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Du kan använda önskade [Active Directory platser och tjänste](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) omfång för Azure NetApp Files. Med det här alternativet kan läsningar och skrivningar Active Directory Domain Services (lägger till) domänkontrollanter som är [tillgängliga via Azure NetApp Files](azure-netapp-files-network-topologies.md). Det förhindrar också att tjänsten kommunicerar med domänkontrollanter som inte finns på den angivna Active Directory platser och tjänster-platsen. 

Om du vill hitta plats namnet när du använder tillägg kan du kontakta den administrativa gruppen i organisationen som ansvarar för Active Directory Domain Services. Exemplet nedan visar plugin-programmet för Active Directory platser och tjänster där plats namnet visas: 

![Active Directory-platser och -tjänster](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

När du konfigurerar en AD-anslutning för Azure NetApp Files anger du plats namnet i området för fältet **AD-plats namn** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services konfiguration och vägledning för (AADDS) finns i [Azure AD Domain Services dokumentation](https://docs.microsoft.com/azure/active-directory-domain-services/).

Ytterligare AADDS-överväganden gäller för Azure NetApp Files: 

* Se till att VNet eller undernät där AADDS har distribuerats finns i samma Azure-region som Azure NetApp Files-distributionen.
* Om du använder ett annat VNet i den region där Azure NetApp Files distribueras, bör du skapa en peering mellan de två virtuella nätverk.
* Azure NetApp Files stöd `user` och `resource forest` typer.
* För synkronisering av typ kan du välja `All` eller `Scoped` .   
    Om du väljer `Scoped` , se till att rätt Azure AD-grupp har valts för åtkomst till SMB-resurser.  Om du är osäker kan du använda den här `All` typen av synkronisering.
* Du måste använda Enterprise-eller Premium-SKU: n. Standard-SKU: n stöds inte.

När du skapar en Active Directory anslutning noterar du följande information för AADDS:

* Du hittar information om **primärt**DNS-, **sekundärt DNS**-och **AD DNS-domännamn** på AADDS-menyn.  
För DNS-servrar används två IP-adresser för att konfigurera Active Directory-anslutningen. 
* **Sökvägen till organisationsenheten** är `OU=AADDC Computers` .  
Den här inställningen konfigureras i **Active Directory anslutningar** under **NetApp-konto**:

  ![Sökväg till organisationsenhet](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Autentiseringsuppgifter för **användar namn** kan vara alla användare som är medlemmar i Azure AD-gruppen **Azure AD DC-administratörer**.


## <a name="create-an-active-directory-connection"></a>Skapa en Active Directory anslutning

1. Klicka på **Active Directory anslutningar**på ditt NetApp-konto och klicka sedan på **Anslut**.  

    ![Active Directory anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. I fönstret Anslut Active Directory anger du följande information baserat på de domän tjänster som du vill använda:  

    Information som är speciell för de domän tjänster som du använder finns i [Bestäm vilka domän tjänster som ska användas](#decide-which-domain-services-to-use). 

    * **Primär DNS**  
        Detta är den DNS som krävs för åtgärderna för att Active Directory domän anslutning och SMB-autentisering. 
    * **Sekundär DNS**   
        Det här är den sekundära DNS-servern för att säkerställa redundanta namn tjänster. 
    * **AD DNS-domännamn**  
        Detta är domän namnet för din Active Directory Domain Services som du vill ansluta till.
    * **AD-platsens namn**  
        Detta är webbplats namnet som identifieringen av domänkontrollanten ska begränsas till.
    * **Prefix för SMB-server (dator konto)**  
        Detta är namngivnings prefixet för dator kontot i Active Directory som Azure NetApp Files används för att skapa nya konton.

        Exempel: om namngivnings standarden som din organisation använder för fil servrar är NAS-01, NAS-02..., NAS-045, anger du "NAS" för prefixet. 

        Tjänsten kommer att skapa ytterligare dator konton i Active Directory efter behov.

        > [!IMPORTANT] 
        > Att byta namn på SMB-serverns prefix när du har skapat Active Directory anslutningen är störande. Du måste montera om befintliga SMB-resurser när du har bytt namn på SMB-serverns prefix.

    * **Sökväg till organisationsenhet**  
        Det här är LDAP-sökvägen för organisationsenheten (OU) där SMB-serverns dator konton kommer att skapas. Det vill säga OU = den andra nivån, OU = första nivån. 

        Om du använder Azure NetApp Files med Azure Active Directory Domain Services är organisationsenhetens sökväg `OU=AADDC Computers` när du konfigurerar Active Directory för ditt NetApp-konto.

     * **Användare av säkerhets kopierings princip**  
        Du kan inkludera ytterligare konton som kräver förhöjd behörighet för det dator konto som skapas för användning med Azure NetApp Files. De angivna kontona kommer att kunna ändra NTFS-behörigheter på fil-eller mappnivå. Du kan till exempel ange ett icke-privilegierat tjänst konto som används för att migrera data till en SMB-filresurs i Azure NetApp Files.  

        Funktionen **säkerhets kopierings princip användare** är för närvarande en för hands version. Om det här är första gången du använder den här funktionen ska du registrera funktionen innan du använder den: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Kontrol lera status för funktions registreringen: 

        > [!NOTE]
        > **RegistrationState** kan vara i ett `Registering` tillstånd i upp till 60 minuter innan den ändras till `Registered` . Vänta tills statusen har **registrerats** innan du fortsätter.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Du kan också använda Azure CLI-kommandon [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) och [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) Registrera funktionen och Visa registrerings status. 

    * Autentiseringsuppgifter, inklusive ditt **användar namn** och **lösen ord**

    ![Anslut Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klicka på **Anslut**.  

    Active Directory anslutningen som du skapade visas.

    ![Active Directory anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Lägg till en SMB-volym

1. Klicka på bladet **volymer** på bladet kapacitets pooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3. I fönstret Skapa en volym klickar du på **skapa** och anger information för följande fält under fliken grundläggande:   
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

    * Om du vill använda en befintlig ögonblicks bild princip på volymen klickar du på **Visa Avancerat avsnitt** för att expandera den och väljer en ögonblicks bilds princip på den nedrullningsbara menyn. 

        Information om hur du skapar en ögonblicks bilds princip finns i [Hantera ögonblicks bild principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Visa avancerad markering](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klicka på **protokoll** och Slutför följande information:  
    * Välj **SMB** som protokoll typ för volymen. 
    * Välj din **Active Directory** anslutning i list rutan.
    * Ange namnet på den delade volymen i  **resurs namn**.

    ![Ange SMB-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Granska volym informationen genom att klicka på **Granska + skapa** .  Klicka sedan på **skapa** för att skapa SMB-volymen.

    Volymen som du skapade visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="control-access-to-an-smb-volume"></a>Kontrol lera åtkomst till en SMB-volym  

Åtkomst till en SMB-volym hanteras via behörigheter.  

### <a name="share-permissions"></a>Resurs behörigheter  

Som standard har en ny volym behörigheten **alla/fullständig behörighet** till resurs. Medlemmar i gruppen domän administratörer kan ändra resurs behörigheter genom att använda dator hantering på det dator konto som används för den Azure NetApp Files volymen.

![](../media/azure-netapp-files/smb-mount-path.png) 
Resurs behörigheter för SMB Mount-sökväg ![](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-fil-och mappbehörigheter  

Du kan ange behörigheter för en fil eller mapp genom att använda fliken **säkerhet** i objektets egenskaper i Windows SMB-klienten.
 
![Ange behörigheter för filer och mappar](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vanliga frågor och svar om SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installera en ny Active Directory skog med hjälp av Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
