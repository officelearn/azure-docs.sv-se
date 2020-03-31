---
title: Gå med i en virtuell CoreOS-dator till Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du konfigurerar och ansluter till en Virtuell CoreOS-dator till en hanterad Azure AD Domain Services-domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: b97b542d11e405bab00519c68d2365dada6b6c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298879"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ansluta till en virtuell CoreOS-dator till en hanterad domän för Azure AD Domain Services

Om du vill att användare ska kunna logga in på virtuella datorer (VMs) i Azure med en enda uppsättning autentiseringsuppgifter kan du ansluta virtuella datorer till en AD DS -hanterad domän (Azure Active Directory Domain Services). När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användarkonton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Gruppmedlemskap från Azure AD DS-hanterade domänen tillämpas också för att du ska kunna styra åtkomsten till filer eller tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en CoreOS-vm till en Azure AD DS-hanterad domän.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett användarkonto som är en del av Azure AD DS-hanterad domän.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Skapa och ansluta till en CoreOS Linux VM

Om du har en befintlig CoreOS Linux-vm i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en CoreOS Linux VM, eller vill skapa en test-VM för användning med den här artikeln, kan du använda någon av följande metoder:

* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
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
* *coreos* är värdnamnet för din CoreOS-virtuella dator som du ansluter till den hanterade domänen.

Uppdatera dessa namn med dina egna värden:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

När du är klar sparar och `:wq` avslutar du *hosts-filen* med hjälp av redigerarens kommando.

## <a name="configure-the-sssd-service"></a>Konfigurera SSSD-tjänsten

Uppdatera *SSSD-konfigurationen /etc/sssd/sssd.conf.*

```console
sudo vi /etc/sssd/sssd.conf
```

Ange ditt eget Azure AD DS-hanterade domännamn för följande parametrar:

* *domäner* i VERSALER
* *[domän/AADDS]* där AADDS är i VERSALER
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* i VERSALER

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Ansluta till den virtuella datorn till den hanterade domänen

När SSSD-konfigurationsfilen har uppdaterats ansluter du nu den virtuella datorn till den hanterade domänen.

1. Använd först `adcli info` kommandot för att verifiera att du kan se information om den Hanterade Azure AD DS-domänen. I följande exempel hämtar du information om domänen *AADDSCONTOSO.COM*. Ange ditt eget Azure AD DS-hanterade domännamn i VERSALER:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Om `adcli info` kommandot inte kan hitta din Azure AD DS-hanterade domän läser du följande felsökningssteg:

    * Kontrollera att domänen kan nås från den virtuella datorn. Försök `ping aaddscontoso.com` att se om ett positivt svar returneras.
    * Kontrollera att den virtuella datorn distribueras till samma, eller ett peer-invalt, virtuellt nätverk där Azure AD DS-hanterad domän är tillgänglig.
    * Bekräfta att DNS-serverinställningarna för det virtuella nätverket har uppdaterats så att de pekar på domänkontrollanterna för den Hanterade Azure AD DS-domänen.

1. Gå nu med den virtuella datorn till `adcli join` Azure AD DS-hanterad domän med kommandot. Ange en användare som är en del av azure AD DS-hanterad domän. Om det behövs [lägger du till ett användarkonto i en grupp i Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Återigen måste azure AD DS-hanterade domännamnet anges i VERSALER. I följande exempel används `contosoadmin@aaddscontoso.com` kontot som heter för att initiera Kerberos. Ange ditt eget användarkonto som är en del av azure AD DS-hanterade domänen.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Kommandot `adcli join` returnerar inte någon information när den virtuella datorn har anslutit till azure AD DS-hanterade domänen.

1. Om du vill använda domänkopplingskonfigurationen startar du SSSD-tjänsten:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logga in på den virtuella datorn med ett domänkonto

Om du vill kontrollera att den virtuella datorn har anslutits till den Hanterade Azure AD DS-domänen startar du en ny SSH-anslutning med ett domänanvändarkonto. Bekräfta att en arbetskatalog har skapats och att gruppmedlemskap från domänen tillämpas.

1. Skapa en ny SSH-anslutning från konsolen. Använd ett domänkonto som tillhör den `ssh -l` hanterade domänen `contosoadmin@aaddscontoso.com` med kommandot, till exempel och ange sedan adressen till den virtuella datorn, till exempel *coreos.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Kontrollera nu att gruppmedlemskapen löses korrekt:

    ```console
    id
    ```

    Du bör se dina gruppmedlemskap från azure AD DS-hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den Hanterade Azure AD DS-domänen eller logga in med ett domänkonto läser du [Felsöka problem med domänanslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
