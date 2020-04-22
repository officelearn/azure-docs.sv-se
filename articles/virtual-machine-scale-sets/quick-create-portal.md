---
title: Snabbstart - Skapa en skala för virtuella datorer i Azure-portalen
description: Kom igång med dina distributioner genom att lära dig hur du snabbt skapar en virtuell dator skala Azure-portalen.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: mvc, H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: jushiman
ms.openlocfilehash: 5855c53a73ac94985f34f229fbe5ab77067ef17d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680426"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Snabbstart: Skapa en VM-skalningsuppsättning med Azure Portal

Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. En Azure-lastbalanserare distribuerar sedan trafiken till de virtuella datorinstanserna i skalningsuppsättningen. I den här snabbstarten skapar du en VM-skalningsuppsättning i Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Azure [belastningsutjämnare](../load-balancer/load-balancer-overview.md) distribuerar inkommande trafik mellan felfria instanser av virtuella datorer. 

Skapa först en offentlig standardbelastningsutjämning med hjälp av portalen. Det namn och den offentliga IP-adress som du skapar konfigureras automatiskt som lastbalanserarens klientdel.

1. Skriv **belastningsutjämnaren**i sökrutan . Under **Marketplace** i sökresultaten väljer du **Belastningsutjämnare**.
1. Ange eller välj följande information på fliken Grunderna på sidan **Skapa belastningsutjämnare:** **Basics**

    | Inställning                 | Värde   |
    | ---| ---|
    | Prenumeration  | Välj din prenumeration.    |    
    | Resursgrupp | Välj **Skapa nytt** och skriv *myVMSSResourceGroup* i textrutan.|
    | Namn           | *myLoadBalancer*         |
    | Region         | Välj **östra USA**.       |
    | Typ          | Välj **Offentlig**.       |
    | SKU           | Välj **Standard**.       |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress  | *MyPip (på väg)*   |
    | Tilldelning| Statisk |

1. När du är klar väljer du **Granska + skapa** 
1. När valideringen har godkänts väljer du **Skapa**. 

![Skapa en lastbalanserare](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Skapa VM-skalningsuppsättningar
Du kan distribuera en skalningsuppsättning som anges med en Windows Server-avbildning eller en Linux-avbildningen som RHEL, CentOS, Ubuntu eller SLES.

1. Skriv **Skala i** sökrutan. Välj **skaluppsättningar för virtuella datorer**under **Marketplace.** Sidan **Skapa en uppsättning** för virtuell dator öppnas. 
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myVMSSResourceGroup* för namnet och välj sedan **OK** . 
1. Skriv *myScaleSet* som namn på skalningsuppsättningen.
1. Välj en region som ligger nära ditt område i **Region.**
1. Lämna standardvärdet för **ScaleSet-virtuella datorer** för **Orchestrator**.
1. Välj en marknadsplatsavbildning för **Image**. I det här exemplet har vi valt *Ubuntu Server 18.04 LTS*.
1. Ange önskat användarnamn och välj den autentiseringstyp du föredrar.
   - Ett **lösenord** måste innehålla minst 12 tecken och måste uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken. Mer information finns i [kraven om användarnamn och lösenord](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Om du väljer en Linux OS-diskavbildning kan du istället välja **offentlig SSH-nyckel**. Du behöver bara ange din offentliga nyckel, t.ex. *~/.ssh/id_rsa.pub*. Du kan använda Azure Cloud Shell från portalen för att [skapa och använda SSH-nycklar](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Skapa en VM-skalningsuppsättning](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Välj **Nästa** om du vill flytta de andra sidorna. 
1. Lämna standardvärdena för sidorna **Instans** **och Diskar.**
1. Välj **Ja** under **Belastningsutjämning**på sidan **Nätverk** om du vill lägga skalningsuppsättningsinstanserna bakom en belastningsutjämnare. 
1. I **Belastningsutjämningsalternativ**väljer du **Azure load balancer**.
1. Välj *myLoadBalancer* som du skapade tidigare **i Välj en belastningsutjämnare.**
1. För **Välj en backend pool**väljer du Skapa **ny**, skriv *myBackendPool*och välj sedan **Skapa**.
1. När du är klar väljer du **Granska + skapa**. 
1. När valideringen har godkänts väljer du **Skapa** för att distribuera skalningsuppsättningen.


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser när de inte längre behövs. Om du vill göra det markerar du resursgruppen för skalningsuppsättningen och väljer sedan **Ta bort**.


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat en grundläggande skalningsuppsättning i Azure Portal. Om du vill veta mer kan du gå till självstudiekursen om hur man skapar och hanterar VM-skalningsuppsättningar för Azure.

> [!div class="nextstepaction"]
> [Skapa och hantera VM-skalningsuppsättningar för Azure](tutorial-create-and-manage-powershell.md)
