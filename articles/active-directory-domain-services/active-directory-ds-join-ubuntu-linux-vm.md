---
title: 'Azure Active Directory Domain Services: Anslut en Ubuntu-VM till en hanterad domän | Microsoft Docs'
description: Ansluta en Ubuntu Linux-dator till Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 709c227c955aba6b31ce415767470fa18ff3684f
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155277"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Ansluta en virtuell Ubuntu-dator i Azure till en hanterad domän
Den här artikeln visar hur du kopplar en Ubuntu Linux-dator till en Azure AD Domain Services-hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:  
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningarna för virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
5. Slutför stegen som krävs för att [synkronisera lösenord till din hanterade domän i Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Etablera en virtuell dator med Ubuntu Linux
Etablera en virtuell dator med Ubuntu Linux i Azure, med hjälp av någon av följande metoder:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Distribuera den virtuella datorn till den **samma virtuella nätverk som du har aktiverat Azure AD Domain Services**.
> * Välj en **annat undernät** än det som du har aktiverat Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Fjärransluta till Ubuntu Linux-dator
Ubuntu-dator har etablerats i Azure. Nästa uppgift är att fjärransluta till den virtuella datorn med det lokala administratörskontot som skapas när du etablerar den virtuella datorn.

Följ instruktionerna i artikeln [hur du ansluter till en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurera värdfilen på Linux-dator
I terminalen SSH redigera/etc/hosts-filen och uppdatera IP-adressen för din dator och värdnamn.

```
sudo vi /etc/hosts
```

Ange följande värde i hosts-filen:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
Här är ”contoso100.com” DNS-domännamnet för den hanterade domänen. ”contoso-ubuntu” är värdnamnet för Ubuntu-dator som du ansluter till till den hanterade domänen.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installera nödvändiga paket på Linux-dator
Installera paket som krävs för domänanslutning på den virtuella datorn. Utför följande steg:

1.  I terminalen SSH, skriver du följande kommando för att ladda ned paketet listor från lagringsplatserna. Det här kommandot Uppdaterar paketet listor för att få information om de senaste versionerna av paket och deras beroenden.

    ```
    sudo apt-get update
    ```

2. Skriv följande kommando för att installera de nödvändiga paketen.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Under Kerberos-installationen kan se du en rosa skärm. Installationen av paketet ”krb5-användare” uppmanar sfärnamnet (i alla versaler). Installationen skriver [område] och [domain_realm]-avsnitt i /etc/krb5.conf.

    > [!TIP]
    > Om namnet på den hanterade domänen är contoso100.com, anger du CONTOSO100.COM som området. Kom ihåg att sfärnamnet måste anges med versaler.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Ange inställningarna för NTP (Network Time Protocol) på Linux-dator
Datum och tid för din Ubuntu VM måste synkronisera med den hanterade domänen. Lägg till din hanterade domän NTP värdnamn i filen /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

Ange följande värde och spara filen i filen ntp.conf:

```
server contoso100.com
```
Här är ”contoso100.com” DNS-domännamnet för den hanterade domänen.

Synkronisera nu Ubuntu VM datum och tid med NTP-server och sedan starta NTP-tjänsten:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ansluta Linux-dator till den hanterade domänen
Nu när de nödvändiga paketen har installerats på Linux-dator, är nästa uppgift att ansluta till den virtuella datorn till den hanterade domänen.

1. Upptäck den hanterade domänen AAD Domain Services. Skriv följande kommando i terminalen SSH:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Felsökning:** om *sfär identifiera* gick inte att hitta din hanterade domän:
     * Se till att domänen kan nås från den virtuella datorn (försök ping).
     * Kontrollera att den virtuella datorn faktiskt har distribuerats till samma virtuella nätverk som den hanterade domänen är tillgängligt.
     * Kontrollera om du har uppdaterat DNS-serverinställningarna för det virtuella nätverket så att den pekar till domänkontrollanterna för den hanterade domänen.
   >

2. Initiera Kerberos. Skriv följande kommando i terminalen SSH:

    > [!TIP]
    > * Kontrollera att du anger en användare som tillhör gruppen ”AAD DC-administratörer”.
    > * Ange domännamnet i versaler, annat kinit misslyckas.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Anslut datorn till domänen. Skriv följande kommando i terminalen SSH:

    > [!TIP]
    > Använd samma användarkonto som du angav i föregående steg (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Du bör få ett meddelande (”registrerades dator i området”) när datorn har anslutit till den hanterade domänen.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Uppdatera konfigurationen av SSSD och starta om tjänsten
1. Skriv följande kommando i terminalen SSH. Öppna filen sssd.conf och göra följande ändring
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Kommentera ut raden **use_fully_qualified_names = True** och spara filen.
    ```
    # use_fully_qualified_names = True
    ```

3. Starta om tjänsten SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurera automatisk arbetskatalog för att skapa
Om du vill aktivera automatisk generering av arbetskatalogen när du har loggat in användare, skriver du följande kommandon i terminalen PuTTY:
```
sudo vi /etc/pam.d/common-session
```

Lägg till följande rad i den här filen nedanför linjen session valfritt pam_sss.so och spara den:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Kontrollera domänanslutning
Kontrollera om datorn har anslutits till den hanterade domänen. Ansluta till domänanslutna Ubuntu VM med hjälp av en annan SSH-anslutning. Använd ett domänanvändarkonto och kontrollera sedan om användarkontot matchas korrekt.

1. Din terminal SSH ansluten Skriv följande kommando för att ansluta till domänen virtuell Ubuntu-dator med SSH. Använda ett domänkonto som tillhör den hanterade domänen (till exempel ”bob@CONTOSO100.COM” i det här fallet.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. I terminalen SSH, skriver du följande kommando för att se om arbetskatalogen har initierats korrekt.
    ```
    pwd
    ```

3. I terminalen SSH, skriver du följande kommando för att se om gruppmedlemskap som matchas korrekt.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Ge gruppen sudo-behörighet för AAD DC-administratörer
Du kan ge medlemmar i gruppen ”AAD DC-administratörer” administrativa privilegier på Ubuntu-VM. Sudo-filen finns /etc/sudoers. Medlemmar i AD-grupper som lagts till i sudoers kan utföra sudo.

1. Kontrollera att du har loggat in med behörigheter för superanvändare i din terminal SSH. Du kan använda det lokala administratörskontot som du angav när du skapar den virtuella datorn. Kör följande kommando:
    ```
    sudo vi /etc/sudoers
    ```

2. Lägg till följande post i /etc/sudoers-filen och spara den:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Du kan nu logga in som medlem i gruppen ”AAD DC-administratörer” och ska ha administratörsbehörighet på den virtuella datorn.


## <a name="troubleshooting-domain-join"></a>Felsöka domänanslutning
Referera till den [felsökning domänanslutning](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artikeln.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Ansluta en Windows Server-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Hur du ansluter till en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
