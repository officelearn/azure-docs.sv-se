---
title: Distribuera Azure-dedikerade värdar med hjälp av Azure Portal
description: Distribuera virtuella datorer till dedikerade värdar med hjälp av Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 5af09cf7ef6c811a239a64c5c6349c3625316177
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970742"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Distribuera virtuella datorer till dedikerade värdar med hjälp av portalen

Den här artikeln vägleder dig genom hur du skapar en dedikerad Azure- [värd](dedicated-hosts.md) som värd för dina virtuella datorer. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. I sökrutan ovanför listan över Azure Marketplace-resurser, söker du efter och väljer **Ubuntu Server 16.04 LTS** av Canonical och väljer sedan **Skapa**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad och välj sedan *myDedicatedHostsRG* som **resurs grupp**. 
1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Plats**.
1. I **tillgänglighets alternativ** väljer du **tillgänglighets zon**väljer du *1* i list rutan.
1. Välj **ändra storlek**som storlek. I listan över tillgängliga storlekar väljer du en från Esv3-serien, som **standard E2s v3**. Du kan behöva rensa filtret för att se alla tillgängliga storlekar.
1. Under **Administratörskonto** väljer du **Offentlig SSH-nyckel**, skriver ditt användarnamn och klistrar sedan in den offentliga nyckeln i textrutan. Ta bort eventuella inledande eller avslutande blanksteg i din offentliga nyckel.

    ![Administratörskonto](./media/quick-create-portal/administrator-account.png)

1. Under **regler för inkommande port** > **offentliga inkommande portar**väljer du **Tillåt valda portar** och väljer **SSH (22)** i list rutan. 
1. Längst upp på sidan väljer du fliken **Avancerat** och i avsnittet **värd** väljer du *myHostGroup* för **värd grupp** *och värd* **.** 
    ![välja värd grupp och värd](./media/dedicated-hosts-portal/advanced.png)
1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.
1. När du ser meddelandet att valideringen har slutförts väljer du **skapa**.

Det tar några minuter för den virtuella datorn att distribueras.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i Översikt över [dedikerade värdar](dedicated-hosts.md) .

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också distribuera en dedikerad värd med hjälp av [Azure CLI](dedicated-hosts-cli.md).



