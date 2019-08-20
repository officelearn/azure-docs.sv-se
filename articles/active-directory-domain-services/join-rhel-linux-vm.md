---
title: 'Azure Active Directory Domain Services: Anslut en virtuell RHEL-dator till en hanterad domän | Microsoft Docs'
description: Anslut en Red Hat Enterprise Linux virtuell dator till Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 0e3803edd47c3589652b3fedecd12125e3ff40b7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612794"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Ansluta en Red Hat Enterprise Linux 7-virtuell dator till en hanterad domän
Den här artikeln visar hur du ansluter en virtuell Red Hat Enterprise Linux (RHEL) 7-dator till en Azure AD Domain Services hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
För att utföra de uppgifter som anges i den här artikeln behöver du:  
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** – antingen synkroniserad med en lokal katalog eller en katalog som endast är molnad.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalogen. Om du inte har gjort det följer du alla uppgifter som beskrivs i Komma igångs [guiden](tutorial-create-instance.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningar för Azure Virtual Network](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Slutför de steg som krävs för att [Synkronisera lösen ord till din Azure AD Domain Services hanterade domänen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Etablera en Red Hat Enterprise Linux virtuell dator
Etablera en virtuell dator med RHEL 7 i Azure med någon av följande metoder:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Distribuera den virtuella datorn till **samma virtuella nätverk där du har aktiverat Azure AD Domain Services**.
> * Välj ett **annat undernät** än det som du har aktiverat i Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Fjärrans luta till den nyligen etablerade virtuella Linux-datorn
Den virtuella RHEL 7,2-datorn har etablerats i Azure. Nästa uppgift är att fjärrans luta till den virtuella datorn med det lokala administratörs kontot som skapades när den virtuella datorn etablerades.

Följ instruktionerna i artikeln [så här loggar du in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurera värd filen på den virtuella Linux-datorn
I SSH-terminalen redigerar du/etc/hosts-filen och uppdaterar datorns IP-adress och värdnamn.

```console
sudo vi /etc/hosts
```

I hosts-filen anger du följande värde:

```console
127.0.0.1 contoso-rhel.contoso.com contoso-rhel
```

Här är "contoso.com" DNS-domännamnet för din hanterade domän. "contoso-RHEL" är värd namnet för den virtuella RHEL-dator som du ansluter till den hanterade domänen.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installera nödvändiga paket på den virtuella Linux-datorn
Installera sedan paket som krävs för domän anslutning på den virtuella datorn. I SSH-terminalen skriver du följande kommando för att installera de nödvändiga paketen:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Anslut den virtuella Linux-datorn till den hanterade domänen
Nu när de nödvändiga paketen har installerats på den virtuella Linux-datorn är nästa uppgift att ansluta den virtuella datorn till den hanterade domänen.

1. Identifiera den hanterade domänen för AAD Domain Services. Skriv följande kommando i SSH-terminalen:

    ```console
    sudo realm discover contoso.COM
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

    ```console
    kinit bob@contoso.COM
    ```

3. Anslut datorn till domänen. Skriv följande kommando i SSH-terminalen:

    > [!TIP]
    > Använd samma användar konto som du angav i föregående steg (' kinit ').
    >
    > Om den virtuella datorn inte kan ansluta till domänen ser du till att den virtuella datorns nätverks säkerhets grupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverkets undernät för din Azure AD DS-hanterade domän.

    ```console
    sudo realm join --verbose contoso.COM -U 'bob@contoso.COM'
    ```

Du bör få ett meddelande ("den registrerade datorn i sfären") när datorn har anslutits till den hanterade domänen.


## <a name="verify-domain-join"></a>Verifiera domän anslutning
Kontrol lera om datorn har anslutits till den hanterade domänen. Anslut till den domänanslutna RHEL-datorn med en annan SSH-anslutning. Använd ett domän användar konto och kontrol lera sedan för att se om användar kontot har lösts korrekt.

1. I SSH-terminalen skriver du följande kommando för att ansluta till den domänanslutna virtuella RHEL-datorn med SSH. Använd ett domän konto som tillhör den hanterade domänen (till exempel "bob@contoso.COM" i det här fallet.)
    
    ```console
    ssh -l bob@contoso.COM contoso-rhel.contoso.com
    ```

2. I SSH-terminalen skriver du följande kommando för att se om arbets katalogen har initierats korrekt.
    
    ```console
    pwd
    ```

3. I SSH-terminalen skriver du följande kommando för att se om grupp medlemskapen har lösts korrekt.
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Felsöka domän anslutning
Se artikeln [fel söknings domän anslutning](join-windows-vm.md#troubleshoot-domain-join-issues) .

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](tutorial-create-instance.md)
* [Ansluta en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Logga in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installera Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – guide för Windows-integrering](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
