---
title: Koppla en Core-VM till Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter till en virtuell dator med en kärna till en Azure AD Domain Services hanterad domän.
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
ms.openlocfilehash: 9a0691bd2a556219b3e3d989a3bbc465fa56b4bf
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613816"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Anslut till en virtuell dator med en kärna till en Azure AD Domain Services hanterad domän

För att användarna ska kunna logga in på virtuella datorer i Azure med en enda uppsättning autentiseringsuppgifter, kan du ansluta virtuella datorer till en Azure Active Directory Domain Services (AD DS)-hanterad domän. När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användar konton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Grupp medlemskap från den hanterade domänen i Azure AD DS tillämpas också så att du kan kontrol lera åtkomsten till filer och tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en virtuell dator till en Azure AD DS-hanterad domän.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov [skapar och konfigurerar][create-azure-ad-ds-instance]den första självstudien en Azure Active Directory Domain Services-instans.
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Skapa och Anslut till en Core virtuell Linux-dator

Om du har en befintlig Core virtuella Linux-dator i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en Core-virtuell Linux-dator, eller om du vill skapa en virtuell test dator för användning med den här artikeln, kan du använda någon av följande metoder:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

När du skapar den virtuella datorn ska du tänka på inställningarna för det virtuella nätverket för att se till att den virtuella datorn kan kommunicera med den hanterade Azure AD DS-domänen:

* Distribuera den virtuella datorn till samma eller ett peer-kopplat virtuellt nätverk där du har aktiverat Azure AD Domain Services.
* Distribuera den virtuella datorn i ett annat undernät än Azure AD Domain Services-instansen.

När den virtuella datorn har distribuerats följer du stegen för att ansluta till den virtuella datorn med SSH.

## <a name="configure-the-hosts-file"></a>Konfigurera värd filen

För att se till att namnet på den virtuella datorns värd är korrekt konfigurerat för den hanterade domänen redigerar du */etc/hosts* -filen och anger värd namnet:

```console
sudo vi /etc/hosts
```

I *hosts* -filen uppdaterar du adressen till *localhost* . I följande exempel:

* *aaddscontoso.com* är DNS-domännamnet för din Azure AD DS-hanterade domän.
* *Core* är värd namnet för den virtuella dator i kärnan som du ansluter till den hanterade domänen.

Uppdatera namnen med dina egna värden:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

När du är färdig sparar du och avslutar *hosts* -filen med hjälp av `:wq`-kommandot för redigeraren.

## <a name="configure-the-sssd-service"></a>Konfigurera SSSD-tjänsten

Uppdatera */etc/SSSD/SSSD.conf* SSSD-konfigurationen.

```console
sudo vi /etc/sssd/sssd.conf
```

Ange ditt eget Azure AD DS-hanterade domän namn för följande parametrar:

* *domäner* i alla versaler
* *[domän/AADDS]* där AADDS är i alla versaler
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* i alla versaler

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

## <a name="join-the-vm-to-the-managed-domain"></a>Anslut den virtuella datorn till den hanterade domänen

När konfigurations filen för SSSD har uppdaterats, så anslut nu den virtuella datorn till den hanterade domänen.

1. Använd först kommandot `adcli info` för att kontrol lera att du kan se information om den hanterade domänen i Azure AD DS. I följande exempel hämtas information för domänen *AADDSCONTOSO.com*. Ange ditt eget Azure AD DS-hanterade domän namn med VERSALer:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Om kommandot `adcli info` inte hittar din Azure AD DS-hanterade domän kan du läsa följande fel söknings steg:

    * Kontrol lera att domänen kan kommas åt från den virtuella datorn. Försök `ping aaddscontoso.com` för att se om ett positivt svar returneras.
    * Kontrol lera att den virtuella datorn har distribuerats till samma eller ett peer-kopplat virtuella nätverk där Azure AD DS-hanterad domän är tillgänglig.
    * Bekräfta att DNS-serverinställningarna för det virtuella nätverket har uppdaterats så att de pekar på domän kontrol Lanterna för den hanterade domänen i Azure AD DS.

1. Nu ansluter du den virtuella datorn till den Azure AD DS-hanterade domänen med hjälp av kommandot `adcli join`. Ange en användare som tillhör gruppen *AAD DC-administratörer* . Om det behövs [lägger du till ett användar konto i en grupp i Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Azure AD DS-hanterade domän namnet måste anges i alla VERSALer. I följande exempel används kontot som heter `contosoadmin@aaddscontoso.com` för att initiera Kerberos. Ange ditt eget användar konto som är medlem i gruppen *AAD DC-administratörer* .

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Kommandot `adcli join` returnerar ingen information när den virtuella datorn har anslutits till den hanterade domänen i Azure AD DS.

1. Om du vill tillämpa konfigurationen för domän anslutning startar du SSSD-tjänsten:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logga in på den virtuella datorn med ett domän konto

Verifiera att den virtuella datorn har anslutits till den hanterade Azure AD DS-domänen genom att starta en ny SSH-anslutning med ett domän användar konto. Bekräfta att en arbets katalog har skapats och att grupp medlemskapet från domänen används.

1. Skapa en ny SSH-anslutning från-konsolen. Använd ett domän konto som tillhör den hanterade domänen med kommandot `ssh -l`, till exempel `contosoadmin@aaddscontoso.com` och ange adressen till den virtuella datorn, till exempel *CoreOS.aaddscontoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Kontrol lera nu att grupp medlemskapen har lösts korrekt:

    ```console
    id
    ```

    Du bör se dina grupp medlemskap från den hanterade domänen i Azure AD DS.

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den hanterade domänen i Azure AD DS eller logga in med ett domän konto, se [fel sökning av problem med domän anslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
