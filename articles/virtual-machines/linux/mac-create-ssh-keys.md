---
title: Skapa och använd ett SSH-nyckelpar för virtuella Linux-datorer i Azure | Microsoft Docs
description: Hur du skapar och använder ett SSH offentligt / privat nyckelpar för virtuella Linux-datorer i Azure för att förbättra säkerheten för autentiseringsprocessen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: d442d09c8c8ded3aa50faf74e28c8d95ded24a5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614409"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snabba steg: Skapa och använda ett SSH offentligt / privat nyckelpar för virtuella Linux-datorer i Azure

Du kan skapa virtuella datorer (VM) i Azure som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för att logga in med ett secure shell (SSH)-nyckelpar. Den här artikeln visar hur du snabbt skapa och använda ett offentligt / privat nyckelfil SSH-nyckelpar för virtuella Linux-datorer. Du kan utföra dessa steg med Azure Cloud Shell, en macOS eller Linux-värd, Windows-undersystem för Linux och andra verktyg som stöder OpenSSH. 

> [!NOTE]
> Virtuella datorer som skapas med SSH-nycklar är som standard lösenord inaktiveras, vilket ökar svårigheten att gissa brute-force-attacker. 

Mer bakgrundsinformation och exempel finns i [detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

Ytterligare sätt att skapa och använda SSH-nycklar på en Windows-dator, se [hur du använder SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Använd den `ssh-keygen` kommando för att generera SSH offentliga och privata nyckelfiler. Som standard skapas dessa filer i katalogen ~/.ssh. Du kan ange en annan plats och ett valfritt lösenord (*lösenfras*) att komma åt den privata nyckelfilen. Om en SSH-nyckelpar med samma namn finns på den angivna platsen, kommer dessa filer skrivs över.

Följande kommando skapar en SSH-nyckelpar med hjälp av RSA-kryptering och en bit-längd 2048:

```bash
ssh-keygen -t rsa -b 2048
```

Om du använder den [Azure CLI](/cli/azure) att skapa den virtuella datorn med den [az vm skapa](/cli/azure/vm#az-vm-create) kommandot, du kan också generera SSH offentliga och privata nyckelfiler med hjälp av den `--generate-ssh-keys` alternativet. Viktiga filer lagras i katalogen ~/.ssh om inget annat anges med den `--ssh-dest-key-path` alternativet. Den `--generate-ssh-keys` alternativet inte skriver över befintliga viktiga filer, i stället returnerar ett fel. I följande kommando, ersätter *VMname* och *RGname* med dina egna värden:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Ange en offentlig SSH-nyckel när du distribuerar en virtuell dator

Ange din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure-portalen, Azure CLI, Azure Resource Manager-mallar eller andra metoder för att skapa en Linux VM som använder SSH-nycklar för autentisering:

* [Skapa en Linux-dator med Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-VM med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Om du inte är bekant med formatet för en offentlig SSH-nyckel kan du visa din offentliga nyckel med följande `cat` kommando, ersätta `~/.ssh/id_rsa.pub` med sökväg och filnamn på en egen fil för offentlig nyckel om det behövs:

```bash
cat ~/.ssh/id_rsa.pub
```

En typisk offentliga nyckelvärde ser ut som i följande exempel:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen ska användas i Azure-portalen eller en Resource Manager-mall, se till att inte kopiera alla avslutande blanksteg. Om du vill kopiera en offentlig nyckel i macOS kan du skicka den offentliga nyckelfilen till **pbcopy**. På samma sätt i Linux, du kan skicka den offentliga nyckelfilen till program som **xclip**.

Den offentliga nyckeln som du placerar på din Linux-VM i Azure är som standard i ~ /.ssh/id_rsa.pub, såvida inte du har angett en annan plats när du skapade nyckelparet. Du använder den [Azure CLI 2.0](/cli/azure) för att skapa den virtuella datorn med en befintlig offentlig nyckel, ange värdet och eventuellt platsen för den här offentliga nyckeln med hjälp av den [az vm skapa](/cli/azure/vm#az-vm-create) med den `--ssh-key-value` alternativet. I följande kommando, ersätter *VMname*, *RGname*, och *keyFile* med dina egna värden:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Med den offentliga nyckeln som distribuerats på den virtuella Azure-datorn och den privata nyckeln på din lokala dator, SSH till den virtuella datorn med IP-adressen eller DNS-namnet på den virtuella datorn. I följande kommando, ersätter *azureuser* och *myvm.westus.cloudapp.azure.com* med administratörens användarnamn och fullständigt kvalificerade domännamnet (eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade ditt nyckelpar, ange den lösenfrasen när frågan kommer under inloggningen. Den virtuella datorn har lagts till i din ~/.ssh/known_hosts-fil, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på dina Virtuella Azure-ändringar eller servernamnet tas bort från ~/.ssh/known_hosts.

Om den virtuella datorn använder just-in-time-åtkomstprincip, måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [hantera VM-åtkomst med hjälp av just i time-princip](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du arbetar med SSH-nyckelpar, [detaljerade steg för att skapa och hantera SSH-nyckelpar](create-ssh-keys-detailed.md).

* Om du har problem med SSH-anslutningar till virtuella Azure-datorer, se [Felsök SSH-anslutningar till en virtuell Linux-dator](troubleshoot-ssh-connection.md).
