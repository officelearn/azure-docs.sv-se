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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 68e3b8ae5bdaa3ad9f1c470294ef5c3bcf0c1893
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärranslutning till en virtuell dator scale set-instans eller en klusternod
I ett Service Fabric-kluster som körs i Azure, varje nodtyp i klustret som du definierar [ställer in en separat virtuell skala](service-fabric-cluster-nodetypes.md).  Du kan ansluta fjärr till skalan uppsättning instanser (eller noder i klustret).  Till skillnad från single instance VM scale set instanser inte sina egna virtuella IP-adresser. Detta kan vara en utmaning när du söker efter en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

Utför följande steg för att hitta en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

1. Hämta inkommande NAT-regler för Remote Desktop Protocol (RDP) för att hitta den virtuella IP-adressen för nodtypen.

    Först hämta inkommande NAT-regler värdena som har definierats som en del av resursdefinitionen för `Microsoft.Network/loadBalancers`.
    
    Markera i Azure-portalen på sidan belastningen belastningsutjämnaren **inställningar** > **inkommande NAT-regler**. Detta ger dig IP-adress och port som du kan använda för att fjärransluta till den första skalan ange instans. 
    
    ![Belastningsutjämnare][LBBlade]
    
    I följande bild, IP-adress och port är **104.42.106.156** och **3389**.
    
    ![NAT-regler][NATRules]

2. Hitta den port som du kan använda för att fjärransluta till specifika scale set-instans eller nod.

    Skaluppsättning för instanser karta till noder. Använd skalan information för att fastställa exakt porten som ska användas.
    
    Portar som tilldelas i stigande ordning som matchar scale set-instans. För det tidigare exemplet av nodtyp klientdel visas i följande tabell portarna för varje instans fem noder. Tillämpa samma mappning till din scale set-instans.
    
    | **Instans för skaluppsättning för virtuell dator** | **Port** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Fjärransluta till skalan set-instans.

    Följande bild visar med anslutning till fjärrskrivbord för att ansluta till FrontEnd_1 scale set-instansen:
    
    ![Anslutning till fjärrskrivbord][RDP]


Läs följande artiklar för nästa steg:
* Finns det [översikt över funktionen ”distribuera överallt” och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Uppdatera RDP-porten intervallvärden](./scripts/service-fabric-powershell-change-rdp-port-range.md) i klustret virtuella datorer efter distribution
* [Ändra det användarnamn och lösenord administratör](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för kluster för virtuella datorer

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
