---
title: Gå med i en virtuell dator med Ubuntu till Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du konfigurerar och ansluter en virtuell Ubuntu Linux-dator till en hanterad Azure AD Domain Services-domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 95373ab8ff78c5bcb856e6d7e6d67d8525cd3f7e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655120"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ansluta till en virtuell Ubuntu Linux-dator till en hanterad Azure AD Domain Services-domän

Om du vill att användare ska kunna logga in på virtuella datorer (VMs) i Azure med en enda uppsättning autentiseringsuppgifter kan du ansluta virtuella datorer till en AD DS -hanterad domän (Azure Active Directory Domain Services). När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användarkonton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Gruppmedlemskap från Azure AD DS-hanterade domänen tillämpas också för att du ska kunna styra åtkomsten till filer eller tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en Ubuntu Linux VM till en Azure AD DS-hanterad domän.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett användarkonto som är en del av Azure AD DS-hanterad domän.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Skapa och ansluta till en Ubuntu Linux-VM

Om du har en befintlig Ubuntu Linux-virtuell dator i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en Ubuntu Linux-vm, eller vill skapa en test-VM för användning med den här artikeln, kan du använda någon av följande metoder:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

När du skapar den virtuella datorn bör du vara uppmärksam på de virtuella nätverksinställningarna för att se till att den virtuella datorn kan kommunicera med den hanterade Azure AD DS-domänen:

* Distribuera den virtuella datorn till samma, eller ett peer-inkompatiblat, virtuellt nätverk där du har aktiverat Azure AD Domain Services.
* Distribuera den virtuella datorn till ett annat undernät än din Azure AD Domain Services-instans.

När den virtuella datorn har distribuerats följer du stegen för att ansluta till den virtuella datorn med SSH.

## <a name="configure-the-hosts-file"></a>Konfigurera hosts-filen

Om du vill vara säker på att vm-värdnamnet är korrekt konfigurerat för den hanterade domänen redigerar du filen */etc/hosts* och anger värdnamnet:

```console
sudo vi /etc/hosts
```

Uppdatera den *lokala värdadressen* i *hosts-filen.* Se följande exempel:

* *aaddscontoso.com* är DNS-domännamnet för din Azure AD DS-hanterade domän.
* *ubuntu* är värdnamnet för din Ubuntu-virtuella dator som du ansluter till den hanterade domänen.

Uppdatera dessa namn med dina egna värden:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

När du är klar sparar och `:wq` avslutar du *hosts-filen* med hjälp av redigerarens kommando.

## <a name="install-required-packages"></a>Installera de paket som krävs

Den virtuella datorn behöver ytterligare paket för att ansluta till den virtuella datorn till azure AD DS-hanterad domän. Om du vill installera och konfigurera dessa paket uppdaterar och installerar du verktygen för domänkoppling med`apt-get`

Under Kerberos-installationen frågar *paketet krb5-användare* efter sfärnamnet i VERSALER. Om namnet på din Azure AD DS-hanterade domän till exempel är *aaddscontoso.com*anger du *AADDSCONTOSO.COM* som sfär. Installationen skriver och `[realm]` `[domain_realm]` avsnitten i */etc/krb5.conf* konfigurationsfilen. Se till att du anger sfären en ALL VERSALER:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Konfigurera NTP (Network Time Protocol)

För att domänkommunikation ska fungera korrekt måste datum och tid för den virtuella Ubuntu-datorn synkroniseras med den Hanterade Azure AD DS-domänen. Lägg till den Azure AD DS-hanterade domänens NTP-värdnamn i filen */etc/ntp.conf.*

1. Öppna filen *ntp.conf* med en redigerare:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. Skapa en rad för att lägga till dns-namnet på den Hanterade Azure AD DS-domänen i filen *ntp.conf.* I följande exempel läggs en post för *aaddscontoso.com* till. Använd ditt eget DNS-namn:

    ```console
    server aaddscontoso.com
    ```

    När du är klar sparar och avslutar `:wq` du filen *ntp.conf* med hjälp av redigerarens kommando.

1. För att säkerställa att den virtuella datorn synkroniseras med den Hanterade Azure AD DS-domänen krävs följande steg:

    * Stoppa NTP-servern
    * Uppdatera datum och tid från den hanterade domänen
    * Starta NTP-tjänsten

    Kör följande kommandon för att slutföra dessa steg. Använd ditt eget DNS-namn med kommandot: `ntpdate`

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Ansluta till virtuell dator till den hanterade domänen

Nu när de nödvändiga paketen är installerade på den virtuella datorn och NTP är konfigurerad ansluter du den virtuella datorn till azure AD DS-hanterade domänen.

1. Använd `realm discover` kommandot för att identifiera den hanterade Azure AD DS-domänen. I följande exempel upptäcks sfären *AADDSCONTOSO.COM*. Ange ditt eget Azure AD DS-hanterade domännamn i VERSALER:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Om `realm discover` kommandot inte kan hitta din Azure AD DS-hanterade domän läser du följande felsökningssteg:

    * Kontrollera att domänen kan nås från den virtuella datorn. Försök `ping aaddscontoso.com` att se om ett positivt svar returneras.
    * Kontrollera att den virtuella datorn distribueras till samma, eller ett peer-invalt, virtuellt nätverk där Azure AD DS-hanterad domän är tillgänglig.
    * Bekräfta att DNS-serverinställningarna för det virtuella nätverket har uppdaterats så att de pekar på domänkontrollanterna för den Hanterade Azure AD DS-domänen.

