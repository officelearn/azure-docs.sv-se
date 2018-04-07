---
title: Anslut en RedHat Linux VM till en Azure Active Directory DS | Microsoft Docs
description: Hur du kopplar en befintlig RedHat Enterprise Linux 7-dator till en Azure Active Directory Domain Service.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 68971372a3c9d9671ccad43db8d7b543d286fc7a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Anslut en RedHat Linux VM till en Azure Active Directory Domain Service

Den här artikeln visar hur du ansluter till en virtuell dator för Red Hat Enterprise Linux (RHEL) 7 till en hanterad Azure Active Directory Domain Services (AADDS)-domän.  Kraven är:

- [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)

- [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md)

- [an Azure Active Directory Domain Services DC](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Snabbkommandon

_Ersätt alla exempel med dina egna inställningar._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Växla azure cli till klassisk distribution läge

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Söka efter en RHEL, version och avbildning

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Skapa en Redhat Linux VM

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>SSH till den virtuella datorn

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>YUM uppdateringspaket

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Installera paket som krävs

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

De nödvändiga paketen är installerat på den virtuella Linux-datorn, är nästa uppgift att ansluta den virtuella datorn till den hanterade domänen.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Identifiera den hanterade domänen AAD Domain Services

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Initiera kerberos

Kontrollera att du anger en användare som tillhör gruppen AAD DC-administratörer. Endast dessa användare kan ansluta datorer till den hanterade domänen.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Anslut datorn till domänen

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Kontrollera att datorn är ansluten till domänen

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Nästa steg

* [Red Hat Update infrastruktur (RHUI) för att på begäran Red Hat Enterprise Linux virtuella datorer i Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ställ in Key Vault för virtuella datorer i Azure Resource Manager](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
