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
ms.date: 04/02/2018
ms.author: cynthn
ms.openlocfilehash: 2e3d86d776f44c47a33bf075cf7f2140a3940e5e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928462"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snabba steg: skapa och använda ett SSH offentligt / privat nyckelpar för virtuella Linux-datorer i Azure
Med ett SSH-nyckelpar kan du skapa virtuella datorer i Azure som använder SSH-nycklar för autentisering, vilket gör att inga lösenord krävs för att logga in. Den här artikeln visar hur du snabbt skapa och använda ett offentligt / privat nyckelfil SSH-nyckelpar för virtuella Linux-datorer. Du kan utföra dessa steg med Azure Cloud Shell, en macOS eller Linux-värd, Windows-undersystem för Linux och andra verktyg som stöder OpenSSH. 

Mer bakgrundsinformation och exempel finns i [detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

Ytterligare sätt att skapa och använda SSH-nycklar på en Windows-dator, se [hur du använder SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Använd den `ssh-keygen` kommando för att generera SSH offentliga och privata nyckelfiler som skapas i som standard den `~/.ssh` directory. Du kan ange en annan plats och en ytterligare lösenfras (lösenord för att komma åt den privata nyckelfilen) när du uppmanas till detta. Om en SSH-nyckelpar finns i den aktuella platsen, kommer dessa filer skrivs över.

```bash
ssh-keygen -t rsa -b 2048
```

Om du använder den [Azure CLI 2.0](/cli/azure) för att skapa den virtuella datorn, kan du också generera SSH offentliga och privata nyckelfiler genom att köra den [az vm skapa](/cli/azure/vm#az_vm_create) med den `--generate-ssh-keys` alternativet. Nycklarna lagras i katalogen ~/.ssh. Observera att det här kommandoalternativet skriver inte över nycklar om de redan finns på den platsen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ange offentlig SSH-nyckel när du distribuerar en virtuell dator
Om du vill skapa en Linux-VM som använder SSH-nycklar för autentisering, ange din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure portal, CLI, Resource Manager-mallar eller andra metoder:

* [Skapa en Linux-dator med Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-VM med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Om du inte är bekant med formatet för en offentlig SSH-nyckel kan du se din offentliga nyckel genom att köra `cat` enligt följande, där du ersätter `~/.ssh/id_rsa.pub` med din egen plats för offentlig nyckelfil:

```bash
cat ~/.ssh/id_rsa.pub
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen för att använda det i Azure Portal eller i en Resource Manager-mall ska du se till att inte kopiera några extra blanksteg. Om du använder macOS kan du till exempel skicka den offentliga nyckelfilen (som standard `~/.ssh/id_rsa.pub`) till **pbcopy** att kopiera innehållet (det finns andra Linux-program som gör samma sak, till exempel **xclip**).

Den offentliga nyckeln som du placerar på din Linux-VM i Azure är som standard i `~/.ssh/id_rsa.pub`, såvida inte du har ändrat plats när du skapade nycklarna. Om du använder den [Azure CLI 2.0](/cli/azure) för att skapa den virtuella datorn med en befintlig offentlig nyckel, anger du värdet eller platsen för den offentliga nyckeln genom att köra den [az vm skapa](/cli/azure/vm#az_vm_create) med den `--ssh-key-value` alternativet. 

## <a name="ssh-to-your-vm"></a>SSH till den virtuella datorn
Med den offentliga nyckeln som distribuerats på den virtuella Azure-datorn och den privata nyckeln på din lokala dator, SSH till den virtuella datorn med IP-adressen eller DNS-namnet på den virtuella datorn. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörens användarnamn och fullständigt kvalificerade domännamnet (eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade ditt nyckelpar ska du ange lösenfrasen när frågan kommer under inloggningen. (Servern läggs till i din `~/.ssh/known_hosts`-mapp, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på din virtuella Azure-dator ändras eller när servernamnet tas bort från `~/.ssh/known_hosts`.)

Lösenord är som standard inaktiverade för virtuella datorer som skapas med SSH-nycklar för att göra råstyrkeattacker något dyrare och därför svåra. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du skapar en enkel SSH-nyckelpar för snabb användning. 

* Om du behöver mer hjälp att arbeta med SSH-nyckelpar kan du läsa [detaljerade steg för att skapa och hantera SSH-nyckelpar](create-ssh-keys-detailed.md).

* Om du har problem med SSH-anslutningar till en Azure-dator, se [Felsök SSH-anslutningar till en virtuell Linux-dator](troubleshoot-ssh-connection.md).