1. Initiera nu Kerberos `kinit` med kommandot. Ange en användare som är en del av azure AD DS-hanterad domän. Om det behövs [lägger du till ett användarkonto i en grupp i Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Återigen måste azure AD DS-hanterade domännamnet anges i VERSALER. I följande exempel används `contosoadmin@aaddscontoso.com` kontot som heter för att initiera Kerberos. Ange ditt eget användarkonto som är en del av den hanterade Azure AD DS-domänen:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Slutligen ansluter du datorn till Azure AD DS-hanterad domän med `realm join` kommandot. Använd samma användarkonto som är en del av den hanterade Azure AD DS-domän som du angav i föregående `kinit` kommando, till exempel: `contosoadmin@AADDSCONTOSO.COM`

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Det tar en stund att ansluta till den virtuella datorn till azure AD DS-hanterad domän. Följande exempelutdata visar att den virtuella datorn har anslutit till den Hanterade Azure AD DS-domänen:

```output
Successfully enrolled machine in realm
```

Om den virtuella datorn inte kan slutföra domänanslutningsprocessen kontrollerar du att den virtuella datorns nätverkssäkerhetsgrupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverksundernätet för din Azure AD DS-hanterad domän.

## <a name="update-the-sssd-configuration"></a>Uppdatera SSSD-konfigurationen

Ett av paketen som installerades i ett tidigare steg var för System Security Services Daemon (SSSD). När en användare försöker logga in på en virtuell dator med domänautentiseringsuppgifter vidarebefordrar SSSD begäran till en autentiseringsprovider. I det här fallet använder SSSD Azure AD DS för att autentisera begäran.

1. Öppna *filen sssd.conf* med en redigerare:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Kommentera ut raden för *use_fully_qualified_names* enligt följande:

    ```console
    # use_fully_qualified_names = True
    ```

    När du är klar sparar och avslutar du `:wq` filen *sssd.conf* med hjälp av redigerarens kommando.

1. Om du vill använda ändringen startar du om SSSD-tjänsten:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Konfigurera inställningar för användarkonto och grupp

Med den virtuella datorn ansluten till Azure AD DS-hanterad domän och konfigurerad för autentisering finns det några användarkonfigurationsalternativ att slutföra. Dessa konfigurationsändringar inkluderar att tillåta lösenordsbaserad autentisering och automatiskt skapa hemkataloger på den lokala virtuella datorn när domänanvändare loggar in för första gången.

### <a name="allow-password-authentication-for-ssh"></a>Tillåt lösenordsautentisering för SSH

Som standard kan användare bara logga in på en virtuell dator med SSH offentlig nyckelbaserad autentisering. Lösenordsbaserad autentisering misslyckas. När du ansluter den virtuella datorn till en Azure AD DS-hanterad domän måste dessa domänkonton använda lösenordsbaserad autentisering. Uppdatera SSH-konfigurationen så att lösenordsbaserad autentisering tillåts enligt följande.

1. Öppna *sshd_conf-filen* med en redigerare:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Uppdatera raden för *PasswordAuthentication* till *ja:*

    ```console
    PasswordAuthentication yes
    ```

    När du är klar sparar och avslutar `:wq` *du sshd_conf-filen* med hjälp av redigerarens kommando.

1. Om du vill tillämpa ändringarna och låta användare logga in med ett lösenord startar du om SSH-tjänsten:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Konfigurera automatisk skapande av arbetskatalog

Så här aktiverar du automatiskt skapandet av arbetskatalogen när en användare loggar in för första gången:

1. Öppna filen */etc/pam.d/common-session* i en redigerare:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Lägg till följande rad i `session optional pam_sss.so`den här filen under raden :

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    När du är klar sparar och avslutar `:wq` du den *gemensamma sessionsfilen* med hjälp av redigerarens kommando.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja grupp sudo-behörigheterna För AAD DC-administratörer

Om du vill bevilja medlemmar i *aad DC-administratörsgruppens* administrativa behörigheter på den virtuella datorn i Ubuntu lägger du till en post i */etc/sudoers*. När medlemmar i gruppen *AAD DC-administratörer* har lagts till kan de `sudo` använda kommandot på den virtuella datorn ubuntu.

1. Öppna *sudoers-filen* för redigering:

    ```console
    sudo visudo
    ```

1. Lägg till följande post i slutet av */ etc / sudoers* fil:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    När du är klar sparar `Ctrl-X` och avslutar du redigeraren med kommandot.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logga in på den virtuella datorn med ett domänkonto

Om du vill kontrollera att den virtuella datorn har anslutits till den Hanterade Azure AD DS-domänen startar du en ny SSH-anslutning med ett domänanvändarkonto. Bekräfta att en arbetskatalog har skapats och att gruppmedlemskap från domänen tillämpas.

1. Skapa en ny SSH-anslutning från konsolen. Använd ett domänkonto som tillhör den `ssh -l` hanterade domänen `contosoadmin@aaddscontoso.com` med kommandot, till exempel och ange sedan adressen till den virtuella datorn, till exempel *ubuntu.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. När du har anslutit till den virtuella datorn kontrollerar du att arbetskatalogen har initierats korrekt:

    ```console
    pwd
    ```

    Du bör vara i *katalogen /home* med din egen katalog som matchar användarkontot.

1. Kontrollera nu att gruppmedlemskapen löses korrekt:

    ```console
    id
    ```

    Du bör se dina gruppmedlemskap från azure AD DS-hanterade domänen.

1. Om du har loggat in på den virtuella datorn som medlem i gruppen `sudo` *AAD DC-administratörer* kontrollerar du att du kan använda kommandot på rätt sätt:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den Hanterade Azure AD DS-domänen eller logga in med ett domänkonto läser du [Felsöka problem med domänanslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
