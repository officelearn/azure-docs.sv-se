---
title: 'Azure Active Directory Domain Services: Ansluta en CoreOS Linux-dator till en hanterad domän | Microsoft Docs'
description: Ansluta en CoreOS Linux-dator till Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: fe9ec05b3d8f084f58d5836f1a077e952966ecef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417392"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Ansluta en CoreOS Linux-dator till en hanterad domän
Den här artikeln visar hur du kopplar en CoreOS Linux-dator i Azure till en Azure AD Domain Services-hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningarna för virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
5. Slutför stegen som krävs för att [synkronisera lösenord till din hanterade domän i Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Etablera en virtuell CoreOS Linux-dator
Etablera en CoreOS-dator i Azure, med hjälp av någon av följande metoder:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Den här artikeln används den **CoreOS Linux (Stable)** avbildning av virtuell dator i Azure.

> [!IMPORTANT]
> * Distribuera den virtuella datorn till den **samma virtuella nätverk som du har aktiverat Azure AD Domain Services**.
> * Välj en **annat undernät** än det som du har aktiverat Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Fjärransluta till den nyetablerade virtuella Linux-datorn
CoreOS virtuell dator har etablerats i Azure. Nästa uppgift är att fjärransluta till den virtuella datorn med det lokala administratörskontot som skapas när du etablerar den virtuella datorn.

Följ instruktionerna i artikeln [hur du ansluter till en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurera värdfilen på Linux-dator
I terminalen SSH redigera/etc/hosts-filen och uppdatera IP-adressen för din dator och värdnamn.

```
sudo vi /etc/hosts
```

Ange följande värde i hosts-filen:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
Här är ”contoso100.com” DNS-domännamnet för den hanterade domänen. ”contoso-coreos' är värdnamnet för den virtuella datorn för CoreOS som du ansluter till till den hanterade domänen.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Konfigurera tjänsten SSSD på Linux-dator
Därefter uppdaterar SSSD konfigurationsfilen i ('/ etc/sssd/sssd.conf ”) enligt följande exempel:

 ```
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
Ersätt ”CONTOSO100. COM-med DNS-domännamnet för den hanterade domänen. Kontrollera att du anger domännamnet i dessa fall i conf-filen.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ansluta Linux-dator till den hanterade domänen
Nu när de nödvändiga paketen har installerats på Linux-dator, är nästa uppgift att ansluta till den virtuella datorn till den hanterade domänen.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Felsökning:** Om *adcli* gick inte att hitta din hanterade domän:
>   * Se till att domänen kan nås från den virtuella datorn (försök ping).
>   * Kontrollera att den virtuella datorn faktiskt har distribuerats till samma virtuella nätverk som den hanterade domänen är tillgängligt.
>   * Kontrollera om du har uppdaterat DNS-serverinställningarna för det virtuella nätverket så att den pekar till domänkontrollanterna för den hanterade domänen.

Starta tjänsten SSSD. Skriv följande kommando i terminalen SSH:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Kontrollera domänanslutning
Kontrollera om datorn har anslutits till den hanterade domänen. Ansluta till domänanslutna CoreOS VM med hjälp av en annan SSH-anslutning. Använd ett domänanvändarkonto och kontrollera sedan om användarkontot matchas korrekt.

1. Skriv följande kommando för att ansluta till domänen ansluten din terminal SSH CoreOS-dator med SSH. Använda ett domänkonto som tillhör den hanterade domänen (till exempel ”bob@CONTOSO100.COM” i det här fallet.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. I terminalen SSH, skriver du följande kommando för att se om arbetskatalogen har initierats korrekt.
    ```
    pwd
    ```

3. I terminalen SSH, skriver du följande kommando för att se om gruppmedlemskap som matchas korrekt.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Felsöka domänanslutning
Referera till den [felsökning domänanslutning](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artikeln.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Ansluta en Windows Server-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Hur du ansluter till en virtuell dator som kör Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
