---
title: Skapa och använda ett SSH-nyckelpar för virtuella Linux-datorer i Azure
description: Så här skapar och använder du ett SSH-offentligt-privat nyckelpar för virtuella Linux-datorer i Azure för att förbättra säkerheten för autentiseringsprocessen.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969041"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snabbsteg: Skapa och använda ett SSH-offentligt-privat nyckelpar för virtuella Linux-datorer i Azure

Med ett SSH-nyckelpar (Secure Shell) kan du skapa virtuella datorer (VMs) i Azure som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för att logga in. Den här artikeln visar hur du snabbt genererar och använder ett SSH-offentligt-privat nyckelfilpar för virtuella Linux-datorer. Du kan slutföra dessa steg med Azure Cloud Shell, en macOS- eller Linux-värd, Windows-undersystemet för Linux och andra verktyg som stöder OpenSSH. 

> [!NOTE]
> Virtuella datorer som skapas med SSH-nycklar är som standard konfigurerade med lösenord inaktiverade, vilket avsevärt ökar svårigheten att gissa attacker med brute force. 

Mer bakgrund och exempel finns i [Detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

Mer information om hur du skapar och använder SSH-nycklar på en Windows-dator finns i [Så här använder du SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Använd `ssh-keygen` kommandot för att generera SSH offentliga och privata nyckelfiler. Som standard skapas dessa filer i katalogen ~/.ssh. Du kan ange en annan plats och ett valfritt lösenord *(lösenfras)* för att komma åt den privata nyckelfilen. Om ett SSH-nyckelpar med samma namn finns på den angivna platsen skrivs filerna över.

Följande kommando skapar ett SSH-nyckelpar med RSA-kryptering och en bitlängd på 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Om du använder [Azure CLI](/cli/azure) för att skapa din virtuella dator med kommandot az [vm](/cli/azure/vm#az-vm-create) create `--generate-ssh-keys` kan du eventuellt generera SSH-filer för offentliga och privata nyckelfiler med alternativet. Nyckelfilerna lagras i katalogen ~/.ssh om inget `--ssh-dest-key-path` annat anges med alternativet. Alternativet `--generate-ssh-keys` skriver inte över befintliga nyckelfiler utan returnerar ett fel. I följande kommando ersätter du *VMname* och *RGname* med dina egna värden:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Ange en allmän SSH-nyckel när du distribuerar en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering anger du den offentliga SSH-nyckeln när du skapar den virtuella datorn med Azure-portalen, Azure CLI, Azure Resource Manager-mallar eller andra metoder:

* [Skapa en virtuell Linux-dator med Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Om du inte är bekant med formatet på en SSH-offentlig nyckel `cat` kan du `~/.ssh/id_rsa.pub` visa din offentliga nyckel med följande kommando och ersätta med sökvägen och filnamnet för din egen offentliga nyckelfil om det behövs:

```bash
cat ~/.ssh/id_rsa.pub
```

Ett typiskt värde för offentliga nyckelr ut ser ut så här:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen som ska användas i Azure-portalen eller en Resource Manager-mall kontrollerar du att du inte kopierar något efterföljande blanktecken. Om du vill kopiera en offentlig nyckel i macOS kan du skicka den offentliga nyckelfilen till `pbcopy`. På samma sätt i Linux kan du leda `xclip`den offentliga nyckelfilen till program som .

Den offentliga nyckeln som du placerar på din Virtuella Linux-dator i Azure lagras som standard i ~/.ssh/id_rsa.pub, såvida du inte angav en annan plats när du skapade nyckelparet. Om du vill använda [Azure CLI 2.0](/cli/azure) för att skapa din virtuella dator med en befintlig offentlig nyckel `--ssh-key-values` anger du värdet och valfri plats för den här offentliga nyckeln med kommandot az vm [create](/cli/azure/vm#az-vm-create) med alternativet. I följande kommando ersätter du *VMname,* *RGname*och *keyFile* med dina egna värden:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Om du vill använda flera SSH-nycklar med den virtuella datorn kan du `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`ange dem i en utrymmesavgränsad lista, så här .


## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln på ditt lokala system, SSH till din virtuella dator med IP-adressen eller DNS-namnet på din virtuella dator. I följande kommando *ersätter du azureuser* och *myvm.westus.cloudapp.azure.com* med administratörens användarnamn och det fullständigt kvalificerade domännamnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade nyckelparet anger du lösenfrasen när du uppmanas att göra det under inloggningsprocessen. Den virtuella datorn läggs till i filen ~/.ssh/known_hosts och du blir inte ombedd att ansluta igen förrän antingen den offentliga nyckeln på din Virtuella Azure-dator ändras eller servernamnet tas bort från ~/.ssh/known_hosts.

Om den virtuella datorn använder just-in-time-åtkomstprincipen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med hjälp av just-in-time-principen](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med SSH-nyckelpar finns i [Detaljerade steg för att skapa och hantera SSH-nyckelpar](create-ssh-keys-detailed.md).

* Om du har problem med SSH-anslutningar till virtuella Azure-datorer läser [du Felsöka SSH-anslutningar till en Virtuell Azure Linux.](troubleshoot-ssh-connection.md)
