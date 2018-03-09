---
title: "Skapa och använd ett SSH-nyckelpar för virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Så här skapar och använder du ett offentligt och privat SSH-nyckelpar för virtuella Linux-datorer i Azure för att förbättra säkerheten för autentiseringsprocessen."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: ecd3a01ee5591cb09140edb1b1290ff2d4510200
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Så här skapar du säkert ett offentligt och ett privat SSH-nyckelpar för virtuella Linux-datorer i Azure
Med ett SSH-nyckelpar kan du skapa virtuella datorer i Azure som använder SSH-nycklar för autentisering, vilket gör att inga lösenord krävs för att logga in. I den här artikeln visas hur du kan skapa ett RSA-nyckelfilpar med SSH-protokollversion 2 med en offentlig och en privat nyckel för virtuella Linux-datorer. Du kan utföra dessa steg med Azure Cloud Shell, en macOS- eller Linux-värd eller Windows-undersystem för Linux. Mer detaljerade steg och ytterligare exempel finns i [detaljerade steg för att skapa SSH-nyckelpar och certifikat](create-ssh-keys-detailed.md).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Använd den `ssh-keygen` kommando för att skapa SSH offentliga och privata nyckelfiler som är som standard som skapats i den `~/.ssh` directory. Du kan ange en annan plats och lösenfrasen ytterligare (ett lösenord för åtkomst till filen för privat nyckel) när du uppmanas till detta. Om SSH-nyckel finns i den aktuella platsen, skrivs över.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>Använda SSH-nyckelpar
Den offentliga nyckel du placerar i din virtuella Linux-dator i Azure lagras som standard i `~/.ssh/id_rsa.pub`, om du inte har ändrat plats när du skapade den. Om du använder [Azure CLI 2.0](/cli/azure) när du skapar din virtuella dator anger du plats för den offentliga nyckeln när du använder [az vm create](/cli/azure/vm#az_vm_create) med alternativet `--ssh-key-path`. Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen för att använda det i Azure Portal eller i en Resource Manager-mall ska du se till att inte kopiera några extra blanksteg. Om du exempelvis använder OS X kan du skicka den offentliga nyckelfilen (som standard **~/.ssh/id_rsa.pub**) till **pbcopy** för att kopiera innehållet (det finns andra Linux-program som gör samma sak, som `xclip`).

Om du inte är bekant med offentliga nycklar för SSH kan du se din offentliga nyckel genom att köra `cat` enligt följande, och ersätta `~/.ssh/id_rsa.pub` med din egen plats för offentlig nyckelfil:

```bash
cat ~/.ssh/id_rsa.pub
```

Med den offentliga nyckeln på din virtuella Azure-dator, SSH till din virtuella dator med datorns IP-adress eller DNS-namn (kom ihåg att ersätta `azureuser` och `myvm.westus.cloudapp.azure.com` nedan med adminanvändarnamnet och det fullständigt kvalificerade domännamnet – eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade ditt nyckelpar ska du ange lösenfrasen när frågan kommer under inloggningen. (Servern läggs till i din `~/.ssh/known_hosts`-mapp, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på din virtuella Azure-dator ändras eller när servernamnet tas bort från `~/.ssh/known_hosts`.)

## <a name="next-steps"></a>Nästa steg

Lösenord är som standard inaktiverade för virtuella datorer som skapas med SSH-nycklar för att göra råstyrkeattacker något dyrare och därför svåra. I det här avsnittet beskrivs hur du skapar en enkel SSH-nyckel för snabb användning. Om du behöver mer hjälp för att skapa SSH-nyckelpar eller om du behöver fler certifikat finns [detaljerade steg för att skapa SSH-nyckelpar och certifikat](create-ssh-keys-detailed.md).

Du kan skapa virtuella datorer som använder ditt SSH-nyckelpar med Azure Portal, CLI och mallar:

* [Skapa en säker virtuell Linux-dator med hjälp av Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en säker virtuell Linux-dator med hjälp av Azure CLI 2.0)](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
