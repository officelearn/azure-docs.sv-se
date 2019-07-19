---
title: 'Azure Active Directory Domain Services: Anslut till en kärna Linux VM | Microsoft Docs'
description: Anslut en virtuell Linux-dator till Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 78a6c5262cd6668712beac1e041fa4f25c05a724
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234070"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Anslut en kärn Linux virtuell dator till en hanterad domän
Den här artikeln visar hur du ansluter en virtuell Linux-dator i Azure till en Azure AD Domain Services hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
För att utföra de uppgifter som anges i den här artikeln behöver du:
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** – antingen synkroniserad med en lokal katalog eller en katalog som endast är molnad.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalogen. Om du inte har gjort det följer du alla uppgifter som beskrivs i Komma igångs [guiden](create-instance.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningar för Azure Virtual Network](active-directory-ds-getting-started-dns.md)
5. Slutför de steg som krävs för att [Synkronisera lösen ord till din Azure AD Domain Services hanterade domänen](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Etablera en virtuell Linux-dator
Etablera en Core-virtuell dator i Azure med någon av följande metoder:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

I den här artikeln används avbildningen **Core Linux (stabil)** virtuell dator i Azure.

> [!IMPORTANT]
> * Distribuera den virtuella datorn till **samma virtuella nätverk där du har aktiverat Azure AD Domain Services**.
> * Välj ett **annat undernät** än det som du har aktiverat i Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Fjärrans luta till den nyligen etablerade virtuella Linux-datorn
Den virtuella datorns kärna har etablerats i Azure. Nästa uppgift är att fjärrans luta till den virtuella datorn med det lokala administratörs kontot som skapades när den virtuella datorn etablerades.

Följ anvisningarna i artikeln [så här loggar du in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurera värd filen på den virtuella Linux-datorn
I SSH-terminalen redigerar du/etc/hosts-filen och uppdaterar datorns IP-adress och värdnamn.

```console
sudo vi /etc/hosts
```

I hosts-filen anger du följande värde:

```console
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```

Här är "contoso100.com" DNS-domännamnet för din hanterade domän. Contoso-Core är värd namnet för den virtuella kärnor-dator som du ansluter till den hanterade domänen.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Konfigurera SSSD-tjänsten på den virtuella Linux-datorn
Uppdatera sedan konfigurations filen för SSSD i ('/etc/SSSD/SSSD.conf ') så att den matchar följande exempel:

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO100.COM

[domain/CONTOSO100.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso100.com
ldap_search_base = dc=contoso100,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso100.com
krb5_realm = CONTOSO100.COM
```

Ersätt "CONTOSO100. COM med DNS-domännamnet för din hanterade domän. Se till att du anger domän namnet i versaler i conf-filen.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Anslut den virtuella Linux-datorn till den hanterade domänen
Nu när de nödvändiga paketen har installerats på den virtuella Linux-datorn är nästa uppgift att ansluta den virtuella datorn till den hanterade domänen.

```console
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Felsökning:** Om *adcli* inte kan hitta din hanterade domän:
>   * Se till att domänen kan kontaktas från den virtuella datorn (testa ping).
>   * Kontrol lera att den virtuella datorn verkligen har distribuerats till samma virtuella nätverk där den hanterade domänen är tillgänglig.
>   * Kontrol lera om du har uppdaterat DNS-serverinställningarna för det virtuella nätverket så att de pekar på domän kontrol Lanterna i den hanterade domänen.

Starta SSSD-tjänsten. Skriv följande kommando i SSH-terminalen:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Verifiera domän anslutning
Kontrol lera om datorn har anslutits till den hanterade domänen. Anslut till den domänbaserade kärnan i den virtuella datorn med en annan SSH-anslutning. Använd ett domän användar konto och kontrol lera sedan för att se om användar kontot har lösts korrekt.

1. I SSH-terminalen skriver du följande kommando för att ansluta till den domänanslutna virtuella datorn med SSH. Använd ett domän konto som tillhör den hanterade domänen (till exempel "bob@CONTOSO100.COM" i det här fallet.)
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
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
Se artikeln [fel söknings domän anslutning](join-windows-vm.md#troubleshoot-joining-a-domain) .

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](create-instance.md)
* [Ansluta en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Så här loggar du in på en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
