---
title: Distribuera Azure-dedikerade värdar med hjälp av Azure Portal
description: Distribuera virtuella datorer till dedikerade värdar med hjälp av Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 19bd7960c4a6a02566d86d51ff7e21f4e570c34d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373648"
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

1. Under **ingående port regler**  >  **offentliga inkommande portar**väljer du **Tillåt valda portar** och väljer **SSH (22)** i list rutan. 
1. Längst upp på sidan väljer du fliken **Avancerat** och i avsnittet **värd** väljer du *myHostGroup* för **värd grupp** *och värd* **.** 
    ![Välj värd grupp och värd](./media/dedicated-hosts-portal/advanced.png)
1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.
1. När du ser meddelandet att valideringen har slutförts väljer du **skapa**.

Det tar några minuter för den virtuella datorn att distribueras.

## <a name="add-an-existing-vm"></a>Lägg till en befintlig virtuell dator 

Du kan lägga till en virtuell dator som avslutas till en dedikerad värd, men den virtuella datorn måste först vara Stop\Deallocated. Innan du flyttar en virtuell dator till en dedikerad värd kontrollerar du att VM-konfigurationen stöds:

- Storleken på den virtuella datorn måste vara i samma storleks familj som den dedikerade värden. Om din dedikerade värd till exempel är DSv3, kan storleken på den virtuella datorn vara Standard_D4s_v3, men det gick inte att Standard_A4_v2. 
- Den virtuella datorn måste finnas i samma region som den dedikerade värden.
- Den virtuella datorn kan inte ingå i en närhets placerings grupp. Ta bort den virtuella datorn från närhets placerings gruppen innan du flyttar den till en dedikerad värd. Mer information finns i [flytta en virtuell dator från en närhets placerings grupp](../windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- Den virtuella datorn får inte finnas i en tillgänglighets uppsättning.
- Om den virtuella datorn finns i en tillgänglighets zon måste den vara samma tillgänglighets zon som värd gruppen. Inställningarna för tillgänglighets zonen för den virtuella datorn och värd gruppen måste matcha.

Flytta den virtuella datorn till en dedikerad värd med hjälp av [portalen](https://portal.azure.com).

1. Öppna sidan för den virtuella datorn.
1. Välj **stoppa** för att stop\deallocate den virtuella datorn.
1. Välj **konfiguration** på den vänstra menyn.
1. Välj en värd grupp och en värd från List menyerna.
1. När du är färdig väljer du **Spara** längst upp på sidan.
1. När den virtuella datorn har lagts till i värden väljer du **Översikt** på den vänstra menyn.
1. Längst upp på sidan väljer du **Starta** för att starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i Översikt över [dedikerade värdar](dedicated-hosts.md) .

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också distribuera en dedikerad värd med hjälp av [Azure CLI](dedicated-hosts-cli.md).
