---
title: Fjärranslutning till en azure service fabric-klusternod
description: Lär dig hur du fjärransluter till en skalningsuppsättningsinstans (en klusternod för Service Fabric).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458327"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärranslutning till en skalningsuppsättningsinstans för virtuell dator eller en klusternod
I ett Service Fabric-kluster som körs i Azure skapar varje klusternodtyp som du definierar [en separat skala för en virtuell dator](service-fabric-cluster-nodetypes.md).  Du kan fjärransluta till specifika skalentuppsättningsinstanser (klusternoder).  Till skillnad från virtuella datorer med en instans har skalningsuppsättningsinstanser inte sina egna virtuella IP-adresser. Detta kan vara en utmaning när du letar efter en IP-adress och port som du kan använda för att fjärransluta till en viss instans.

Om du vill hitta en IP-adress och port som du kan använda för att fjärransluta till en viss instans gör du följande steg.

1. Hämta de inkommande NAT-reglerna för RDP (Remote Desktop Protocol).

    Vanligtvis har varje nodtyp som definieras i klustret sin egen virtuella IP-adress och en dedikerad belastningsutjämnare. Som standard namnges belastningsutjämnaren för en nodtyp med följande format: *LB-{cluster-name}-{nod-type}*; till exempel *LB-mycluster-FrontEnd*. 
    
    På sidan för din belastningsutjämnare i Azure portal väljer du **Inställningar** > **Inkommande NAT-regler:** 

    ![Inkommande NAT-regler för belastningsutjämnare](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Följande skärmbild visar de inkommande NAT-reglerna för en nodtyp med namnet FrontEnd: 

    ![Inkommande NAT-regler för belastningsutjämnare](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    För varje nod visas IP-adressen i kolumnen **DESTINATION,** kolumnen **TARGET** ger skalningsuppsättningsinstansen och kolumnen **TJÄNST** anger portnumret. För fjärranslutning allokeras portar till varje nod i stigande ordning som börjar med port 3389.

    Du hittar också de inkommande NAT-reglerna i `Microsoft.Network/loadBalancers` avsnittet i Resource Manager-mallen för klustret.
    
2. Om du vill bekräfta den inkommande porten till målportmappning för en nod kan du klicka på dess regel och titta på **värdet för målporten.** Följande skärmbild visar den inkommande NAT-regeln för **FrontEnd-noden (instans 1)** i föregående steg. Observera att även om (inkommande) portnumret är 3390 mappas målporten till port 3389, porten för RDP-tjänsten på målet.  

    ![Mappning av målport](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Som standard är målporten port 3389 för Windows-kluster, som mappar till RDP-tjänsten på målnoden. För Linux-kluster är målporten port 22, som mappar till tjänsten Secure Shell (SSH).

3. Fjärranslutning till den specifika noden (skalningsuppsättningsinstans). Du kan använda användarnamnet och lösenordet som du angav när du skapade klustret eller andra autentiseringsuppgifter som du har konfigurerat. 

    Följande skärmbild visar hur du använder anslutning till fjärrskrivbord för att ansluta till **frontend-noden (instans 1)** i ett Windows-kluster:
    
    ![Anslutning till fjärrskrivbord](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    På Linux-noder kan du ansluta till SSH (i följande exempel återanvänder samma IP-adress och port för korthet):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Nästa steg finns i följande artiklar:
* Se [översikten över funktionen "Distribuera var som helst" och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [klustersäkerhet](service-fabric-cluster-security.md).
* [Uppdatera RDP-portintervallvärdena](./scripts/service-fabric-powershell-change-rdp-port-range.md) på kluster-virtuella datorer efter distribution
* [Ändra administratörsanvändarnamn och lösenord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella kluster-datorer

