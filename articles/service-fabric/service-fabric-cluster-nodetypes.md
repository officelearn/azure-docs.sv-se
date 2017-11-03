---
title: Azure Service Fabric-nodtyper och den virtuella datorn skala anger | Microsoft Docs
description: "Lär dig hur Azure Service Fabric-noden typer som relaterar till virtuella datorn anger och hur du kan fjärransluta till en skala ange instans eller den klusternod."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Skala anger Azure Service Fabric-nodtyper och den virtuella datorn
Skaluppsättningar för den virtuella datorn är en Azure compute-resurs. Du kan använda skaluppsättningar för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Ställ in en separat skaluppsättningen för varje nod som du definierar i ett Azure Service Fabric-kluster. Du kan oberoende skala varje nodtyp upp eller ned, har olika uppsättningar av portar är öppna och använda olika kapacitetsdata.

Följande bild visar ett kluster med två nodtyper heter FrontEnd och BackEnd. Varje nodtyp har fem noder.

![Ett kluster med två nodtyper][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mappa virtuella scale set instanser till noder
I föregående bild visas skala ange instanser start på instansen 0 och sedan öka med 1. Numreringen avspeglas i nod-namn. Noden BackEnd_0 är till exempel instans 0 för BackEnd-skaluppsättning. Den här viss skaluppsättning har fem instanser som heter BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 och BackEnd_4.

När du skalar upp en skalningsuppsättning skapas en ny instans. Nya scale set-instansnamnet är vanligtvis skaluppsättning namn plus nästa instansnummer. I vårt exempel är BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mappa scale set belastningsutjämnare till nodtyper och skala uppsättningar
Om du distribuerade klustret i Azure-portalen eller exempelmall för Azure Resource Manager, visas alla resurser i en resursgrupp. Du kan se belastningsutjämnare för varje scale set eller nod. Belastningsutjämnarens namn används följande format: **LB -&lt;namnet nodtypen&gt;**. LB-sfcluster4doc-0 är ett exempel som visas i följande bild:

![Resurser][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärranslutning till en virtuell dator scale set-instans eller en klusternod
Ställ in en separat skala för varje nodtyp som du definierade i ett kluster. Du kan skala nodtyperna oberoende uppåt eller nedåt. Du kan också använda olika VM SKU: er. Till skillnad från single instance VM scale set instanser inte sina egna virtuella IP-adresser. Detta kan vara en utmaning när du söker efter en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

Utför följande steg för att hitta en IP-adress och port som du kan använda för att fjärransluta till en specifik instans.

**Steg 1**: hämta inkommande NAT-regler för Remote Desktop Protocol (RDP) för att hitta den virtuella IP-adressen för nodtypen.

Först hämta inkommande NAT-regler värdena som har definierats som en del av resursdefinitionen för `Microsoft.Network/loadBalancers`.

Markera i Azure-portalen på sidan belastningen belastningsutjämnaren **inställningar** > **inkommande NAT-regler**. Detta ger dig IP-adress och port som du kan använda för att fjärransluta till den första skalan ange instans. 

![Belastningsutjämnare][LBBlade]

I följande bild, IP-adress och port är **104.42.106.156** och **3389**.

![NAT-regler][NATRules]

**Steg 2**: hitta porten som du kan använda för att fjärransluta till specifika scale set-instans eller nod.

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

**Steg 3**: fjärransluta till skalan set-instans.

Följande bild visar med anslutning till fjärrskrivbord för att ansluta till FrontEnd_1 scale set-instansen:

![Anslutning till fjärrskrivbord][RDP]

## <a name="change-the-rdp-port-range-values"></a>Ändra intervallvärden för RDP-port

### <a name="before-cluster-deployment"></a>Innan du Klusterdistribution
När du konfigurerar klustret med hjälp av en Resource Manager-mall anger intervallet i `inboundNatPools`.

Gå till resursdefinitionen för `Microsoft.Network/loadBalancers`. Hitta beskrivningen av `inboundNatPools`.  Ersätt den `frontendPortRangeStart` och `frontendPortRangeEnd` värden.

![inboundNatPools värden][InboundNatPools]

### <a name="after-cluster-deployment"></a>Efter distribution
Det är mer komplex ändring av omfångsvärden för RDP-porten när klustret har distribuerats. Säkerställ att du inte återvinna de virtuella datorerna genom för att ange nya värden med hjälp av Azure PowerShell. 

> [!NOTE]
> Se till att du har Azure PowerShell 1.0 eller senare installerat på datorn. Om du inte har Azure Powershell 1.0 eller senare, rekommenderar vi att du följer stegen som beskrivs i [hur du installerar och konfigurerar du Azure PowerShell.](/powershell/azure/overview)

1. Logga in på ditt Azure-konto. Om följande PowerShell-kommandot misslyckas kontrollerar du att du har installerat PowerShell korrekt.

    ```
    Login-AzureRmAccount
    ```

2. Få information om din belastningsutjämnare och för att se värdena för beskrivningen av `inboundNatPools`, kör följande kod:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Ange `frontendPortRangeEnd` och `frontendPortRangeStart` till de värden som du vill använda.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Ändra RDP-användarnamnet och lösenordet för noder

Utför följande steg om du vill ändra lösenordet för alla noder i en enskild nodtyp. De här ändringarna gäller för alla aktuella och framtida noder i skaluppsättning.

1. Öppna PowerShell som administratör. 
2. Kör följande kommandon för att logga in och välja din prenumeration för sessionen. Ändra den `SUBSCRIPTIONID` parameter till ditt prenumerations-ID. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Kör följande skript. Använd de relevanta `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME`, och `PASSWORD` värden. Den `USERNAME` och `PASSWORD` värden är de nya autentiseringsuppgifterna för att du använder i framtiden RDP-sessioner. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Nästa steg
* Finns det [översikt över funktionen ”distribuera överallt” och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig mer om den [Fabric-SDK och komma igång](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
