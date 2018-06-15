---
title: Skapa och använd ett SSH-nyckelpar för virtuella Linux-datorer i Azure | Microsoft Docs
description: Hur du skapar och använder ett SSH privat-offentligt nyckelpar för Linux virtuella datorer i Azure för att förbättra säkerheten för autentiseringen.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
ms.locfileid: "31602638"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snabbsteg: skapar och använder ett SSH privat-offentligt nyckelpar för Linux virtuella datorer i Azure
Med ett SSH-nyckelpar kan du skapa virtuella datorer i Azure som använder SSH-nycklar för autentisering, vilket gör att inga lösenord krävs för att logga in. Den här artikeln visar hur du snabbt skapa och använda en SSH nyckelfilen privat-offentligt nyckelpar för Linux virtuella datorer. Du kan utföra dessa steg med Azure Cloud Shell, en macOS eller Linux-värden, Windows-undersystem för Linux och andra verktyg som stöder OpenSSH. 

Flera bakgrund och exempel finns [detaljerade steg för att skapa SSH-nyckeln par](create-ssh-keys-detailed.md).

Ytterligare sätt att skapa och använda SSH-nycklar på en Windows-dator, se [hur du använder SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Använd den `ssh-keygen` kommando för att generera SSH offentliga och privata nyckelfiler som är som standard som skapats i den `~/.ssh` directory. Du kan ange en annan plats och en ytterligare lösenfras (ett lösenord för åtkomst till filen för privat nyckel) när du uppmanas till detta. Om det finns en SSH-nyckel på den aktuella platsen, skrivs filerna över.

```bash
ssh-keygen -t rsa -b 2048
```

Om du använder den [Azure CLI 2.0](/cli/azure) för att skapa den virtuella datorn, kan du eventuellt generera SSH offentliga och privata nyckelfiler genom att köra den [az vm skapa](/cli/azure/vm#az_vm_create) kommandot med de `--generate-ssh-keys` alternativet. Nycklarna lagras i katalogen ~/.ssh. Observera att det här kommandoalternativet inte över nycklar, om de redan finns på den platsen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ange offentlig SSH-nyckel när du distribuerar en virtuell dator
För att skapa en Linux VM som använder SSH-nycklar för autentisering, ange din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure-portalen CLI Resource Manager-mallar eller andra metoder:

* [Skapa en virtuell Linux-dator med Azure-portalen](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux VM som använder en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Om du inte är bekant med formatet för en offentlig SSH-nyckel kan du se din offentliga nyckel genom att köra `cat` enligt följande ersätter `~/.ssh/id_rsa.pub` med din egen offentlig nyckelfil plats:

```bash
cat ~/.ssh/id_rsa.pub
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen för att använda det i Azure Portal eller i en Resource Manager-mall ska du se till att inte kopiera några extra blanksteg. Till exempel om du använder macOS, kan du skicka fil för offentlig nyckel (som standard `~/.ssh/id_rsa.pub`) till **pbcopy** att kopiera innehållet (det finns andra Linux-program som gör samma sak som **xclip**).

Den offentliga nyckeln som placeras på din Linux VM i Azure är som standard i `~/.ssh/id_rsa.pub`, såvida inte du har ändrat plats när du skapade nycklarna. Om du använder den [Azure CLI 2.0](/cli/azure) för att skapa den virtuella datorn med en befintlig offentlig nyckel, anger du värdet eller platsen för den här offentliga nyckel genom att köra den [az vm skapa](/cli/azure/vm#az_vm_create) kommandot med de `--ssh-key-value` alternativet. 

## <a name="ssh-to-your-vm"></a>SSH till den virtuella datorn
Med den offentliga nyckeln som distribueras på Azure VM, och den privata nyckeln i det lokala systemet SSH till den virtuella datorn med IP-adressen eller DNS-namnet på den virtuella datorn. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörsanvändarnamn och fullständigt kvalificerat domännamn (eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade ditt nyckelpar ska du ange lösenfrasen när frågan kommer under inloggningen. (Servern läggs till i din `~/.ssh/known_hosts`-mapp, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på din virtuella Azure-dator ändras eller när servernamnet tas bort från `~/.ssh/known_hosts`.)

Lösenord är som standard inaktiverade för virtuella datorer som skapas med SSH-nycklar för att göra råstyrkeattacker något dyrare och därför svåra. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du skapar en enkel SSH-nyckel för snabb användning. 

* Om du behöver mer hjälp att arbeta med SSH-nyckel finns [detaljerade steg för att skapa och hantera SSH-nyckeln par](create-ssh-keys-detailed.md).

* Om du har problem med SSH-anslutningar till en Azure VM, se [felsökning av SSH-anslutningar till en Azure Linux VM](troubleshoot-ssh-connection.md).


