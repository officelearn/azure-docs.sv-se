---
title: Skapa SSH-nycklar i Azure Portal
description: Lär dig hur du skapar och lagrar SSH-nycklar i Azure Portal för att ansluta virtuella Linux-datorer.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 17ab7ee75e335d686bf308c4b15a53dc4e2e6b0e
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041756"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Skapa och lagra SSH-nycklar i Azure Portal

Om du ofta använder portalen för att distribuera virtuella Linux-datorer kan du göra det enklare att använda SSH-nycklar genom att skapa dem direkt i portalen eller ladda upp dem från datorn.

Du kan skapa en SSH-nycklar när du först skapar en virtuell dator och återanvända dem för andra virtuella datorer. Du kan också skapa SSH-nycklar separat, så att du har en uppsättning nycklar som är lagrade i Azure för att passa dina organisations behov. 

Om du har befintliga nycklar och vill förenkla användningen av dem i portalen kan du ladda upp dem och lagra dem i Azure för åter användning.

Mer detaljerad information om hur du skapar och använder SSH-nycklar med virtuella Linux-datorer finns i [använda SSH-nycklar för att ansluta till virtuella Linux-datorer](./linux/ssh-from-windows.md).

## <a name="generate-new-keys"></a>Generera nya nycklar

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Skriv *SSH* att söka efter längst upp på sidan. Under **Marketplace*väljer du **SSH-nycklar**.

1. På sidan **SSH-nyckel** väljer du **skapa**.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Skapa en ny resurs grupp och generera ett SSH-nyckelpar":::

1. I **resurs grupp** väljer du **Skapa nytt** för att skapa en ny resurs grupp för att lagra dina nycklar. Skriv ett namn för resurs gruppen och välj **OK**.

1. I **region** väljer du en region för att lagra dina nycklar. Du kan använda nycklarna i vilken region som helst, detta är bara den region där de kommer att lagras.

1. Ange ett namn för nyckeln i **nyckel par namn**.

1. I **källa för offentlig SSH-nyckel**väljer du **generera källa för offentlig nyckel**. 

1. När du är färdig väljer du **Granska + skapa**.

1. När verifieringen har godkänts väljer du **skapa**.

1. Sedan kan du hämta ett popup-fönster till, välja **Hämta privat nyckel och skapa resurs**. SSH-nyckeln överförs som en. PEM-fil.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="Hämta den privata nyckeln som en. PEM-fil":::

1. När. pem-filen har hämtats kanske du vill flytta den någonstans på datorn där det är enkelt att peka på från SSH-klienten.


## <a name="connect-to-the-vm"></a>Anslut till VM:en

Öppna en PowerShell-prompt på din lokala dator och skriv:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Skriv till exempel:`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Ladda upp en SSH-nyckel

Du kan också ladda upp en offentlig SSH-nyckel för att lagra i Azure. Information om hur du skapar ett SSH-nyckelpar finns i [använda SSH-nycklar för att ansluta till virtuella Linux-datorer](./linux/ssh-from-windows.md).

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Skriv *SSH* att söka efter längst upp på sidan. Under **Marketplace*väljer du **SSH-nycklar**.

1. På sidan **SSH-nyckel** väljer du **skapa**.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Ladda upp en offentlig SSH-nyckel som ska lagras i Azure":::

1. I **resurs grupp** väljer du **Skapa nytt** för att skapa en ny resurs grupp för att lagra dina nycklar. Skriv ett namn för resurs gruppen och välj **OK**.

1. I **region** väljer du en region för att lagra dina nycklar. Du kan använda nycklarna i vilken region som helst, detta är bara den region där de kommer att lagras.

1. Ange ett namn för nyckeln i **nyckel par namn**.

1. I **källa för offentlig SSH-nyckel**väljer du **Ladda upp befintlig offentlig nyckel**. 

1. Klistra in det fullständiga innehållet i den offentliga nyckeln i **överförings nyckeln** och välj sedan **Granska + skapa**.

1. När verifieringen är klar väljer du **skapa**. 

När nyckeln har laddats upp kan du välja att använda den när du skapar en virtuell dator.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder SSH-nycklar med virtuella Azure-datorer finns i [använda SSH-nycklar för att ansluta till virtuella Linux-datorer](./linux/ssh-from-windows.md).
