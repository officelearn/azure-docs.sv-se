---
title: Tillgänglighet för SQL Server grupperar - virtuella datorer i Azure - katastrofåterställning | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar en SQL Server-tillgänglighetsgrupp på virtuella Azure-datorer med en replik i en annan region.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 542505c5a6c3af91669ebe28287ae6e1477e214d
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487184"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Konfigurera en Always On-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner

Den här artikeln förklarar hur du konfigurerar en SQL Server Always On tillgänglighetsgruppsrepliker på Azure virtuella datorer i en annan Azure-plats. Använd den här konfigurationen för att ge stöd för haveriberedskap.

Den här artikeln gäller Resource Manager-läge till Azure Virtual Machines.

Följande bild visar en gemensam distribution i en tillgänglighetsgrupp på virtuella Azure-datorer:

   ![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

I den här distributionen finns alla virtuella datorer i en Azure-region. Tillgänglighetsgruppsrepliker kan ha synkront genomförande med automatisk redundans på SQL-1 och SQL-2. Om du vill skapa den här arkitekturen, se [Availability Group-mall eller självstudien](virtual-machines-windows-portal-sql-availability-group-overview.md).

Den här arkitekturen är sårbar för driftstopp om Azure-regionen blir otillgänglig. Lägg till en replik i en annan Azure-region för att lösa problemet. Följande diagram visar hur den nya arkitekturen ser ut:

   ![Tillgänglighet grupp DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

I föregående diagram visas en ny virtuell dator som kallas SQL-3. SQL-3 är i en annan Azure-region. SQL-3 har lagts till i Windows Server-redundanskluster. SQL-3 kan vara värd för en tillgänglighetsreplik för gruppen. Observera att Azure-region för SQL-3 har en ny Azure-belastningsutjämnare.

>[!NOTE]
> En Azure-tillgänglighetsuppsättning är obligatorisk när fler än en virtuell dator är i samma region. Om det bara en virtuell dator är i regionen, och sedan tillgänglighetsuppsättningen inte är obligatoriskt. Du kan bara placera en virtuell dator i en tillgänglighetsuppsättning vid tidpunkten för skapandet. Om den virtuella datorn är redan i en tillgänglighetsuppsättning, kan du lägga till en virtuell dator för ytterligare en replik senare.

I den här arkitekturen konfigureras normalt repliken i avlägsen region med tillgänglighetsläget för asynkront genomförande av tillgänglighet och manuellt redundansläge.

När tillgänglighetsgrupprepliker är på Azure virtuella datorer i olika Azure-regioner, kräver varje region:

* En virtuell nätverksgateway
* En gateway för virtuell nätverksanslutning

Följande diagram visar hur nätverken kommunicera mellan datacenter.

   ![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Den här arkitekturen är fri från kostnaderna för utgående för data som replikeras mellan Azure-regioner. Se [bandbredd priser](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Skapa fjärransluten replik

Om du vill skapa en replik i en fjärransluten datacenter, gör du följande:

1. [Skapa ett virtuellt nätverk i det nya området](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurera en VNet-till-VNet-anslutning med Azure-portalen](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >I vissa fall kan behöva du använda PowerShell för att skapa VNet-till-VNet-anslutning. Om du använder olika Azure-konton kan du exempelvis konfigurera anslutningen i portalen. I det här fallet visas [konfigurera en VNet-till-VNet-anslutning med Azure-portalen](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Skapa en domänkontrollant i den nya regionen](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Den här domänkontrollanten ger autentisering om domänkontrollanten på den primära platsen inte är tillgänglig.

1. [Skapa en SQL Server-dator i det nya området](virtual-machines-windows-portal-sql-server-provision.md).

1. [Skapa en Azure belastningsutjämnare i nätverket på det nya området](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Den här belastningsutjämnaren måste:

   - Finnas i samma nätverk och undernät som den nya virtuella datorn.
   - Ha en statisk IP-adress för tillgänglighetsgruppens lyssnare.
   - Inkludera en serverdelspool som består av virtuella datorer i samma region som belastningsutjämnaren.
   - Använd en TCP-port avsökning specifika IP-adressen.
   - Ha en regel som är specifika för SQL Server i samma region för belastningsutjämning.  
   - Vara en Standardbelastningsutjämnare om de virtuella datorerna i serverdelspoolen inte är en del av en enskild tillgänglighetsuppsättning eller skalningsuppsättning för virtuell dator. För ytterligare information granskning [Azure Load Balancer Standard översikt](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Lägga till redundansklusterfunktionen till den nya SQL-servern](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Anslut den nya SQL-servern till domänen](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Ange det nya tjänstkontot för SQL Server att använda ett domänkonto](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Lägg till den nya SQL Server i Windows Server-redundanskluster](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Skapa en IP-adressresurs i klustret.

   Du kan skapa IP-adressresurs i hanteraren för redundanskluster. Högerklicka på rollen tillgänglighet för gruppen, klicka på **Lägg till resurs**, **mer resurser**, och klicka på **IP-adress**.

   ![Skapa IP-adress](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Konfigurera den här IP-adress enligt följande:

   - Använda nätverket från den fjärranslutna datacentralen.
   - Tilldela IP-adress från den nya Azure-belastningsutjämnaren. 

1. På den nya SQL-servern i SQL Server Configuration Manager [aktivera Always On Tillgänglighetsgrupper](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Öppna portar i brandväggen på den nya SQL-servern](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   De portnummer som du behöver öppna beror på din miljö. Öppna portar för databasspegling slutpunkt och Azure att läsa in hälsoavsökning för belastningsutjämnaren.

1. [Lägg till en replik i tillgänglighetsgruppen på den nya SQL-servern](https://msdn.microsoft.com/library/hh213239.aspx).

   Ställa in den för asynkron replikering med manuell redundans för en replik i en fjärransluten Azure-region.  

1. Lägg till IP-adressresursen som ett beroende för lyssnare klienten åtkomst punkt (nätverksnamn) klustret.

   I följande skärmbild visas en korrekt konfigurerad klusterresurs för IP-adress:

   ![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Klusterresursgruppen innehåller både IP-adresser. Båda IP-adresser finns beroenden för den lyssnare klientåtkomstpunkten. Använd den **eller** operator i beroende klusterkonfigurationen.

1. [Ange Klusterparametrar i PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Kör PowerShell-skript med klustrets nätverksnamn, IP-adress och avsökningsporten som du konfigurerade i belastningsutjämnaren i det nya området.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Set-anslutning för flera undernät

Repliken i den fjärranslutna datacentralen är en del av tillgänglighetsgruppen men det är i ett annat undernät. Om den här repliken blir den primära repliken, kan det uppstå programmet anslutnings-timeout. Det här beteendet är samma som en lokal tillgänglighetsgrupp i en distribution med flera undernät. För att tillåta anslutningar från klienten program, uppdatera klientanslutningen eller konfigurera namnmatchning för cachelagring på klustret nätverksnamnresursen.

Helst uppdatera anslutningssträngarna för klienten att ställa in `MultiSubnetFailover=Yes`. Se [ansluta med MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Om du inte kan ändra anslutningssträngar, kan du konfigurera name resolution cachelagring. Se [timeout i flera undernät tillgänglighetsgrupp](https://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Växla över till avlägsen region

Om du vill testa lyssnare anslutningen till den fjärranslutna regionen, kan du växla över repliken till den fjärranslutna regionen. Repliken är asynkron, är växling vid fel sårbar för potentiell dataförlust. Om du vill redundansväxla utan dataförlust, ändra tillgänglighetsläget till synkront och ange redundansläget till automatisk. Använd följande steg:

1. I **Object Explorer**, Anslut till instansen av SQL Server som är värd för den primära repliken.
1. Under **AlwaysOn Availability Groups**, **Tillgänglighetsgrupper**, högerklicka på tillgänglighetsgruppen och klickar på **egenskaper**.
1. På den **Allmänt** sidan under **Tillgänglighetsrepliker**, ange den sekundära repliken i DR-plats att använda **synkron genomför** tillgänglighetsläget och  **Automatisk** redundansläge.
1. Om du har en sekundär replik i samma plats som den primära repliken för hög tillgänglighet anger du den här repliken till **asynkrona genomför** och **manuell**.
1. Klicka på OK.
1. I **Object Explorer**, högerklicka på tillgänglighetsgruppen och på **visa instrumentpanelen**.
1. Kontrollera att replikeringen på DR-plats har synkroniserats på instrumentpanelen.
1. I **Object Explorer**, högerklicka på tillgänglighetsgruppen och på **växling vid fel...** . SQL Server Management Studios öppnar en guide för att växla över SQL Server.  
1. Klicka på **nästa**, och välj SQL Server-instansen i DR-plats. Klicka på **nästa** igen.
1. Ansluta till SQL Server-instans i DR-plats och klicka på **nästa**.
1. På den **sammanfattning** kontrollerar du inställningarna och klicka på **Slutför**.

När du testar anslutningen, flytta den primära repliken tillbaka till ditt primära datacenter och ange tillgänglighetsläget tillbaka till normal drift inställningarna. I följande tabell visas vanliga inställningar för den arkitektur som beskrivs i det här dokumentet:

| Plats | Server-instans | Roll | Läget för tillgänglighet | Redundansläge
| ----- | ----- | ----- | ----- | -----
| Primära Datacenter | SQL-1 | Primär | Synkron | Automatisk
| Primära Datacenter | SQL-2 | Sekundär | Synkron | Automatisk
| Sekundär eller fjärranslutna Datacenter | SQL-3 | Sekundär | Asynkron | Manuellt


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Mer information om planerat och framtvingad manuell redundans

Mer information finns i följande avsnitt:

- [Utför en planerad manuell Redundansväxling i en tillgänglighetsgrupp (SQLServer)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Utför en framtvingad manuell Redundansväxling i en tillgänglighetsgrupp (SQLServer)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Ytterligare länkar

* [Always On-Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure-belastningsutjämnare](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azures Tillgänglighetsuppsättningar](../manage-availability.md)
