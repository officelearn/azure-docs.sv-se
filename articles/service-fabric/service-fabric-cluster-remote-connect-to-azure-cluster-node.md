---
title: Fjärranslutning till en klusternod i Azure Service Fabric | Microsoft Docs
description: Lär dig mer om att fjärransluta till en scale set-instans (Service Fabric klusternod).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 28424f9a7a0f77882ee3360c5599549303075c18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642581"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärranslutning till en virtuell dator scale set-instans eller en klusternod
I ett Service Fabric-kluster som körs i Azure, varje nodtyp i klustret som du definierar [ställer in en separat virtuell skala](service-fabric-cluster-nodetypes.md).  Du kan ansluta fjärr till specifika scale set instanser (noder).  Till skillnad från single instance VM scale set instanser inte sina egna virtuella IP-adresser. Detta kan vara en utmaning när du söker efter en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

Utför följande steg för att hitta en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

1. Hämta inkommande NAT-regler för Remote Desktop Protocol (RDP).

    Varje nodtyp som definierats i klustret har sin egen virtuella IP-adress och en särskild belastningsutjämnare. Som standard heter belastningsutjämnaren för typen med följande format: *LB-{klusternamnet}-{nodtypen}*, till exempel *LB-mycluster-klientdel*. 
    
    På sidan för din belastningsutjämnare i Azure portal väljer **inställningar** > **inkommande NAT-regler**: 

    ![Läsa in belastningsutjämning för inkommande NAT-regler](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Följande skärmbild visar ingående NAT-regler för en nod med namnet klientdel: 

    ![Läsa in belastningsutjämning för inkommande NAT-regler](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    För varje nod, IP-adress som visas i den **mål** kolumn, och den **mål** kolumnen innehåller scale set-instans och **SERVICE** kolumnen innehåller portnumret. Portar som är allokerade till varje nod i stigande ordning som börjar med port 3389 för anslutning till.

    Du kan också hitta inkommande NAT-regler i den `Microsoft.Network/loadBalancers` avsnitt i Resource Manager-mall för klustret.
    
2. För att bekräfta den inkommande porten till målet portmappning för en nod, kan du klicka på dess regel och titta på den **mål port** värde. Följande skärmbild visar inkommande NAT-regel för den **klientdel (instans 1)** nod i föregående steg. Observera att även om (inkommande) portnumret 3390, målporten mappas till port 3389, porten för RDP-tjänsten på måldatorn.  

    ![Port målmappning](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Som standard för Windows-kluster är målporten port 3389, som mappar till RDP-tjänsten på målnoden. För Linux-kluster är målporten port 22, som mappar till tjänsten SSH (Secure Shell).

3. Fjärransluta till en viss nod (skaluppsättning instans). Du kan använda användarnamn och lösenord som du angav när du skapade klustret eller andra autentiseringsuppgifter som du har konfigurerat. 

    I följande skärmbild visas med anslutning till fjärrskrivbord för att ansluta till den **klientdel (instans 1)** nod i ett Windows-kluster:
    
    ![Anslutning till fjärrskrivbord](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Du kan ansluta med SSH (följande exempel återanvänder samma IP-adress och port för planeringsaspekter) på Linux-noder:

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Läs följande artiklar för nästa steg:
* Finns det [översikt över funktionen ”distribuera överallt” och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Uppdatera RDP-porten intervallvärden](./scripts/service-fabric-powershell-change-rdp-port-range.md) i klustret virtuella datorer efter distribution
* [Ändra det användarnamn och lösenord administratör](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för kluster för virtuella datorer

