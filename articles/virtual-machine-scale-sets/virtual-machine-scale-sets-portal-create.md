---
title: "Skapa en Skalningsuppsättning virtuell dator med hjälp av Azure portal | Microsoft Docs"
description: "Distribuera skaluppsättningar med hjälp av Azure portal."
keywords: "Skaluppsättningar för den virtuella datorn"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Så här skapar du en Virtual Machine Scale Set med Azure-portalen
Den här kursen visar hur lätt det är att skapa en Virtual Machine Scale Set i bara några minuter med hjälp av Azure portal. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Välja VM-avbildning från Marketplace
Du kan enkelt distribuera en skala med CentOS virtuell CoreOS, Debian, Ubuntu Server, andra avbildningar för Linux och Windows Server-avbildningar från portalen.

Först gå till den [Azure-portalen](https://portal.azure.com) i en webbläsare. Klicka på **ny**, söka efter **skaluppsättning**, och välj sedan den **virtuella datorns skaluppsättning** post:

![skaluppsättning för virtuell dator i Azure portal-sökning](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>Skapa skalningsuppsättningen
Nu kan du använda standardinställningarna och snabbt skapa skaluppsättning.

* Ange ett namn för skaluppsättning.  
Det här namnet blir base på FQDN på belastningsutjämnaren framför skaluppsättning, så se till att namnet är unikt över alla Azure.

* Välj din önskade OS-typen.

* Ange ditt önskade användarnamn och välj vilken autentiseringstyp som du föredrar.  
Om du väljer ett lösenord, det måste vara minst 12 tecken långa och uppfylla tre av fyra följande krav på komplexitet: en gemen, en versal, en siffra och ett specialtecken. Läs mer om [krav för användarnamn och lösenord](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Om du väljer **offentliga SSH-nyckeln**, vara säker på att du bara vill klistra in i den offentliga nyckeln *inte* din privata nyckel:

* Välj **Ja** eller **nr** till **aktivera skalning utöver 100 instanser**.  
Om Ja, skaluppsättning kan sträcka sig över flera grupper för placering. Mer information finns i [denna dokumentation](./virtual-machine-scale-sets-placement-groups.md).

* Se till att välja en lämplig **instansen storlek**.  
Mer information om storlekar för virtuella datorer, se endera [Windows VM-storlekar](..\virtual-machines\windows\sizes.md) eller [Linux VM-storlekar](..\virtual-machines\linux\sizes.md).

* Ange önskade resursgruppens namn och plats.  
Om din region och **instansen storlek** stöder tillgänglighet zoner i **tillgänglighet zoner** fältet har aktiverats. Mer information om tillgänglighet zoner finns [översikt](../availability-zones/az-overview.md) artikel.

* Ange den önskade domännamnet (bas FQDN för belastningsutjämnaren framför skaluppsättning).  
Den här etiketten måste vara unikt inom alla Azure.

* Välj önskat operativsystem diskavbildning, instansantal och storleken på datorn.

* Välj önskad disk-typ: hanterad eller ohanterad.  
Mer information finns i [denna dokumentation](./virtual-machine-scale-sets-managed-disks.md). Om du väljer att låta skaluppsättningen sträcker sig över flera placering grupper, det här alternativet är inte tillgänglig eftersom hanterade diskar krävs för skalningsuppsättningar fördela placering grupper.

* Aktivera eller inaktivera Autoskala och konfigurera om aktiverat.

![virtuella Azure-datorn scale set-portal, skapa fråga](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>Ansluta till en virtuell dator i skaluppsättning
Om du vill begränsa din skaluppsättningen till en enda placering grupp distribueras skaluppsättning med NAT-regler som är konfigurerad så att du kan ansluta till skaluppsättningen enkelt (om inte, att ansluta till de virtuella datorerna i skaluppsättning, du kan behöva skapa en jumpbox i samma virtuella nätverk som skaluppsättning). Om du vill visa dem genom att navigera till den `Inbound NAT Rules` fliken i belastningsutjämnaren för skaluppsättning:

![skaluppsättning för virtuell dator i Azure portal nat-regler](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

Du kan ansluta till varje virtuell dator i skaluppsättningen med dessa NAT-regler. Till exempel för en skaluppsättning för Windows om det finns en NAT-regel för inkommande port 50000, du kunde ansluta till den datorn via RDP på `<load-balancer-ip-address>:50000`. För en Linux-skaluppsättning kan du ansluta med hjälp av kommandot `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Nästa steg
Dokumentationen om hur du distribuerar skala anger från CLI, finns [denna dokumentation](virtual-machine-scale-sets-cli-quick-create.md).

Dokumentationen om hur du distribuerar skala anger från PowerShell, finns [denna dokumentation](virtual-machine-scale-sets-windows-create.md).

Dokumentationen om hur du distribuerar skala anger från Visual Studio, finns [denna dokumentation](virtual-machine-scale-sets-vs-create.md).

Allmän dokumentation finns på den [dokumentationen översiktssidan för skalningsuppsättningar](virtual-machine-scale-sets-overview.md).

Allmän information finns i [huvudsakliga Landningssida för skalningsuppsättningar](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

