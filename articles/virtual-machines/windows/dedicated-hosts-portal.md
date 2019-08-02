---
title: Distribuera Azure-dedikerade värdar med hjälp av Azure Portal | Microsoft Docs
description: Distribuera virtuella datorer till dedikerade värdar med hjälp av Azure Portal.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 0a3cb76d1e28559727957c1007b0992fb8e90f2b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700566"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Förhandsversion: Distribuera virtuella datorer till dedikerade värdar med hjälp av portalen

Den här artikeln vägleder dig genom hur du skapar en dedikerad Azure- [värd](dedicated-hosts.md) som värd för dina virtuella datorer. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure Portal.
1. På sidan **ny** under **populär**väljer du **Windows Server 2016 Data Center**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad och välj sedan *myDedicatedHostsRG* som **resurs grupp**. 
1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Plats**.
1. I **tillgänglighets alternativ** väljer du **tillgänglighets zon**väljer du *1* i list rutan.
1. Välj **ändra storlek**som storlek. I listan över tillgängliga storlekar väljer du en från Esv3-serien, som **standard E2s v3**. Du kan behöva rensa filtret för att se alla tillgängliga storlekar.
1. Under **Administratörskonto**, anger du ett användarnamn, som *azureuser*, och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Under **regler för inkommande port**väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** i list rutan.
1. Längst upp på sidan väljer du fliken **Avancerat** och i avsnittet **värd** väljer du *myHostGroup* för **värd grupp** *och värd* **.** 
    ![Välj värd grupp och värd](./media/dedicated-hosts-portal/advanced.png)
1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.
1. När du ser meddelandet att valideringen har slutförts väljer du **skapa**.


## <a name="next-steps"></a>Nästa steg

- Mer information finns i Översikt över [dedikerade värdar](dedicated-hosts.md) . 

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också distribuera en dedikerad värd med [Azure PowerShell](dedicated-hosts-powershell.md).