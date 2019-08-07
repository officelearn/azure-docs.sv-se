---
title: 'Azure Active Directory Domain Services: Anslut en virtuell Ubuntu-dator till en hanterad domän | Microsoft Docs'
description: Anslut en Ubuntu Linux virtuell dator till Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 78afec75269876c309b2c324d8a5973fd5ebf9a8
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68773042"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Ansluta en virtuell Ubuntu-dator i Azure till en hanterad domän
Den här artikeln visar hur du ansluter en Ubuntu Linux virtuell dator till en Azure AD Domain Services hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
För att utföra de uppgifter som anges i den här artikeln behöver du:  
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** – antingen synkroniserad med en lokal katalog eller en katalog som endast är molnad.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalogen. Om du inte har gjort det följer du alla uppgifter som beskrivs i Komma igångs [guiden](create-instance.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningar för Azure Virtual Network](active-directory-ds-getting-started-dns.md)
5. Slutför de steg som krävs för att [Synkronisera lösen ord till din Azure AD Domain Services hanterade domänen](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Etablera en Ubuntu Linux virtuell dator
Etablera en Ubuntu Linux virtuell dator i Azure med någon av följande metoder:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Distribuera den virtuella datorn till **samma virtuella nätverk där du har aktiverat Azure AD Domain Services**.
> * Välj ett **annat undernät** än det som du har aktiverat i Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Fjärrans luta till den Ubuntu Linux virtuella datorn
Den virtuella Ubuntu-datorn har etablerats i Azure. Nästa uppgift är att fjärrans luta till den virtuella datorn med det lokala administratörs kontot som skapades när den virtuella datorn etablerades.

Följ anvisningarna i artikeln [så här loggar du in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurera värd filen på den virtuella Linux-datorn
I SSH-terminalen redigerar du/etc/hosts-filen och uppdaterar datorns IP-adress och värdnamn.

```console
sudo vi /etc/hosts
```

I hosts-filen anger du följande värde:

```console
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```

Här är "contoso100.com" DNS-domännamnet för din hanterade domän. "contoso-Ubuntu" är värd namnet för den virtuella Ubuntu-dator som du ansluter till den hanterade domänen.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installera nödvändiga paket på den virtuella Linux-datorn
Installera sedan paket som krävs för domän anslutning på den virtuella datorn. Utför följande steg:

1.  I SSH-terminalen skriver du följande kommando för att ladda ned paket listorna från databaserna. Det här kommandot uppdaterar paket listorna för att hämta information om de senaste paket versionerna och deras beroenden.

    ```console
    sudo apt-get update
    ```

2. Skriv följande kommando för att installera de nödvändiga paketen.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Under Kerberos-installationen ser du en rosa skärm. Installationen av paketet "krb5-user" efterfrågar sfär namnet (i alla VERSALer). Installationen skriver [Realm] och [domain_realm]-avsnitten i/etc/krb5.conf.

    > [!TIP]
    > Om namnet på din hanterade domän är contoso100.com anger du CONTOSO100.COM som sfär. Kom ihåg att sfär namnet måste anges i VERSALer.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Konfigurera NTP-inställningarna (Network Time Protocol) på den virtuella Linux-datorn
Datum och tid för den virtuella Ubuntu-datorn måste synkroniseras med den hanterade domänen. Lägg till din hanterade domäns NTP-värdnamn i/etc/NTP.conf-filen.

```console
sudo vi /etc/ntp.conf
```

I filen NTP. conf anger du följande värde och sparar filen:

```console
server contoso100.com
```

Här är "contoso100.com" DNS-domännamnet för din hanterade domän.

Synkronisera nu Ubuntu VM-datum och-tid med NTP-servern och starta sedan NTP-tjänsten:

```console
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Anslut den virtuella Linux-datorn till den hanterade domänen
Nu när de nödvändiga paketen har installerats på den virtuella Linux-datorn är nästa uppgift att ansluta den virtuella datorn till den hanterade domänen.

1. Identifiera den hanterade domänen för AAD Domain Services. Skriv följande kommando i SSH-terminalen:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Felsökning:** Om *sfär identifieringen* inte kan hitta din hanterade domän:
   >   * Se till att domänen kan kontaktas från den virtuella datorn (testa ping).
   >   * Kontrol lera att den virtuella datorn verkligen har distribuerats till samma virtuella nätverk där den hanterade domänen är tillgänglig.
   >   * Kontrol lera om du har uppdaterat DNS-serverinställningarna för det virtuella nätverket så att de pekar på domän kontrol Lanterna i den hanterade domänen.

2. Initiera Kerberos. Skriv följande kommando i SSH-terminalen:

    > [!TIP]
    > * Se till att du anger en användare som tillhör gruppen "AAD DC-administratörer".
    > * Ange domän namnet med versala bokstäver, annars Miss lyckas kinit.
    >

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Anslut datorn till domänen. Skriv följande kommando i SSH-terminalen:

    > [!TIP]
    > Använd samma användar konto som du angav i föregående steg (' kinit ').
    >
    > Om den virtuella datorn inte kan ansluta till domänen ser du till att den virtuella datorns nätverks säkerhets grupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverkets undernät för din Azure AD DS-hanterade domän.

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Du bör få ett meddelande ("den registrerade datorn i sfären") när datorn har anslutits till den hanterade domänen.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Uppdatera SSSD-konfigurationen och starta om tjänsten
1. Skriv följande kommando i SSH-terminalen. Öppna filen SSSD. conf och gör följande ändring
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Kommentera ut raden **use_fully_qualified_names = True** och spara filen.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Starta om SSSD-tjänsten.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurera automatisk skapande av arbets katalog
Om du vill aktivera automatisk generering av hem katalogen efter inloggnings användare skriver du följande kommandon i dinin Terminal:

```console
sudo vi /etc/pam.d/common-session
```

Lägg till följande rad i den här filen under radens sessions valfria pam_sss. so och spara den:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Verifiera domän anslutning
Kontrol lera om datorn har anslutits till den hanterade domänen. Anslut till den domänanslutna Ubuntu-datorn med en annan SSH-anslutning. Använd ett domän användar konto och kontrol lera sedan för att se om användar kontot har lösts korrekt.

1. I SSH-terminalen skriver du följande kommando för att ansluta till den domänanslutna virtuella Ubuntu-datorn med SSH. Använd ett domän konto som tillhör den hanterade domänen (till exempel "bob@CONTOSO100.COM" i det här fallet.)
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. I SSH-terminalen skriver du följande kommando för att se om arbets katalogen har initierats korrekt.
    
    ```console
    pwd
    ```

3. I SSH-terminalen skriver du följande kommando för att se om grupp medlemskapen har lösts korrekt.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja gruppen "AAD DC-administratörer" sudo behörigheter
Du kan bevilja medlemmar i gruppen "AAD DC-administratörer" administratörs behörighet på den virtuella Ubuntu-datorn. Filen sudo finns på/etc/sudoers. Medlemmar i AD-grupper som läggs till i sudoers kan utföra sudo.

1. I SSH-terminalen ser du till att du är inloggad med superanvändare-behörighet. Du kan använda det lokala administratörs kontot som du angav när du skapade den virtuella datorn. Kör följande kommando:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Lägg till följande post i/etc/sudoers-filen och spara den:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Nu kan du logga in som medlem i gruppen "AAD DC-administratörer" och ska ha administratörs behörighet på den virtuella datorn.


## <a name="troubleshooting-domain-join"></a>Felsöka domän anslutning
Se artikeln [fel söknings domän anslutning](join-windows-vm.md#troubleshoot-joining-a-domain) .


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](create-instance.md)
* [Ansluta en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Så här loggar du in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
