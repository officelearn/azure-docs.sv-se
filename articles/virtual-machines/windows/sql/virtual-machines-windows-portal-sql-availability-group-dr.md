---
title: Konfigurera tillgänglighetsgrupp i olika regioner
description: I den här artikeln beskrivs hur du konfigurerar en SQL Server-tillgänglighetsgrupp på virtuella Azure-datorer med en replik i en annan region.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060119"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Konfigurera en tillgänglighetsgrupp på virtuella Azure SQL Server-datorer i olika regioner

I den här artikeln beskrivs hur du konfigurerar en SQL Server Always On availability group replica på virtuella Azure-datorer på en fjärrplats för Azure. Använd den här konfigurationen för att stödja haveriberedskap.

Den här artikeln gäller för virtuella Azure-datorer i resource manager-läge.

Följande avbildning visar en gemensam distribution av en tillgänglighetsgrupp på virtuella Azure-datorer:

   ![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

I den här distributionen finns alla virtuella datorer i en Azure-region. Tillgänglighetsgrupprepliker kan ha synkron commit med automatisk redundans på SQL-1 och SQL-2. Information om hur du skapar den här arkitekturen finns i [Mallen eller självstudien för tillgänglighetsgrupp](virtual-machines-windows-portal-sql-availability-group-overview.md).

Den här arkitekturen är sårbar för driftstopp om Azure-regionen blir otillgänglig. För att lösa det här säkerhetsproblemet lägger du till en replik i en annan Azure-region. Följande diagram visar hur den nya arkitekturen skulle se ut:

   ![Tillgänglighet Grupp DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

I föregående diagram visas en ny virtuell dator som heter SQL-3. SQL-3 finns i en annan Azure-region. SQL-3 läggs till i Redundansklustret för Windows Server. SQL-3 kan vara värd för en replik för tillgänglighetsgrupper. Observera slutligen att Azure-regionen för SQL-3 har en ny Azure-belastningsutjämnare.

>[!NOTE]
> En Azure-tillgänglighetsuppsättning krävs när mer än en virtuell dator finns i samma region. Om bara en virtuell dator finns i regionen krävs inte tillgänglighetsuppsättningen. Du kan bara placera en virtuell dator i en tillgänglighetsuppsättning vid skapande. Om den virtuella datorn redan finns i en tillgänglighetsuppsättning kan du lägga till en virtuell dator för ytterligare en replik senare.

I den här arkitekturen är repliken i fjärrregionen normalt konfigurerad med asynkront tillgänglighetsläge och manuellt redundansläge.

När repliker för tillgänglighetsgrupper finns på virtuella Azure-datorer i olika Azure-regioner kräver varje region:

* En virtuell nätverksgateway
* En anslutning till virtuell nätverksgateway

Följande diagram visar hur nätverken kommunicerar mellan datacenter.

   ![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Den här arkitekturen medför utgående dataavgifter för data som replikeras mellan Azure-regioner. Se [Bandbreddsprissättning](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Skapa fjärrreplik

Så här skapar du en replik i ett fjärrdatacenter:

1. [Skapa ett virtuellt nätverk i den nya regionen](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurera en VNet-till-VNet-anslutning med Azure-portalen](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >I vissa fall kan du behöva använda PowerShell för att skapa VNet-till-VNet-anslutningen. Om du till exempel använder olika Azure-konton kan du inte konfigurera anslutningen i portalen. I det här fallet se [konfigurera en VNet-till-VNet-anslutning med Azure-portalen](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Skapa en domänkontrollant i den nya regionen](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Den här domänkontrollanten tillhandahåller autentisering om domänkontrollanten på den primära platsen inte är tillgänglig.

1. [Skapa en virtuell SQL Server-dator i den nya regionen](virtual-machines-windows-portal-sql-server-provision.md).

1. [Skapa en Azure-belastningsutjämnare i nätverket i den nya regionen](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Denna belastningsutjämnare skall

   - Vara i samma nätverk och undernät som den nya virtuella datorn.
   - Ha en statisk IP-adress för tillgänglighetsgruppens lyssnare.
   - Inkludera en serverdpool som endast består av virtuella datorer i samma region som belastningsutjämnaren.
   - Använd en TCP-portavsökning som är specifik för IP-adressen.
   - Har en belastningsutjämningsregel som är specifik för SQL Server i samma region.  
   - Bli standardbelastningsutjämnare om de virtuella datorerna i serverdelspoolen inte ingår i en enda tillgänglighetsuppsättning eller en skalningsuppsättning för virtuella datorer. Mer information finns i [översikt över Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Lägg till funktionen För redundanskluster i den nya SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Gå med i den nya SQL Server till domänen](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Ange att det nya SQL Server-tjänstkontot ska använda ett domänkonto](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Lägg till den nya SQL Server i Windows Server Redundanskluster](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Lägg till en IP-adressresurs i klustret.

   Du kan skapa IP-adressresursen i Redundansklusterhanteraren. Markera namnet på klustret och högerklicka sedan på klusternamnet under **Klusterkärnresurser** och välj **Egenskaper:** 

   ![Klusteregenskaper](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   I dialogrutan **Egenskaper** väljer du **Lägg till** under **IP-adress**och lägger sedan till IP-adressen för klusternamnet från fjärrnätverksregionen. Välj **OK** i dialogrutan **IP-adress** och välj sedan **OK** igen i dialogrutan **Klusteregenskaper** för att spara den nya IP-adressen.. 

   ![Lägg till kluster-IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Lägg till IP-adressen som ett beroende för kärnklustrets namn.

   Öppna klusteregenskaperna en gång till och välj fliken **Beroenden.** Konfigurera ett ELLER-beroende för de två IP-adresserna: 

   ![Klusteregenskaper](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. Lägg till en IP-adressresurs i rollen tillgänglighetsgrupp i klustret. 

   Högerklicka på rollen tillgänglighetsgrupp i Redundansklusterhanteraren, välj **Lägg till resurs**, Fler **resurser**och välj **IP-adress**.

   ![Skapa IP-adress](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Konfigurera den här IP-adressen enligt följande:

   - Använd nätverket från fjärrdatacentret.
   - Tilldela IP-adressen från den nya Azure-belastningsutjämnaren. 

1. Lägg till IP-adressresursen som ett beroende för lyssnarens klientåtkomstpunkt (nätverksnamn) kluster.

   Följande skärmbild visar en korrekt konfigurerad IP-adressklusterresurs:

   ![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Klusterresursgruppen innehåller båda IP-adresserna. Båda IP-adresserna är beroenden för lyssnarens klientåtkomstpunkt. Använd **OPERATORN ELLER** i klusterberoendekonfigurationen.

1. [Ange klusterparametrar i PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Kör PowerShell-skriptet med klusternätverksnamn, IP-adress och avsökningsport som du har konfigurerat på belastningsutjämnaren i den nya regionen.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Aktivera [alltid på tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server)på den nya SQL Server i SQL Server Configuration Manager .

1. [Öppna brandväggsportar på den nya SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Vilka portnummer du behöver för att öppna beror på din miljö. Öppna portar för den speglingslutpunkt och Azure belastningsutjämnare hälsa sonden.


1. [Lägg till en replik i tillgänglighetsgruppen på den nya SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   För en replik i en fjärrregion Azure anger du den för asynkron replikering med manuell redundans.  

## <a name="set-connection-for-multiple-subnets"></a>Ange anslutning för flera undernät

Repliken i fjärrdatacentret är en del av tillgänglighetsgruppen, men den finns i ett annat undernät. Om den här repliken blir den primära repliken kan time-out för programanslutning uppstå. Det här beteendet är samma som en lokal tillgänglighetsgrupp i en distribution med flera undernät. Om du vill tillåta anslutningar från klientprogram uppdaterar du klientanslutningen eller konfigurerar cachelagring av namnmatchning på klusternätverksnamnresursen.

Uppdatera helst klientanslutningssträngarna `MultiSubnetFailover=Yes`så att de anger . Se [Ansluta med MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Om du inte kan ändra anslutningssträngarna kan du konfigurera cachelagring av namnmatchning. Se [Time-out-fel och du kan inte ansluta till en SQL Server 2012 AlwaysOn-tillgänglighetsgrupplyssnare i en miljö med flera undernät](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Växla över till fjärrregion

Om du vill testa lyssnaranslutningen till fjärrregionen kan du växla över repliken till fjärrregionen. Repliken är asynkron, men redundans är sårbar för potentiell dataförlust. Om du vill växla över utan dataförlust ändrar du tillgänglighetsläget till synkron och ställer in redundansläget till automatiskt. Använd följande steg:

1. Anslut till instansen av SQL Server som är värd för den primära repliken i **Object Explorer.**
1. Högerklicka på din tillgänglighetsgrupp under **Alltidpå tillgänglighetsgrupper**och klicka på **Egenskaper**. **Availability Groups**
1. På sidan **Allmänt,** under **Tillgänglighetsrepliker,** anger du att den sekundära repliken på DR-platsen ska använda tillgänglighetsläge för **synkron commit** och **automatiskt** redundansläge.
1. Om du har en sekundär replik på samma plats som den primära repliken för hög tillgänglighet ställer du in repliken på **Asynkron commit** och **manuell**.
1. Klicka på OK.
1. Högerklicka på tillgänglighetsgruppen i **Objektutforskaren**och klicka på **Visa instrumentpanel**.
1. Kontrollera att repliken på DR-platsen är synkroniserad på instrumentpanelen.
1. Högerklicka på tillgänglighetsgruppen i **Objektutforskaren**och klicka på **Redundans...**. SQL Server Management Studios öppnar en guide för att växla över SQL Server.  
1. Klicka på **Nästa**och välj SQL Server-instansen på DR-platsen. Klicka på **Nästa** igen.
1. Anslut till SQL Server-instansen på DR-platsen och klicka på **Nästa**.
1. Kontrollera inställningarna på sidan **Sammanfattning** och klicka på **Slutför**.

När du har testat anslutningen flyttar du tillbaka den primära repliken till ditt primära datacenter och ställer in tillgänglighetsläget igen på sina normala driftsinställningar. I följande tabell visas de normala driftinställningarna för arkitekturen som beskrivs i det här dokumentet:

| Location | Serverinstans | Roll | Tillgänglighetsläge | Redundansläge
| ----- | ----- | ----- | ----- | -----
| Primärt datacenter | SQL-1 | Primär | Synkron | Automatisk
| Primärt datacenter | SQL-2 | Sekundär | Synkron | Automatisk
| Sekundärt datacenter eller fjärrdatacenter | SQL-3 | Sekundär | Asynkron | Manuell


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Mer information om planerad och påtvingad manuell redundans

Mer information finns i följande avsnitt:

- [Utföra en planerad manuell redundans för en tillgänglighetsgrupp (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Utföra en tvingad manuell redundans för en tillgänglighetsgrupp (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Ytterligare länkar

* [Alltid på tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx)
* [Virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure belastningsutjämnare](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azure-tillgänglighetsuppsättningar](../manage-availability.md)
