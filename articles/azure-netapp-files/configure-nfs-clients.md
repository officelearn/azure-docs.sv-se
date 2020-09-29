---
title: Konfigurera en NFS-klient för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du konfigurerar NFS-klienter som ska användas med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449646"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurera en NFS-klient för Azure NetApp Files

Konfigurationen av NFS-klienten som beskrivs i den här artikeln är en del av installationen när du [konfigurerar nfsv 4.1 Kerberos-kryptering](configure-kerberos-encryption.md) eller [skapar en volym med dubbla protokoll](create-volumes-dual-protocol.md). Det finns många olika Linux-distributioner som du kan använda med Azure NetApp Files. I den här artikeln beskrivs konfigurationer för två av de ofta använda miljöerna: RHEL 8 och Ubuntu 18,04. 

Oavsett vilken Linux-variant du använder, krävs följande konfigurationer:
* Konfigurera en NTP-klient för att undvika problem med tids skevning.
* Konfigurera DNS-poster för Linux-klienten för namn matchning.  
    Den här konfigurationen omfattar posten "A" (forward) och PTR (omvänd). 
* Skapa ett dator konto i mål Active Directory för domän anslutning (som skapas vid kommandot sfär anslutning). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Variabeln som används i kommandona nedan måste vara ett användar konto med behörighet eller delegering för att skapa ett dator konto i den aktuella organisationsenheten.
* Aktivera klienten för montering av NFS-volymer och andra relevanta övervaknings verktyg.

## <a name="rhel-8-configuration"></a>RHEL 8-konfiguration 

1. Installera paket:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Konfigurera NTP-klienten:  
    RHEL 8 använder `chrony` som standard.  Följ rikt linjerna för konfiguration i [chrony-programsviten för att konfigurera NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Anslut Active Directory-domänen:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu-konfiguration 
I det här avsnittet beskrivs Ubuntu-konfigurationen för NFS-klienterna.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Om du använder NFSv 4.1 Kerberos-kryptering 

1. Installera paket:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Konfigurera NTP-klienten.  
    Ubuntu 18,04 används `chrony` som standard.  Följa konfigurations rikt linjerna i [Ubuntu Bionic: använda chrony för att konfigurera NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Gå med i Active Directory-domän:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Om du använder två protokoll  

1. Kör följande kommando för att uppgradera de installerade paketen:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Exempel:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Kör följande kommando för att starta om och aktivera tjänsten:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

I följande exempel frågar AD LDAP-servern från LDAP-Ubuntu LDAP-klient för en LDAP-användare `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Nästa steg  

* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa en dual-Protocol-volym för Azure NetApp Files](create-volumes-dual-protocol.md)

