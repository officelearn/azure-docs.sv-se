---
title: Distribuera Azure-dedikerade värdar med Azure-portalen
description: Distribuera virtuella datorer till dedikerade värdar med Azure-portalen.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086753"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Distribuera virtuella datorer till dedikerade värdar med hjälp av portalen

Den här artikeln guidar dig genom hur du skapar en [Azure-dedikerad värd](dedicated-hosts.md) som värd för dina virtuella datorer. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. I sökrutan ovanför listan över Azure Marketplace-resurser, söker du efter och väljer **Ubuntu Server 16.04 LTS** av Canonical och väljer sedan **Skapa**.
1. Kontrollera att rätt prenumeration är markerad under **Projektinformation**på fliken **Grunderna** och välj sedan *myDedicatedHostsRG* som **resursgrupp**. 
1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Plats**.
1. Välj *1* **Availability zone**i listrutan i **tillgänglighetsalternativ.**
1. För storleken väljer du **Ändra storlek**. I listan över tillgängliga storlekar väljer du en från Esv3-serien, till exempel **Standard E2s v3**. Du kan behöva rensa filtret för att se alla tillgängliga storlekar.
1. Under **Administratörskonto** väljer du **Offentlig SSH-nyckel**, skriver ditt användarnamn och klistrar sedan in den offentliga nyckeln i textrutan. Ta bort eventuella inledande eller avslutande blanksteg i din offentliga nyckel.

    ![Administratörskonto](./media/quick-create-portal/administrator-account.png)

1. Under **Inkommande portregler** > **Offentliga inkommande portar**väljer du **Tillåt valda portar** och väljer sedan **SSH (22)** i listrutan. 
1. Högst upp på sidan väljer du fliken **Avancerat** och väljer i avsnittet *Värdgrupp* för **Host** **värdgrupp** och *myHost* för värden för **värden**. 
    ![Välj värdgrupp och värd](./media/dedicated-hosts-portal/advanced.png)
1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.
1. När meddelandet som valideringen har skickats visas väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras.

## <a name="add-an-existing-vm"></a>Lägga till en befintlig virtuell dator 

Du kan lägga till en spännande virtuell dator till en dedikerad värd, men den virtuella datorn måste först vara Stop\Deallocated. Innan du flyttar en virtuell dator till en dedikerad värd kontrollerar du att vm-konfigurationen stöds:

- Den virtuella datorns storlek måste vara i samma storlek som den dedikerade värden. Om din dedikerade värd är DSv3 kan storleken på den virtuella datorn vara Standard_D4s_v3, men det kan inte vara en Standard_A4_v2. 
- Den virtuella datorn måste finnas i samma region som den dedikerade värden.
- Den virtuella datorn kan inte ingå i en närhetsplaceringsgrupp. Ta bort den virtuella datorn från proximityplaceringsgruppen innan du flyttar den till en dedikerad värd. Mer information finns i [Flytta en virtuell dator från en närhetsplaceringsgrupp](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Den virtuella datorn kan inte vara i en tillgänglighetsuppsättning.
- Om den virtuella datorn finns i en tillgänglighetszon måste den vara samma tillgänglighetszon som värdgruppen. Tillgänglighetszoninställningarna för den virtuella datorn och värdgruppen måste matcha.

Flytta den virtuella datorn till en dedikerad värd med hjälp av [portalen](https://portal.azure.com).

1. Öppna sidan för den virtuella datorn.
1. Välj **Stoppa** om du vill stoppa\deallocate den virtuella datorn.
1. Välj **Konfiguration** på den vänstra menyn.
1. Välj en värdgrupp och en värd på de rullgardinsmenyerna.
1. När du är klar väljer du **Spara** högst upp på sidan.
1. När den virtuella datorn har lagts till i värden väljer du **Översikt** på den vänstra menyn.
1. Högst upp på sidan väljer du **Start** för att starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i översikten [Dedikerade värdar.](dedicated-hosts.md)

- Det finns exempelmall, som finns [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och feldomäner för maximal återhämtning i en region.

- Du kan också distribuera en dedikerad värd med [Azure CLI](dedicated-hosts-cli.md).



