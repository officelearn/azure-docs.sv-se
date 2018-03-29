---
title: 'Azure Active Directory Domain Services: Anslut en CentOS virtuell dator till en hanterad domän | Microsoft Docs'
description: Anslut en CentOS Linux-dator till Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: f7095e82605d12221ba7beb0c48fd3eda75a2e7b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Anslut en CentOS Linux-dator till en hanterad domän
Den här artikeln visar hur du kopplar en CentOS Linux-dator i Azure till en Azure AD Domain Services-hanterad domän.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [hur du uppdaterar DNS-inställningarna för virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
5. Slutför steg som krävs för att [synkronisera lösenord till din Azure AD Domain Services-hanterad domän](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Etablera en virtuell dator för CentOS Linux
Etablera en CentOS virtuell dator i Azure, med någon av följande metoder:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Distribuera den virtuella datorn till den **samma virtuella nätverk som du har aktiverat Azure AD Domain Services**.
> * Välj en **annat undernät** än det som du har aktiverat Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Fjärransluta till den nyetablerade virtuella Linux-datorn
CentOS virtuella datorn har etablerats i Azure. Nästa uppgift är att fjärransluta till den virtuella datorn med det lokala administratörskontot som skapades när etablerar den virtuella datorn.

Följ anvisningarna i artikeln [så att logga in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurera värdfilen på Linux-dator
Redigera/etc/hosts-filen i terminalen SSH och uppdatera datorns IP-adress och värdnamn.

```
sudo vi /etc/hosts
```

Ange följande värde i hosts-filen:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
Här är ”contoso100.com” DNS-domännamnet för din hanterade domän. ”contoso-centos' är värdnamnet på den virtuella datorn för CentOS som du ansluter till den hanterade domänen.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installera nödvändiga paket på den virtuella Linux-datorn
Därefter installera paket som krävs för domänanslutning på den virtuella datorn. Skriv följande kommando för att installera de nödvändiga paketen i terminalen SSH:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Anslut den virtuella Linux-datorn till den hanterade domänen
De nödvändiga paketen är installerat på den virtuella Linux-datorn, är nästa uppgift att ansluta den virtuella datorn till den hanterade domänen.

1. Identifiera den hanterade domänen AAD Domain Services. Skriv följande kommando i terminalen SSH:

    ```
    sudo realm discover CONTOSO100.COM
    ```

    > [!NOTE]
    > **Felsöka:** om *sfär identifiera* gick inte att hitta din hanterade domän:  
      * Se till att domänen kan nås från den virtuella datorn (försök ping).  
      * Kontrollera att den virtuella datorn faktiskt har distribuerats till samma virtuella nätverk som den hanterade domänen är tillgänglig. 
      * Kontrollera om du har uppdaterat DNS-serverinställningarna för det virtuella nätverket så att den pekar till domänkontrollanterna i den hanterade domänen.  
      >

2. Initiera Kerberos. Skriv följande kommando i terminalen SSH:

    > [!TIP]
    > * Ange en användare som tillhör gruppen AAD DC-administratörer.
    > * Ange namnet på en domän med versaler, annan kinit misslyckas.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Anslut datorn till domänen. Skriv följande kommando i terminalen SSH:

    > [!TIP]
    > Använd samma användarkonto som du angav i föregående steg (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Du bör få ett meddelande (”har registrerad dator i sfär”) när datorn har anslutit till den hanterade domänen.


## <a name="verify-domain-join"></a>Kontrollera domänanslutning
Kontrollera om datorn har anslutit till den hanterade domänen. Ansluta till domänanslutna CentOS VM med hjälp av en annan SSH-anslutning. Använd ett domänanvändarkonto och kontrollera sedan om användarkontot är borta på rätt sätt.

1. Skriv följande kommando för att ansluta till domänen ansluten din terminal SSH CentOS virtuell dator med SSH. Använda ett domänkonto som tillhör den hanterade domänen (till exempel 'bob@CONTOSO100.COM' i det här fallet.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. Skriv följande kommando för att se om arbetskatalogen har initierats korrekt i terminalen SSH.
    ```
    pwd
    ```

3. Skriv följande kommando för att se om gruppmedlemskap som matchas korrekt i terminalen SSH.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Felsöka domänanslutning
Referera till den [felsökning domänanslutning](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artikel.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Anslut en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Logga in till en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installera Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Guide för Windows-integrering](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
