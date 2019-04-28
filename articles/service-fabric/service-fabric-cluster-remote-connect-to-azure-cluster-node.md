---
title: Fjärransluta till en Azure Service Fabric-klusternod | Microsoft Docs
description: Lär dig mer om att fjärransluta till en scale set-instans (en Service Fabric klusternod).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110931"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärransluta till en VM scale set-instans eller en klusternod
I ett Service Fabric-kluster som körs i Azure, varje nodtyp i klustret som du definierar [ställer in en separat VM-skalningsuppsättning](service-fabric-cluster-nodetypes.md).  Du kan fjärransluta till specifika skalningsuppsättningsinstanser (noder).  Till skillnad från en instans virtuella datorer har inte skalningsuppsättningsinstanser sina egna virtuella IP-adresser. Detta kan vara svårt när du söker efter en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

Utför följande steg för att hitta en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

1. Hämta inkommande NAT-regler för Remote Desktop Protocol (RDP).

    Varje nodtyp som definierats i ditt kluster har normalt en egen virtuell IP-adress och en särskild belastningsutjämnare. Som standard heter belastningsutjämnaren för en nodtyp med följande format: *LB-{kluster-name}-{nodtyp}*, till exempel *LB-mycluster-klientdel*. 
    
    På sidan för belastningsutjämnaren i Azure-portalen, väljer **inställningar** > **inkommande NAT-regler**: 

    ![Inkommande NAT-regler för belastningsutjämnare](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Följande skärmbild visar inkommande NAT-regler för en nodtyp som heter FrontEnd: 

    ![Inkommande NAT-regler för belastningsutjämnare](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    För varje nod, IP-adressen som visas i den **mål** kolumnen den **TARGET** kolumnen innehåller scale set-instans och **SERVICE** kolumnen innehåller portnumret. För anslutning till tilldelas portar till varje nod i stigande ordning och börjar med port 3389.

    Du kan också hitta inkommande NAT-regler i den `Microsoft.Network/loadBalancers` i Resource Manager-mallen för klustret.
    
2. För att bekräfta den inkommande porten till målet portmappning för en nod, kan du klicka på dess regel och titta på den **rikta port** värde. I följande skärmbild visas den inkommande NAT-regeln för den **klientdel (1 instans)** nod i föregående steg. Observera att även om (inkommande) portnumret är 3390, mappas målporten till port 3389, port för RDP-tjänsten på måldatorn.  

    ![Portmappning för mål](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Som standard för Windows-kluster är målporten port 3389, som mappar till RDP-tjänsten på målnoden. För Linux-kluster är målporten port 22, som mappar till Secure Shell (SSH)-tjänsten.

3. Fjärransluta till den specifika noden (skaluppsättningsinstans). Du kan använda det användarnamn och lösenord som du angav när du skapade klustret eller andra autentiseringsuppgifter som du har konfigurerat. 

    I följande skärmbild visas med anslutning till fjärrskrivbord för att ansluta till den **klientdel (1 instans)** nod i ett Windows-kluster:
    
    ![Anslutning till fjärrskrivbord](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Du kan ansluta med SSH (följande exempel återanvänder samma IP-adress och port för kortfattat) på Linux-noder:

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Nästa steg finns i följande artiklar:
* Se den [översikt över ”distribuera var som helst”-funktionen och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster security](service-fabric-cluster-security.md).
* [Uppdatera RDP-portintervallvärden](./scripts/service-fabric-powershell-change-rdp-port-range.md) i klustret virtuella datorer efter distributionen
* [Ändra administratörens användarnamn och lösenord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella datorer klustret

