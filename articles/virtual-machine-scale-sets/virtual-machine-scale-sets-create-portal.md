---
title: Skapa en Virtual Machine Scale Set i Azure portal | Microsoft Docs
description: Lär dig att snabbt skapa en virtuell dator skala i Azure-portalen
keywords: skaluppsättningar för den virtuella datorn
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6157f15ef471676424a2f7027c7e9299e2218d4b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201467"
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Skapa en Virtual Machine Scale Set i Azure-portalen
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. I den här komma igång artikel skapar du en virtuell dator skaluppsättningen i Azure-portalen. Du kan också skapa en skala som anges med den [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) eller [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på http://portal.azure.com.


## <a name="create-virtual-machine-scale-set"></a>Skapa virtuella datorns skaluppsättning
Du kan distribuera en skala som anges med en avbildning för Windows Server eller Linux-avbildningen som RHEL, CentOS, Ubuntu eller SLES.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Sök efter *skaluppsättning*, Välj **virtuella datorns skaluppsättning**och välj **skapa**.
3. Ange ett namn för skaluppsättning som *myScaleSet*.
4. Välj din önskade OS-typ, till exempel *Windows Server 2016 Datacenter*.
5. Ange önskade resursgruppens namn, t.ex *myResourceGroup*, och plats, t.ex *östra USA*.
6. Ange ditt önskade användarnamn och välj vilken autentiseringstyp som du föredrar.
    - En **lösenord** måste vara minst 12 tecken långa och uppfylla tre av fyra följande krav på komplexitet: en gemen, en versal, en siffra och ett specialtecken. Mer information finns i [användarnamn och lösenord krav](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Om du väljer en Linux OS-diskavbildning du istället välja **offentliga SSH-nyckeln**. Ange endast den offentliga nyckeln som *~/.ssh/id_rsa.pub*. Du kan använda Azure Cloud-gränssnittet från portalen för att [skapar och använder SSH-nycklar](../virtual-machines/linux/mac-create-ssh-keys.md).

7. Ange en **offentliga IP-adressnamn**, som *myPublicIP*.
8. Ange ett unikt **domännamnsetiketten**, som *myuniquedns*. Den här DNS-etikett utgör grunden FQDN för belastningsutjämnaren framför skaluppsättning.
9. För att bekräfta skaluppsättning alternativ, Välj **skapa**.

    ![Skapa en virtuell dator skaluppsättningen i Azure-portalen](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Ansluta till en virtuell dator i skaluppsättning
När du skapar en skala som angetts i portalen, skapas en belastningsutjämnare. Network Address Translation (NAT) regler för att distribuera trafiken till scale set-instanser för fjärranslutningar, till exempel RDP eller SSH.

Ange instanser för att visa dessa NAT regler och anslutningsinformationen för nivå:

1. Markera den resursgrupp som du skapade i föregående steg, som *myResourceGroup*.
2. Listan över resurser och välj din **belastningsutjämnaren**, som *myScaleSetLab*.
3. Välj **inkommande NAT-regler** på menyn till vänster i fönstret.

    ![Inkommande NAT-regler kan du ansluta till virtuella scale set instanser](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

Du kan ansluta till varje virtuell dator i skaluppsättningen med dessa NAT-regler. Varje VM-instans visas en mål-IP-adress och värdet för TCP-porten. Om den IP-adressen är till exempel *104.42.1.19* och TCP-porten är *50001*, du ansluter till VM-instans på följande sätt:

- Ansluta till VM-instans med RDP för en skaluppsättning för Windows på `104.42.1.19:50001`
- Ansluta till VM-instans med SSH för en Linux-skaluppsättning på `ssh azureuser@104.42.1.19 -p 50001`

När du uppmanas, anger du de autentiseringsuppgifter du angav i föregående steg när du skapade skaluppsättning. Scale set-instanserna är vanliga virtuella datorer som du kan interagera med som vanligt. Mer information om hur du distribuerar och köra program på din skala uppsättning instanser finns [distribuera ditt program på virtuella datorer](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs, ta bort resursgruppen, skalan och alla relaterade resurser. Det gör du genom att välja resursgruppen för den virtuella datorn och klicka på **Ta bort**.


## <a name="next-steps"></a>Nästa steg
I den här komma igång artikeln skapat du en grundläggande skaluppsättningen i Azure-portalen. Du kan få bättre skalbarhet och automatisering genom att utöka skalningsuppsättningarna med hjälp av följande instruktionsartiklar:

- [Distribuera din app på VM-skalningsuppsättningar](virtual-machine-scale-sets-deploy-app.md)
- Skala automatiskt med [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md) eller [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Använda automatiska uppgraderingar av operativsystemet för VM-instanser i skalningsuppsättningar](virtual-machine-scale-sets-automatic-upgrade.md)
