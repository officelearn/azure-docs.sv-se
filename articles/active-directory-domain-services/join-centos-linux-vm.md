---
title: Gå med i en CentOS-virtuell dator till Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du konfigurerar och ansluter till en virtuell CentOS Linux-dator till en hanterad Azure AD-domäntjänst.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d08552dcae51c897f2419d94e5e61e857247f09a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655226"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ansluta till en virtuell CentOS Linux-dator till en hanterad Azure AD Domain Services-domän

Om du vill att användare ska kunna logga in på virtuella datorer (VMs) i Azure med en enda uppsättning autentiseringsuppgifter kan du ansluta virtuella datorer till en AD DS -hanterad domän (Azure Active Directory Domain Services). När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användarkonton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Gruppmedlemskap från Azure AD DS-hanterade domänen tillämpas också för att du ska kunna styra åtkomsten till filer eller tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en CentOS Linux VM till en Azure AD DS-hanterad domän.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett användarkonto som är en del av Azure AD DS-hanterad domän.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Skapa och ansluta till en CentOS Linux VM

Om du har en befintlig CentOS Linux-vm i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en Virtuell CentOS Linux-dator eller vill skapa en test-VM för användning med den här artikeln kan du använda någon av följande metoder:

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
* *centos* är värdnamnet för din Virtuella CentOS-dator som du ansluter till den hanterade domänen.

Uppdatera dessa namn med dina egna värden:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

När du är klar sparar och `:wq` avslutar du *hosts-filen* med hjälp av redigerarens kommando.

## <a name="install-required-packages"></a>Installera de paket som krävs

Den virtuella datorn behöver ytterligare paket för att ansluta till den virtuella datorn till azure AD DS-hanterad domän. Om du vill installera och konfigurera dessa paket uppdaterar `yum`och installerar du domänkopplingsverktygen med:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Ansluta till virtuell dator till den hanterade domänen

Nu när de nödvändiga paketen är installerade på den virtuella datorn ansluter du den virtuella datorn till den Azure AD DS-hanterade domänen.

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
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Det tar en stund att ansluta till den virtuella datorn till azure AD DS-hanterad domän. Följande exempelutdata visar att den virtuella datorn har anslutit till den Hanterade Azure AD DS-domänen:

```output
Successfully enrolled machine in realm
```

Om den virtuella datorn inte kan slutföra domänanslutningsprocessen kontrollerar du att den virtuella datorns nätverkssäkerhetsgrupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverksundernätet för din Azure AD DS-hanterad domän.

## <a name="allow-password-authentication-for-ssh"></a>Tillåt lösenordsautentisering för SSH

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja grupp sudo-behörigheterna För AAD DC-administratörer

Om du vill bevilja medlemmar i *aad DC-administratörsgruppens* administrativa behörigheter på den virtuella centos-datorn lägger du till en post i */etc/sudoers*. När medlemmar i gruppen *AAD DC-administratörer* har lagts till kan de `sudo` använda kommandot på den virtuella centos-datorn.

1. Öppna *sudoers-filen* för redigering:

    ```console
    sudo visudo
    ```

1. Lägg till följande post i slutet av */etc/sudoers-filen.* Gruppen *AAD DC-administratörer* innehåller blanktecken i namnet, så inkludera omvänt snedstreck i gruppnamnet. Lägg till ditt eget domännamn, till exempel *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    När du är klar sparar `:wq` och avslutar du redigeraren med hjälp av redaktörens kommando.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logga in på den virtuella datorn med ett domänkonto

Om du vill kontrollera att den virtuella datorn har anslutits till den Hanterade Azure AD DS-domänen startar du en ny SSH-anslutning med ett domänanvändarkonto. Bekräfta att en arbetskatalog har skapats och att gruppmedlemskap från domänen tillämpas.

1. Skapa en ny SSH-anslutning från konsolen. Använd ett domänkonto som tillhör den `ssh -l` hanterade domänen `contosoadmin@aaddscontoso.com` med kommandot, till exempel och ange sedan adressen till den virtuella datorn, till exempel *centos.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den Hanterade Azure AD DS-domänen eller logga in med ett domänkonto läser du [Felsöka problem med domänanslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
